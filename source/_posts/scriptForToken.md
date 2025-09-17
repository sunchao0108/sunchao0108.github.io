---
title: Figma token转换脚本
date: 2025-08-16 21:33:18
tags: 工作
---

最近工作中遇到一个需求，要使用脚本将Figma design token转换成代码，记录一下实现脚本的思路。
<!-- more -->

Figma的design token有不同的工具，但是最终输出的都是json格式的文件，开发需要读取json文件内容转换为Android对应的xml或者Compose代码。
Figma本身有一个token code插件，仅支持Android xml，不支持compose。并且功能残废，放弃使用。

### 1. token studio
token studio支持token导出，设计师在token studio中点击同步后，令牌json文件会自动提交到代码仓库，便于开发同步和版本管理。
调研了第三方脚本工具Style Dictionary支持将token转换为xml或者Compose代码：https://styledictionary.com/getting-started/
简单记录下使用方法：
安装依赖工具：
* 安装 Node.js（含 npm，建议 v16+）
* 全局安装 Style Dictionary
npm install -g style-dictionary 

项目示例配置文件结构：
```
   android-token-converter/
   ├── tokens/              # 从 GitHub 同步的令牌 JSON 文件
   │   ├── global.json
   │   ├── light.json
   │   └── dark.json
   ├── config.json          # Style Dictionary 配置文件
   ├── build.js             # 自定义转换逻辑（可选）
   └── package.json         # 项目依赖配置
```
配置转换规则（config.json）
1. 配置config.json 定义 Style Dictionary 如何处理令牌并生成目标代码
2. 自定义转换逻辑（可选，build.js），对于复杂场景（如生成自定义属性 attr.xml 或主题样式），通过build.js扩展功能
3. 执行转换并集成到项目
4. 运行转换命令

使用默认配置
style-dictionary build

若使用自定义 build.js
node build.js

总结：插件本身基础功能可用，使用JS语言，对该语言不是很熟悉，对字体，Compose支持不完整，需要大量的自定义扩展，使用起来有些麻烦。

### 2. token manager
合作方使用的是另一个工具token manager，同样是导出json文件，但是没有版本控制功能，并且导出的格式与token studio不同，没有三方脚本支持。
因此需要项目自己定制脚本。

### 使用kotlin制作token转换脚本
将脚本和.kt文件组织为标准Gradle项目，通过build.gradle.kts管理依赖，对Android开发相当的友好，几乎没有学习曲线，可以当作一个module来看。

1. 在build.gradle中添加插件 java-library和org.jetbrains.kotlin.jvm 
2. 然后添加依赖，只需要两个就可以gson和kotlinpoet。 
3. 添加resources文件夹用于存放token的json文件。
4. main函数作为入口，使用classLoader.getResource方法读取文件。
5. 用gson将读取到的内容转换成对应的data class或map。
6. 使用kotlinpoet将转换的数据生成对应的Compose代码。

因为是项目上的代码，实现就不记录了，记录一下思路即可。自己写脚本的优点就是可以根据项目定制，第三方的为了通用性使用上总会有许多限制。
有了这次的经验，学会了写脚本真的很方便，以后很多东西都可以借鉴。

