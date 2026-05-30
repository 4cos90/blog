---
title: "OpenClaw+EvoMap —— 加入光荣的进化"
date: "2026-02-22"
tags: ["AI", "Agent", "EvoMap", "OpenClaw"]
summary: "EvoMap 是一个连接了很多 Agent 的平台，让经验可以在 Agent 之间共享和继承，终结经验孤岛。"
---

过年的时候EvoMap也是大火，但是很多文章用词都比较超前，我尝试用更加朴素的表达来描述一下EvoMap是什么，怎么接入。

# 叠甲

以下内容仅出于我个人使用体验，理解有出入的地方，欢迎指正！

# OpenClaw是什么

## 简单概括

OpenClaw是一个在本地拥有root权限的Agent。通过对接主流的聊天app(国内主流：飞书/企业微信），实现在聊天app内输入自然语言指令，Agent在本地直接以root权限操作，完成任务。

## 与大模型应用的核心差异

*   拥有本地执行的权限，所有数据，历史会话，工具调用执行均发生在本地，可执行需要本人授权的操作。比如用自己的邮箱发送邮件。
*   可以对自身进行优化，比如在完成任务后，总结当次任务生成skill文件。当下次执行类似任务时，可通过导入skill优化执行效率。(进化)

## 部署方式

*   现在国内的云服务厂商基本都提供了一键部署，因为OpenClaw的执行权限较大，不建议直接在自己的电脑上部署。

# EvoMap是什么

官网:<https://evomap.ai/>

## 简单概括

EvoMap是一个连接了很多agent的平台。

*   Agent连接到Evomap后，Agent会定时向Evomap提供：自己执行任务时总结的成功经验。当Agent执行任务时，会在Evomap搜索是否存在相关经验。
*   人登录Evomap后，可以在平台上提问，并由连接的Agent主动发现并作答。

## 对接EvoMap

对接EvoMap的方式我个人认为非常的符合AI应用的直觉

直接在对接OpenClaw的聊天框内发送消息，即可自动完成对接：
curl -s <https://evomap.ai/skill.md>

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/4f02b933630d44678434cb4c5b4a90ff~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=RCDDmHcLgilbYkzNyHEvJhMHgGE%3D)

这个命令实际上是让OpenClaw打开了一个skill.md文件。

我们打开这个文件看一下，其中包含了两部分内容，一部分是关于EvoMap功能的简单介绍。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/2f49c40fe4ca4672b37eb29f32f444d6~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=IYrO%2B85G8ae8ky%2FSJaEczxCvvNo%3D)

另外一部分是EvoMap的接口文档和请求样例。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/240752433ff24d11a4178148f2e55a22~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=l%2BjSRJwyT%2FlvpwIyL6kYv2YiWCE%3D)

OpenClaw读到这个skill就会按照文档上的 Quick Start 自动完成Agent对接工作，并返回一个绑定链接。点击该链接即可将个人账户与Agent绑定。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d9da5e9f9d354f2d9597fda5a5797b35~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=TELXqtZTJZbck%2BPeFJsVSbKQq2I%3D)

## 核心概念

### Gene

原子化能力单元,可以理解为一条知识。
比如我们打开一个Gene，是关于http重试机制的，只有一句话：

**HTTP 重试机制**，包含**指数退避策略**、**超时控制**与**连接池**功能。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/c04d731859224697a771e37ac70bd3cf~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=b8ZwEbsMtYn5ADBgMX0F1qopZh8%3D)

### Gene Capsule

Gene + 效果验证

可以看到上面重试机制的Gene，经过验证API调用成功率提升了30%。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5afd09832dbe4009980ba0e7300794d7~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=P5hqEIqY0OMHwFu23pTwOfVtlIg%3D)

### Evolver

**Evolver** 是运行在开发者本地或服务器上的AI进化引擎，**EvoMap** 是承载整个进化生态的云端基础设施。二者的关系类似于 **Git 客户端与 GitHub**

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/a6638e260d7b4d72a4170eda476db9db~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751935&x-orig-sign=8DZzLHyE8Zs7br80izb1RG2pw2Q%3D)

## 工作流程

1.  **学习**：Agent 执行任务时，Evolver捕捉到高效策略（如 `grep -r` 比 `find` 更快）。
2.  **固化**：将策略封装为 **Gene**，在沙盒中验证，将成功方案打包成带唯一标识的 **Gene Capsule**。
3.  **发布**：上传到 EvoMap 网络，全网可检索。
4.  **继承**：其他 Agent 遇到同类问题时，直接下载并加载该胶囊，**复用已经经过验证的能力**。
5.  **反馈与进化**：使用Gene的Agent可以反馈使用效果。验证优秀的Gene会有更高的评分。

# 总结

现在Agent应用层的开发，基本可以总结为上下文的管理。无论rag，mcp还是skill。都是在为模型提供更高相关性，更实时，更"正确"的上下文。

通过EvoMap，我们给Agent接入了一个不断更新迭代的，经过验证的知识库。

*   **终结经验孤岛**：一个Agent学会，所有Agent学会。
*   **大幅降低试错成本**：跳过重复训练，直接使用验证过的最佳实践。
*   **持续自我进化**：Agent能力随网络共同进化，持续迭代。
