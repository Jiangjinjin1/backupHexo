---
title: eslint与prettier与husky配置
top: 100
copyright: true
date: 2022-06-18 16:42:26
tags:
- 项目框架配置
categories:
- 项目框架配置
- eslint配置
---



# 前言

`eslint它规范的是代码偏向语法层面上的风格。`本篇文章以一个基于react+ts项目，来说明eslint+prettier+husky+lint-staged配置项目代码规范, 可以搭配vscode中的eslint插件，我就不做介绍了。

<!--more-->

# 一、eslint

## 1、安装在项目中安装eslint
```shell
yard add eslint -D
```

## 2、创建eslint配置文件

```shell
npx eslint —-init

```
**获得一个.eslintrc.json文件,根据终端提示，选择项目框架类型，本文是以react+ts，按照提示选择，并生成.eslintrc.json文件**

<img src="/images/projectConfigure/project_configure_01.png" alt=".eslintrc.json" title=.eslintrc.json配置内容"">

## 3、创建.eslintignore文件

> 针对项目结构，定义需要检测的目录，配置.eslintignproore忽略检测的文件目录

<img src="/images/projectConfigure/project_configure_02.png" alt=".eslintignore" title=".eslintignore配置内容">

## 4、总结：
+ __我们为什么需要eslint?__
因为每个人的代码习惯不一样，每个开发人员都需要和他人协作或者项目需要交接给下一代开发者。 保持统一的代码规范，可以极大地提高效率，降低沟通和理解代码的成本。


# 二、prettier

## 1、安装prettier
安装：
```shell
yarn add prettier -D
```
> 在根目录创建.prettierrc，定义自己的规则

<img src="/images/projectConfigure/project_configure_03.png" title=".prettierrc配置内容">

## 2、创建.prettierignore文件
> 同eslint一样，prettier也需要对一些特定目录进行格式化，忽略不检查的目录

<img src="/images/projectConfigure/project_configure_04.png" title=".prettierignore文件">


## 3、prettier搭配eslint使用

> 让编译器能报prettier的错

安装：
```shell
yarn add eslint-plugin-prettier eslint-config-prettier -D
```
___
介绍：
> 1、`eslint-config-prettier为了解决eslint和prettier的冲突问题。`
> 2、eslint-plugin-prettier搭配eslint-config-prettier使用，通过eslint-plugin-prettier插件，配置rules中开启"prettier/prettier": "error”规则。
> 3、在extends中加入 "plugin:prettier/recommended”，这样就成功配置prettier插入eslint规则了。

完整配置如下：
```json
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended", // 禁用插件中与 Prettier 冲突的规则
        "plugin:prettier/recommended" // eslint-plugin-prettier搭配eslint-config-prettier使用简写形式
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint"
    ],
    "rules": {
        "prettier/prettier": "warn", // 开启prettier规则
        "@typescript-eslint/ban-ts-comment": "off",
        "eqeqeq": 2, //必须使用全等
        "no-debugger": "warn",
        "react/display-name": 0, // 防止React组件定义中缺少displayName
        "react/no-string-refs": "warn", // 防止字符串定义引用和防止引用this.refs
        "react-hooks/exhaustive-deps": 0,
        "no-unused-vars": 0, // 禁止出现未使用过的变量
        "react/prop-types": 1, // 防止React组件定义中缺少属性验证
        "indent": 0 // 强制使用一致的缩进
    }
}

```

## 4、项目中import会很多行，且混在一起，很难区分外部、三方、内部包引入
> 你可能认识到这里的问题。很难区分什么是所有的第三方和本地（内部）导入。它们没有被分组，似乎到处都是。

解决方案：
安装：
```shell
yarn add eslint-plugin-import -D
```

完整配置如下：
```json
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended", // 禁用插件中与 Prettier 冲突的规则
        "plugin:prettier/recommended" // eslint-plugin-prettier搭配eslint-config-prettier使用简写形式
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint",
        "import" // eslint-plugin-import配置
    ],
    "rules": {
        "prettier/prettier": "warn", // 开启prettier规则
        "@typescript-eslint/ban-ts-comment": "off",
        "eqeqeq": 2, //必须使用全等
        "no-debugger": "warn",
        "react/display-name": 0, // 防止React组件定义中缺少displayName
        "react/no-string-refs": "warn", // 防止字符串定义引用和防止引用this.refs
        "react-hooks/exhaustive-deps": 0,
        "no-unused-vars": 0, // 禁止出现未使用过的变量
        "react/prop-types": 1, // 防止React组件定义中缺少属性验证
        "indent": 0, // 强制使用一致的缩进
        "import/order": [ // eslint-plugin-import 规则配置
            "error",
            {
                "groups": ["builtin", "external", "internal", ["parent", "sibling"]],
                "pathGroups": [
                    {
                        "pattern": "react",
                        "group": "external",
                        "position": "before"
                    }
                ],
                "pathGroupsExcludedImportTypes": ["react"],
                "newlines-between": "always",
                "alphabetize": {
                    "order": "asc",
                    "caseInsensitive": true
                }
            }
        ]
    }
}

```

<img src="/images/projectConfigure/project_configure_09.png" title="改善后的效果">

参考：[eslint-plugin-import配置](https://dev.to/otamnitram/sorting-your-imports-correctly-in-react-213m)

## 5、总结
`这里需要再强调一点，这个extends数组中的规则，后面的会覆盖前面的，也就是plugin:react/recommended会覆盖掉recommended中的重复部分。`
并且这里的规则是由安装依赖引入的，存放在node_modules文件夹中，也就是为了保证其他开发人员代码一致，这里面的文件是不允许改动的。
所以说eslint和prettier的冲突问题，其实说的是这些依赖引入的规则和prettier的冲突！

# husky的使用
安装：
```shell
yarn add husky -D
```
用法：
```shell
npm set-script prepare "husky install"
npm run prepare
```

Add a hook:
```shell
npx husky add .husky/pre-commit "npx lint-staged"
git add .husky/pre-commit
```
会在.husky目录生成一个pre-commit文件

<img src="/images/projectConfigure/project_configure_06.png" title=".husky目录结构">

在package.json中配置husky

<img src="/images/projectConfigure/project_configure_07.png" title="package.json中配置husky">

# lint-staged使用
**考虑要使用lint-staged**
`如果这是一个新项目以上的就已经满足要求了，但是如果拿到的项目是一个老项目呢，别人开发了很久，这个时候加入再加入 eslint 规则，全局去检查，会发现一堆报错信息。这个就慌了。修改可能带来其他问题。`
这时候就需要用到lint-staged缓存区，只有在缓存区的文件做eslint检测。

> 为了解决这种问题，我们就需要引入 lint-staged

在package.json中增加配置
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "npx lint-staged"
    }
  },
  "lint-staged": {
    "src/**/*.{js,ts,tsx}": [
      "prettier --write",
      "eslint --fix"
    ]
  }
}
  
```

到这一步，配置基本就完成了。开始提交测试吧。
根据项目需要，自己定制eslint中的rules和prettier规则。


























