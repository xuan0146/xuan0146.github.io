---
title: vue中引入并使用markdown编辑器
type: categories
copyright: true
date: 2019-05-23 19:45:17
tags: [vue, markdown]
categories: 前端笔记
description:
---
### 效果
![markdown组件示例](/images/posts/markdown示例.png)

<!--more-->

### 开篇
&emsp;&emsp;不知道各位是否对`markdown`熟悉。它算是一门标记语言，但是语法简单，却本领强大。例如本篇博客，便是用`markdown`语法编写，然后根据不同的环境及依赖生成各种我想要的样式，比如直接生成本篇博客样式或者使用一些依赖或者工具赋之更加美观的样式。同样，一般在**CMS**后台管理系统中，也经常会用到编辑器来编写一些文章材料。其中最常用的还是`markdown编辑器`和`富文本编辑器`。

### Markdown 介绍
***1. 什么是Markdown***
&emsp;&emsp;首先，各位框架玩家一定不会陌生，因为无论是在各类框架下还是开源项目中，都会有一个`.md`的文件，该文件一般是项目说明文件，不仅语法简洁，而且所生成的“页面”也要比普通标记语言更加整洁明了。那么它到底是什么呢？
&emsp;&emsp;**Markdown是一种可以使用普通文本编辑器编写的标记语言**，通过简单的标记语法，它可以使普通文本内容具有一定的格式。
&emsp;&emsp;Markdown具有一系列衍生版本，用于扩展Markdown的功能（如表格、脚注、内嵌HTML等等），这些功能原初的Markdown尚不具备，它们能让Markdown转换成更多的格式，例如LaTeX，Docbook。Markdown增强版中比较有名的有Markdown Extra、MultiMarkdown、 Maruku等。这些衍生版本要么基于工具，如Pandoc；要么基于网站，如GitHub和Wikipedia，在语法上基本兼容，但在一些语法和渲染效果上有改动。

***2. Markdown用途***
&emsp;&emsp;Markdown的语法简洁明了、学习容易，而且功能比纯文本更强，因此有很多人用它写博客。世界上最流行的博客平台WordPress和大型CMS如Joomla、Drupal都能很好的支持Markdown。完全采用Markdown编辑器的博客平台有Ghost和Typecho。
&emsp;&emsp;用于编写说明文档，并且以“README.md”的文件名保存在软件的目录下面。
除此之外，由于我们有了RStudio这样的神级编辑器，我们还可以快速将Markdown转化为演讲PPT、Word产品文档、LaTex论文甚至是用非常少量的代码完成最小可用原型。在数据科学领域，Markdown已经广泛使用，极大地推进了动态可重复性研究的历史进程。

### vue 中如何使用

*注： 本示例选用的是`mavon-editor`*
**很明显，所有组件逃不过三步走原则：**
>1. 安装
2. 引入、配置
3. 使用

#### 1. 安装
安装mavon-editor:`npm install mavon-editor --save`

#### 2. 引入、配置
在要使用markdown编辑器的组件内操作：
```
<script>
// 导入组件 及 组件样式
import { mavonEditor } from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'
</script>
```

#### 3. 使用
>注意：
1. 绑定model
2. 实时改变：将`markdown`实时转化为`html`

在要使用markdown编辑器的组件内操作:
```
<template>
	<div>
		<mavon-editor 
			v-model="content" 
			ref="md" 
			@change="change" 
			style="min-height: 600px"
		/>

		<button @click="submit">提交</button>
	</div>
</template>



<script>
export default {
	// 注册
	components: {
		mavonEditor,
	},
	data() {
		return {
			content:'',	// 输入的markdown
			html:'',	// 及时转的html
		}
	},
	methods: {
		// 所有操作都会被解析重新渲染
		change(value, render){
			// render 为 markdown 解析后的结果[html]
			this.html = render;
		},
		// 提交
		submit(){
			console.log(this.content);
			console.log(this.html);
		}
	},
	mounted() {

	}
}
</script>
```

### 问题
那么，在常用的操作里面，例如各级标题、表格、加粗加斜、标记、图文……图？此时，我们发现一个问题：如何添加本地图片呢？
#### Q

***在某些IDE里面使用markdown时，可以自由的插入本地图片，那我们在页面中使用此编译器组件如何添加图片呢？***
#### A
**监听。**没错，就是监听输入框变化。如果监听到有图片插入，那么我们可以先将图片上传至服务器，然后获取到线上url，拿到该url再插入到该位置。那么具体过程就很明显了：
1. 选择本地图片，插入
2. 监听到有图片插入
3. 将该图上传至服务器
4. 获取到服务器返回的图片url
5. 将该线上url冬天插入到输入框中

#### 代码如下
```
<template>
    <div class="markdown">
        <div class="container">
            <mavon-editor v-model="content" ref="md" @imgAdd="$imgAdd" @change="change" style="min-height: 600px"/>
            <button @click="submit">提交</button>
        </div>
    </div>
</template>

<script>
    import { mavonEditor } from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    export default {
        name: "",
        props: [],
        components: {
            mavonEditor,
        },
        data() {
            return {
                content:'',
                html:'',
                configs: {}
            }
        },
        methods: {
            // 将图片上传到服务器，返回地址替换到md中
            $imgAdd(pos, $file){
                let formdata = new FormData();

                this.$upload.post('/上传接口地址', formdata).then(res => {
                    console.log(res.data);
                    this.$refs.md.$img2Url(pos, res.data);
                }).catch(err => {
                    console.log(err)
                })
            },
            // 所有操作都会被解析重新渲染
            change(value, render){
                // render 为 markdown 解析后的结果[html]
                this.html = render;
            },
            // 提交
            submit(){
                console.log(this.content);
                console.log(this.html);
                this.$message.success('提交成功，已打印至控制台！');
            }
        },
        mounted() {

        }
    }
</script>

```

### 后
&emsp;&emsp;到这里，vue中使用markdown编辑器教程结束。那么更为大众广泛使用的`富文本编辑器`如何使用呢？且看下回~




