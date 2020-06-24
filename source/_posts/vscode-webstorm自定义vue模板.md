---
title: vscode/webstorm自定义vue模板
type: categories
copyright: true
date: 2020-05-14 11:40:33
tags: [webstorm, vscode, vue, 配置]
categories: 前端笔记
description: 在进行vue项目时，我们需要对编译器进一步配置来支持vue文件以及相关模板语法的高亮显示等。本文列出关于webstorm和vscode两种常用编译器的模板语法配置。
---

### webstorm
#### 1. 打开设置
打开设置：
`File => Settings`

#### 2. 安装vue插件
在搜索区输入`plugins`，或者找到plugins选项，右边的`Marketplace`搜索`vue`，安装。
![安装插件](/images/posts/webstorm_1.png '安装插件')

#### 3. 自定义模板
有时候我们新建vue文件时默认的模板不是我们想要的，这时可以自定义模板：
还是在设置中，搜索框输入`file and code template`或者在设置中找到该选项也行，在右侧选择vue模板，然后在编辑区修改模板即可。如图
![修改vue模板](/images/posts/webstorm_2.png '修改vue模板')

#### 4. 参考
给出一份我自己的作为参考，可按自己常用规则优化修改。
其中，`${COMPONENT_NAME}`直接获取组件名。
```template
<template>
    <div>
        
    </div>
</template>

<script>
    export default {
        name: "${COMPONENT_NAME}",
        props: [],
        components: {

        },
        data () {
            return {

            }
        },
        methods: {

        },
        mounted () {

        }
    }
</script>

<style type="text/scss" lang="scss" scoped>

</style>
```

-----

### vscode
#### 1. 安装Vetur
在应用市场中安装`Vetur`
#### 2. 使用
此时，在vue项目中新建`.vue`文件，然后输入`vue`时，按`tab`键则会出来一份默认的模板。由于默认模板涵盖内容过少，因此我们仍需要对齐进行自定义模板配置。
#### 3. 自定义模板
1. 打开 `文件 => 首选项 => 用户片段`
2. 搜索关键词`vue`
3. 在候选列表中选择`vue.json`
4. 在打开的文件中自定义配置即可

*如果没有找到`vue.json`，可以重启一下vscode试一下。*

#### 4. 参考
给出一份参考
```json
{
    "Print to console": {
        "prefix": "vue",
        "body": [
            "<template>",
            "    <div>\n",
            "    </div>",
            "</template>\n",
            "<script>",
            "export default {",
            "    props: {\n",
			"    },",
            "    components: {\n",
            "    },",
            "    data() {",
            "        return {\n",
            "        };",
            "    },",
            "    watch: {\n",
            "    },",
            "    methods: {\n",
            "    },",
            "    mounted() {\n",
            "    },",

            "};",
            "</script>\n",
            "<style scoped lang=\"${1:scss}\" type=\"text/scss\">\n",
            "</style>\n",
        ],
        "description": "Create vue template"
    }
}
```

