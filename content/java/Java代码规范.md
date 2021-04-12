---
title: "阿里巴巴Java开发手册与代码规约插件P3C-PMD"
date: 2019-12-24
tags: []
categories: [java]
draft: true
---

P-3C Orion【猎户座反潜巡逻机】，阿里大概取p3c先进，监测，发现潜在问题的意思

## 前言

2017年的时，阿里官方首次公开阿里Java代码规范标准，发布阿里巴巴Java开发手册。
这套Java统一规范标准将有助于提高行业编码规范化水平，帮助行业人员提高开发质量和效率、大大降低代码维护成本。 

目前已更迭了五个版本，2019年6月19日，阿里巴巴Java开发手册（华山版）》正式发布，该版本命名为华山版是因为最新版本手册集成了社区开发者集体智慧的结晶。

该开发手册阿里内部Java工程师所遵循的开发规范，涵盖编程规约、单元测试规约、异常日志规约、MySQL规约、工程规约、安全规约等，这是近万名阿里Java技术精英的经验总结，并经历了多次大规模一线实战检验及完善。这是阿里回馈给Java社区的一份礼物，希望能够帮助企业开发团队在Java开发上更高效、容错、有协作性，提高代码质量，降低项目维护成本。

虽然已经发布了开发手册，但是为了让开发者更加方便、快速的将规范推动并实行起来，阿里巴巴基于手册内容，研发了一套自动化的IDE检测插件（IDEA、Eclipse）， 该插件在扫描代码后，将不符合《手册》的代码按Blocker/Critical/Major三个等级显示在下方，甚至在IDEA上，还基于检查机制提供了实时检测功能，编写代码的同时也能快速发现问题所在。另外对于历史代码，部分规则实现了批量一键修复的功能，提升代码质量，提高团队研发效能。

## 如何安装
## eclipse安装
1.打开https://p3c.alibaba.com/plugin/eclipse/update
![Eclipse 插件下载图1](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/Eclipse_plugin_download1.png)
2.点击Zip File直接下载，下载完成后解压缩，将将【features】及【plugins】两个目录复制到eclipse安装目录下（eclipse.exe同级目录），重启eclipse
![Eclipse 插件下载图2](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/Eclipse_plugin_download2.png)

### idea安装
1.访问：https://plugins.jetbrains.com/plugin/10046-alibaba-java-coding-guidelines/versions
下载对应版本的安装包
![IDEA 插件下载图](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/IDEA_Plugins_Download.png)
2.依次点击【File】->【Settings】->【Plugins】->【install plugin from disk】然后点击确认

![扫描结果](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/Offline_Install_Steps.png)

## 如何使用
1.首先现贴一段测试代码
```java
public class Test {
    public static void main(String[] args) {
        String _name = "name";
        System.out.println(_name.equals("name"));
        Long temp = 10l;
        if(temp ==10L)
            System.out.println("temp = 10");
        else
            System.out.println("temp != 10");
        HashMap<String,Integer> map = new HashMap<>();
    }
}
```
![扫描结果](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/Selected_guide.png)
扫描后的结果：
![扫描结果](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/java/Java开发手册/Inspection_Result.png)


