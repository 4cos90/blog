---
title: "为什么我认为 skill 不是一个很好的范式"
date: "2026-05-30"
tags: ["AI", "Agent", "Skill", "安全"]
summary: "Skill 设计让 Agent 自主权过大，存在运行时改环境、bash 权限难管控、过程黑盒三个根本问题。"
---

在用 AI Agent 干活的时候，我们很容易沉浸在「它能做什么」里，却忽略了一个更基本的问题：「它这样做安全吗？」

Skill 是现在主流的 Agent 扩展方式，概念很简单——给 Agent 装上各种技能包，让它能调用外部能力完成更复杂的任务。但是在过去一周里面我连续遇到了两个问题：

---

## 案例一：电脑莫名其妙多了个 .NET 环境

我工作中需要偶尔操作 Word 文档。

在没装 Skill 之前，Claude Code 遇到要创建 docx 的情况，会自己写个 Python 脚本创建文档——我本地有 Python 开发环境，没有问题。

装了 minimax-word 之后，同样的需求，调用了 Skill 用 C# 来创建文档。问题来了：我电脑上没有 .NET 开发环境。于是 Claude Code 主动执行了skill中的安装脚本。安装了 .NET SDK——并且成功用 C# 创建了文档。


![ScreenShot_2026-05-30_221921_279.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d999030cfaa64011833d019b23a3c36f~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=e9ecf3d6&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780237189&x-orig-sign=SK4wY4TObqy5OAQZJFMla4JgOQ8%3D)

![微信图片_20260530210621_264_244.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/fad3a706e79b457c85f3550c0be29c15~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=e9ecf3d6&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780236825&x-orig-sign=bq9tBYzcieRgNnGV6bCLipPuPV0%3D)

这里有两个让我不舒服的点：

**第一，原来的方案被降权了。** Python 脚本明明能工作，但因为 Skill 的存在，Agent 觉得「既然有这个 Skill 就用这个吧」。虽然原来的方案已经证实可行，但 Agent 选择了 Skill，而不是 Python 脚本。

**第二，Agent 在我不知情的情况下改了我的电脑。** 为了创建一次 Word 文档，安装了 .NET SDK。下次遇到类似需求，可能又装一套别的。几次下来，电脑里就乱成一锅粥了。这对于我来说情况是可控的——我看到日志里安装了 .NET SDK，等一下去卸载了就好了。但对于非专业用户，他的电脑上悄悄安装了一个 .NET SDK，他可能并不知道怎么处理。

---

## 案例二：邮件操作能不能封装成 Skill？

第二个案例是一次用户需求：用户用 ChatFlow 生成了一个静态 HTML 页面，想把它塞到 Outlook 邮件正文中（不是附件）。

当时使用了一段 PowerShell 脚本实现了这个功能。
于是我又想：要不把常见的邮件操作封装成一个 Skill——读取、搜索、发送、插入 HTML，功能很合理。

但我发现了一个问题：如果把 PowerShell 脚本封装进 Skill，那就得给 Claude Code 执行 shell 命令的权限。换句话说，它也可以生成这样一条命令：

```powershell
Remove-Item -Recurse -Force $HOME
```

用户的邮件、文档、所有东西，一行命令全删干净。

有人会说：那就不给通用 shell 权限，只开放固定的几个命令。但这里有个根本问题——你限制的是「命令叫什么名字」，而不是「命令能产生什么效果」。比如你允许 `git clone`，Agent 可以 `git clone` 一个恶意脚本再执行；你允许 `python`，Agent 可以用 Python 调用 `os.system()` 执行任意命令。**用预设的规则去对抗 Agent 的智能，规则迟早会被绕过去。**

最后我没做成 Skill，而是用本地服务的方式实现了邮件的读取和搜索能力。删除和修改的接口压根不存在，Agent 调不到。

---

## Skill 的三个根本问题

在 Skill 的设计范式下，如果想让所有 Skill 正常运行，需要给 Agent 的授权太大了。

**第一，运行时它说了算，环境说改就改。**

Skill 执行依赖本地环境，Agent 需要什么 runtime 就装什么。多个 Skill 之间，需要的开发环境不一致，需要的 runtime 也不一致。最后用户的 workspace 一团糟。

**第二，shell 权限一给出去，就收不回来了。**

Skill 需要执行 shell 命令的权限，但 shell 权限本身就是管理员级别的。你没法在给权限的同时又精确限制「只能做 A，不能做 B」——这是一个架构层面的矛盾，不是配几个文件能解决的。具体来说：

- 你允许 `curl`，Agent 可以下载恶意脚本再执行
- 你允许 `python`，Agent 可以用 Python 调用系统 API
- 你允许 PowerShell，Agent 可以执行任意 PowerShell 命令

无论你限得多细，Agent 的组合推理能力总能找到规则之外的路径。

**第三，Agent 做了什么，用户完全不知道。**

Agent 为什么选了这个 Skill 而不是那个？为什么放弃了原本能跑通的方案？用户只看到结果，不知道过程。这种黑盒在关键业务场景里是很危险的。

---

## 如果用 MCP 实现

在邮件的场景里，我的做法是：本地起一个邮件处理的 MCP 服务，只暴露读和搜索的接口。

这个方案不优雅，但它让我不需要回答一个问题：我愿不愿意把系统权限交给这个 Agent？

相比 Skill，MCP 的优势在于：

- **接口级别控制**：Skill 给的是「执行任意脚本」的能力，MCP 给的是「只能调用这几个接口」的能力
- **环境隔离**：MCP 服务运行在独立进程，不影响用户本地的 runtime 环境
- **权限边界清晰**：能调什么、不能调什么，用户心里有数

在企业场景里，我们的用户往往不是技术出身的。他们不会排查问题、不会判断异常、不知道 Agent 在他们电脑上干了什么。Skill 给他们的「自主权」，对他们来说未必是赋能，也可能是负担。

---

## 总结

Skill 设计面向的是专业的开发人员。但在把它交给非技术用户时，安全可能是更大的问题。
