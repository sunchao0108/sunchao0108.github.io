---
title: 2024回顾笔记
thumbnail: /images/leaves.jpg
cover: /images/leaves.jpg
date: 2024-11-11 11:11:11
tags: 工作
---

公司年底就解散了，回想起来在这工作了有四年半了。 做一下简单下回顾，以免疏漏。

## 编译

* 使用增量编译：在编译时尽量只重新编译修改过的文件，而不是整个项目，可以节省大量的编译时间。
* 使用构建缓存：利用构建缓存工具如Gradle Build Cache或Buck等，可以加快编译速度。
* 使用多线程编译：在编译时使用多线程可以加快编译速度，可以在编译配置中设置并发编译数。
* 分离模块编译：将项目拆分成多个模块，分别进行编译，可以减少编译时间。
* Gradle版本升级，增加AS运行内存
* KSP代替kapt
* 删除无用依赖，调整仓库地址顺序，固定依赖版本
* 资源优化，使用webp或svg，debug构建时禁用图片无损压缩
* 禁用R文件传递
* 源码依赖改为远程依赖

## Unit Test

Gradle java-test-fixture提升UT效率比较好用，在之前的项目里，由于使用了gradle脚本的dependencySubstitution方式将本地的远程依赖改为源码依赖进行构建，会有一些冲突，Gradle脚本需要做一些修改才好用。

Mockk尽量避免使用，初始化时间太长，Junit5会并行跑UT，拖慢CI/CD下的速度。 

## 几个印象奇奇怪怪的组件

### 自定义图片加载decoder

Glide-AudioDecoder 支持Glide加载MP3文件的专辑图，很奇快官方既然支持了视频文件缩略图为啥不把音频文件的一起也支持了。

Coil-ScaledSvgDecoder 支持SVG图片以dp格式的原大小展示和无损缩放。Github上有人提过issue，呼声挺高的，希望官方能尽快支持dp格式的size吧。

### MarkdownText
compose中显示markdown文本内容，定制了几个有意思的内容。

一个是通过参数禁止特定的格式渲染，例如禁止OrderedList但不禁止bulletList。

另外一个是检测输入的文本中是否含有带link item的table，有的话需要更改markdown内部TextView的MovementMethod, 避免link无法点击的问题。还不能直接修改，会影响a11y。


##杂项心得
* 非主线程的资源占用也会导致UI卡顿
* 文字转语音服务可以通过官方TextToSpeechService自定义实现
* 弹窗卡顿可能是因为未及时拦截已消费的事件
* Android studio看不到AAR的源码问题不知道官方解决没，临时方案是更改一个Gradle属性或者更改studio的一个设置。
* UID为system的系统应用不能访问外置存储。







