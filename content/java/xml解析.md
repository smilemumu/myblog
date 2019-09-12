---
title: "xml与json互转"
date: 2019-09-11
tags: [Java,xml]
categories: [Java]
draft: true
---
**开发场景中，需要对返回的xml数据转为json再进行处理**

<!--more-->

## 1.使用org.json包来解析xml字符串
```java
import org.json.JSONException;
import org.json.XML;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;

import org.dom4j.*;

/**
 * xml to json
 */
public class JsonUtils {  
     public static String xml2jsonString(String xml)throws JSONException{  
     JSONObject xmlJSONObj = XML.toJSONObject(xml);  
     return xmlJSONObj.toString();  
     }  
}

/**
 * json to xml
 */
public static String json2xml(String json){
    String result  = "";
    try {
        org.json.JSONObject jsonObj = new org.json.JSONObject(json);
        result = "<xml>" + XML.toString(jsonObj) + "</xml>";
    } catch (JSONException e) {
        e.printStackTrace();
    }
    return result;
}
```

## 2.sax解析  
- [ ] 未完成事项

## 3.dom解析  
- [ ] 未完成事项
### dom4j方式xml to json
```java
	/**
     * xml转json
     */
    public static void dom4j2Json(Element element,JSONObject json){
        //如果是属性
        for(Object o:element.attributes()){
            Attribute attr=(Attribute)o;
            if(!isEmpty(attr.getValue())){
                json.put("@"+attr.getName(), attr.getValue());
            }
        }
        List<Element> chdEl=element.elements();
        if(chdEl.isEmpty()&&!isEmpty(element.getText())){//如果没有子元素,只有一个值
            json.put(element.getName(), element.getText());
        }

        for(Element e:chdEl){//有子元素
            if(!e.elements().isEmpty()){//子元素也有子元素
                JSONObject chdjson=new JSONObject();
                dom4j2Json(e,chdjson);
                Object o=json.get(e.getName());
                if(o!=null){
                    JSONArray jsona=null;
                    if(o instanceof JSONObject){//如果此元素已存在,则转为jsonArray
                        JSONObject jsono=(JSONObject)o;
                        json.remove(e.getName());
                        jsona=new JSONArray();
                        jsona.add(jsono);
                        jsona.add(chdjson);
                    }
                    if(o instanceof JSONArray){
                        jsona=(JSONArray)o;
                        jsona.add(chdjson);
                    }
                    json.put(e.getName(), jsona);
                }else{
                    if(!chdjson.isEmpty()){
                        json.put(e.getName(), chdjson);
                    }
                }


            }else{//子元素没有子元素
                for(Object o:element.attributes()){
                    Attribute attr=(Attribute)o;
                    if(!isEmpty(attr.getValue())){
                        json.put("@"+attr.getName(), attr.getValue());
                    }
                }
                if(!e.getText().isEmpty()){
                    json.put(e.getName(), e.getText());
                }
            }
        }
    }
    public static boolean isEmpty(String str) {

        if (str == null || str.trim().isEmpty() || "null".equals(str)) {
            return true;
        }
        return false;
    }


    /**
     * xml to json
     * @param xml
     * @return
     */
    public static String xml2json(String xml) {
        JSONObject json=new JSONObject();
        try {
            Document doc= DocumentHelper.parseText(xml);
            dom4j2Json(doc.getRootElement(),json);
        } catch (DocumentException e) {
            logger.error("xml2jso失败,参数：{}",xml);
        }
        return json.toString();
    }
```