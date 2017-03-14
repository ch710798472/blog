---
layout: post
title: CognitiveJ —— Java语言的图像分析库
category: java
modified: 2017-03-14
tags: [java]
comments: true
pinned: true
excerpt: "1.简介 CognitiveJ 是一个开源的，支持 Java 8 API 的库..."
---
### 1.简介
　　CognitiveJ 是一个开源的，支持 Java 8 API 的库，用于管理和编排 Java 应用和微软的 Cognitive  (Project Oxford) 机器学习和图像处理库的项目，可以让你查询以及分析图像。<br>
    它可以做到：
    
          人脸识别：
          
          人脸检测– 捕获脸部、性别、年龄等相关脸部特征以及图像的标志
          表情检测 – 根据图像中的脸部信息推断出表情状态
          验证 – 验证同一个人的两张不同表情的差异
          识别 – 根据已知的人里识别出某个人
          查找相似 —— 对人脸检测、分组以及排名以找出类似的人脸
          分组 – 基于脸部特征进行人的分组
          Person Group/Person/Face Lists; 创建、管理和训练分组、脸部和人列表用于识别、分组和查找相似的脸部特征
          
          视觉：
          图像描述 —— 描述图像的可视化内容并返回真实世界中的表述方式
          图像分析 —— 抽取图像中的关键信息，例如可判断图片是否包含色情性质
          OCR – 检测和提取图像中的文字
          缩略图 – 根据图像的关键点来创建缩略图
          
          图层 (体验阶段)：
          应用图像层到多个图像上，将发现的特征进行可视化展现
          在人脸和图像上使用字幕
          形象的描述脸部和视觉特征集
          对图像中的人脸进行像素化
          
          其他特性：
          支持本地和远程图像
          参数校验
　　
### 2.开始前的准备
   需要在MS官网申请API的两个key，申请流程以及过程参考这篇[文章](http://blog.sina.com.cn/s/blog_9d9dc1a10102xfqm.html)。

### 3.demo
开发工具：IDEA + maven<br>
添加pom依赖：

    <dependency>
        <groupId>cognitivej</groupId>
        <artifactId>cognitivej</artifactId>
        <version>0.6.2</version>
    </dependency>
    
编写main函数：

    public static void main(String[] args) {
        File file = new File("test/resources/aobama.jpg");
        FaceScenarios faceScenarios = new FaceScenarios("your first key write here",
            "your second key write here");
        //识别人脸
        ImageOverlayBuilder imageOverlayBuilder = ImageOverlayBuilder.builder(file);
        imageOverlayBuilder.outlineFacesOnImage(faceScenarios.findFaces(file), RectangleType.FULL,
            CognitiveJColourPalette.STRAWBERRY).launchViewer();

        //人脸特征标志信息
        Face faces = faceScenarios.findSingleFace(file);
        ImageOverlayBuilder.builder(file).outFaceLandmarksOnImage(faces).launchViewer();

    }
<br>图片使用的是百度百科的奥巴马介绍的照片
![图片1](https://github.com/ch710798472/blog/raw/gh-pages/img/aobama.jpg)
<br>运行结果：
![图片1](https://github.com/ch710798472/blog/raw/gh-pages/img/cognitivej1.png)


### 4.小结
CognitiveJ代码使用简介的令人难以执行，没有复杂的参数设置，没有复杂的预处理过程，也没有令人头晕目眩的使用文档。
<br>人们可以基于此快速实现自己的小需求小产品，不用过多关注内部细节，只有商业化之后再二次开发或者改写框架进行优化。
   
