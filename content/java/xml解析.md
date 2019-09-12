---
title: "xml解析"
date: 2019-09-11T20:39:16+08:00
draft: true
---
# **xml解析**

## 1.使用org.json包来解析xml字符串

	import org.json.JSONException;
	import org.json.JSONObject;
	import org.json.XML;
	public class JsonUtils {  
	     public static String xml2jsonString(String xml)throws JSONException{  
	     JSONObject xmlJSONObj = XML.toJSONObject(xml);  
	     return xmlJSONObj.toString();  
	     }  
	}

## 2.sax解析  
- [ ] 未完成事项

## 3.dom解析  
- [ ] 未完成事项