---
title: "VibeCoding实践，AI能为开发提升多少效率？"
date: "2025-08-01"
tags: ["AI", "VibeCoding", "开发效率", "Tauri"]
summary: "通过一个桌面端小工具的开发实例，验证 AI 作为效率工具的实际效果：76% 代码由 AI 生成，4小时完成开发。"
---

VibeCoding在社交平台上一直是一个有争议性的话题。有人觉得AI能写代码之后不再需要程序员了，有人觉得AI写的代码实现不了复杂的功能，有人觉得AI写的代码有bug修复起来更花时间，有人觉得AI会偷走自己的业务代码。。

但不可否认的是，对于大多数开发者，AI已经是工作中的一部分。下面我会分享一个使用AI完成一个桌面端小工具的例子。体验一下使用AI能提升多少效率。

当然如果不想看例子我也把总结放到最前面。

# 总结

1.  AI没有办法完成所有工作：

*   用户输入需求，AI直接生成产品。这种理想化的场景目前基本上不可能实现。原因不仅在于AI能力本身不足，也在于非专业用户表达需求的能力不足。人们往往愿意承认自己不是一个程序员，但不愿意识到自己也不是一个产品经理。在一些复杂的业务场景中，写代码比使用自然语言描述效率更高。

2.  AI作为效率工具，对人力的替换方式是：高级程序员 + 初级程序员 => 高级程序员 + AI

*   很多人认为的AI替代程序员是从 产品经理 + 程序员 + 测试 => 产品经理 + AI +测试。但事实上的替换过程是： 高级程序员 + 初级程序员 => 高级程序员 + AI。这个过程已经在作画，写网文等类似领域反复验证。由AI生成大量的素材+高级的专业人员来进行审查筛选=>产出 将会变成标准的模式。
*   题外："打怪升级"的技能提升方式将被架空，初级的专业人员不再被需要，"出了新手村就是大boss"的情况会越来越常见。。

3.  使用AI最佳的场景：实现起来困难，但是验证起来简单。

*   由于幻觉的存在，AI返回的内容并不总是可靠的，必须要经过人的审查/验证才可信。但是如果一件任务验证的难度超过人直接去做这件任务的难度，那就得不偿失了。 我这里可以提出两个比较常见的场景：
    *   前端开发补全css。前端开发写css是一件非常费时的事情，但是用AI补全css，提出要求与既有风格相符。AI生成代码后，开发只需要在前端页面验证是否符合预期即可。
    *   实现模块化的单点功能，比如实现防抖，节流，重试等常见的标准能力。同样AI生成代码后，开发只需要验证单点功能是否符合预期即可。

4.  工具有适用的场景。

*   我们不会因为自行车不能在水上骑，就认为自行车没有用，也不会期望自行车能解决所有的出行问题。

5.  现在是开发者VibeCoding的窗口

*   现在的AICoding对非专业的用户还不够友好，但是对专业的开发者开发效率的提升非常可观。这对个人/小开发团队来说是一个好的窗口。

# 从零开始开发桌面端工具

## 工具总览

*   技术栈：桌面端 Tauri (前端 Angular，后端 Rust) 服务端 (java)。
*   Vibe Coding：Tauri前端：910(AI生成750) Tauri后端:190(AI生成90)  合计AI生成代码  840 / 1100  76%。
*   开发时长：4小时。

## 业务背景

当前工作中，我们通常在windows上开发代码打包，在实验室环境上部署服务进行测试。因此我希望能开发一个桌面端的小工具。来解决打完包到部署服务之间的GAP。

实现的基本功能如下：

1.  提供一个简单的用户输入界面，包含如url，实验室环境ip/用户名/密码 等必须字段。
2.  访问输入的url地址下载服务打包好的zip包。
3.  将zip包推送至实验室环境。
4.  在实验室环境顺序执行一些bash脚本，完成服务的部署。

用到的AI工具：豆包。(当然使用Trae会有更好的体验，但是考虑安全因素不使用外部ide)

输入限制：不输入任何已有的本地代码，仅允许输入提示词、实现本工具新写的代码、豆包自身生成的代码。

## 确定技术栈

因为我没有太多开发桌面端的经验，因此也通过简单描述工具功能寻求豆包建议。可以看到首次提问时我对整个工具并没有非常清晰的规划，但是豆包仍然给出了多个建议。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/29bc4b7a952b430f86933029aa9bac3d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=qyvW8RGhwhre48PoDQ9EF%2B0askI%3D)

同样对于不熟悉的领域也寻求了专家的建议，出现了和豆包建议重合的框架Tauri。因此最终选用的Tauri框架来实现。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/9052f767559c440fb2d409f5f0436922~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=c%2FSefkNZtnwRLgfXZakvUYyvJrk%3D)

## 搭建Tauri框架(累计耗时0.5小时)

打开Tauri官网
<https://tauri.app/start/>

根据官网文档安装依赖：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/3c54a78ec64142e297f5ba3a29268924~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=QWYmlI5SWudwi5s8%2BqKHmiJM0iY%3D)

按照官网文档创建项目：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/93bb0ec5b21f4098a4fd6939a216ea37~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=sOmbaW2JG8vJEAgGz9GqIHOnVrI%3D)

考虑到团队内部的技术积累，选取前端UI模板Angular：

## 前端Coding

### 人工完成前端基本功能开发(累计耗时1小时)

前端实现直接手写了html+ts代码，主要包含两个功能：

*   输入url-查询-返回表单-过滤条件
*   表单中勾选文件，点击下载

使用自然语言描述对我而言更复杂也更花费时间，手写代码实现基本的页面框架更高效。代码中为相同类型的组件预置了相同的class，我期望这种写法在后续使用AI补全css代码时能得到更好的结果。

```html
<main class="container">
  <form class="url-form" (submit)="query($event, inputUrl.value)">
    <div class="form-row">
      <textarea #inputUrl id="url-input" class="url-text textarea" placeholder="支持传入多个url 每个url单独一行 例：
https://xxxx1.com
https://xxxx2.com
......"></textarea>
      <button type="submit" class="button primary">查询</button>
    </div>
    <div class="form-row">
      <input type="text" class="search-text  input" placeholder="请输入搜索关键字" [(ngModel)]="filterKey"
        [ngModelOptions]="{standalone: true}" (input)="applyFilter()">
      <input type="checkbox" class="checkbox" [(ngModel)]="selectAll" (change)="toggleAll()"
        [ngModelOptions]="{standalone: true}" /> 全选
      <input type="checkbox" class="checkbox" [(ngModel)]="onlyZip" (change)="applyFilter()"
        [ngModelOptions]="{standalone: true}" /> 仅显示zip/gz文件
    </div>
  </form>
  <div class="table">
    <ng-container
      *ngFor="let v of data.filteredData | paginate: { itemsPerPage: itemsPerPage, currentPage: currentPage }">
      <div class="fileRow">
        <input type="checkbox" class="checkbox" [(ngModel)]="v.checked" (click)="checkBoxClick($event,v)" [ngModelOptions]="{standalone: true}" />
        <div class="fileText">{{v.name}}</div>
      </div>
    </ng-container>
  </div>
  <form (submit)="download($event,user.value,userPwd.value,roowPwd.value,turnkey.value,downLoadPath.value)">
    <div class="group">
      <div class="row">
        <div class="label">登录用户：</div>
        <input #user id="user-input" class="input" placeholder="请输入登录用户" value="sopuser" />
      </div>
      <div class="row">
        <div class="label">登录用户密码：</div>
        <input #userPwd id="userPwd-input" type="password" class="input" placeholder="请输入登录用户密码" value="" />
      </div>
    </div>
    <div class="group">
      <div class="row">
        <div class="label">ip：</div>
        <input #ip id="ip-input" class="input" placeholder="请输入turnkey ip地址" value="" />
      </div>
      <div class="row">
        <div class="label">下载目标目录：</div>
        <input #downLoadPath id="downLoadPath-input" class="input" placeholder="请输入下载目标目录：" value="" />
      </div>
    </div>
    <div class="download-info" *ngIf="data.fileList.length > 0">
      <button type="submit" class="download-button button primary">下载</button>
    </div>
  </form>
</main>
```

基本样式如下：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/142d7693ad464ae8bee51607220221dc~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=JqngMRUv6sJpFr9iaujISKcRr6c%3D)

### 前端css补全(累计耗时0.2小时)

提示词如下，后面附上了上一步手写的全部html代码：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/cb19d9abf9354c2d80d214de20ee3fb2~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=rR2X4SGMofQanoUGDnCuxxQ6Ntg%3D)

deep research会进行一次反问，获取更详细的需求。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/01cf4b6fe2c04a23ae272083d491305c~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=IkhJkunRzv0%2BfKw8YP4Ni6R4ULY%3D)

生成css小样：一次性返回了500行左右的css代码，这里不全部展示了。我们以button为例，可以看出AI生成的css代码是比较完善的，不仅仅符合了我们需要的风格，还为其添加了hover,active,focus等状态样式(相比初级的前端开发首次完成开发任务的完成度)。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/c420d9bfe4a8440da3b993d3663c9199~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=B1AH9CXRvfYbbNTo3VrnrjGD5rU%3D)

将css文件加入项目代码后页面，基本符合日常使用需求：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/a041d41461604fd4b9120550ebc625e3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=quZl6Sq0YFWYKMLw5gftQR0EGIs%3D)

### 单点功能生成：获取url中参数(累计耗时0.1小时)

这里我在提示词中直接给出了一个实例和返回结果。直接生成了可用的代码。生成代码中含有示例使用方式方便合入。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5e411e995ff643f6a2d5cc522a2f9d04~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=LSaalv0Hc1SM6podfMqjbqaH05o%3D)

### 单点功能生成：日志展示(累计耗时0.1小时)

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e6ca8a32ad05428ab3870ea662124582~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=7P18gFP6IBzQn3F%2FQdDmv9IaWhA%3D)

生成代码样例，已经包含了完整的功能实现，代码符合Angular写法，放入项目中直接可用，并且包含了防止内存溢出处理(超出预期)：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d3328e5397ca4fefb8d309aee8d7d9ff~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=RngHrfeZO7vNw2X4iKiTGaNqj9E%3D)

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/8357f66ddc204fe7b5db5f801bae2bde~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=6vFH8ArCNOuLBlNIyH0OUdMoLlU%3D)

生成页面效果：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/1cabd71e76fc48cf93230290032f0541~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=E%2FSsRVwO9xbLhcEiC%2FKTOk%2B5Ce0%3D)

### 单点功能生成：限流重试(累计耗时0.2小时)

这是一个后端比较经典的功能，发起多个请求，当其中存在失败的请求后单个请求重试。这时候对VibeCoding的使用已经比较熟悉了，描述也更加结构化，生成的代码逻辑上没有错误，我对一些变量名进行修改后放入项目代码直接可用。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/bea31c7426154ccbb02579ae269d2c0d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=g4xQ5FAdWVhcFw4YE1uflBIFdM0%3D)

生成代码样例：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/ed8ad4c3da2645bca5d03d588b502625~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=YhYdWAUTFIjOx01c0lE%2Bbqaiuek%3D)

## 后端Coding

### 使用框架问题(累计耗时2小时)

在后端上，虽然说明了需要Tauri 2.0框架下的代码样例，但是生成的仍然是1.0的代码，并不兼容。看来对一些非主流的框架还是有一些限制。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/88e7e0fe37004ee0ab1516d403e7611a~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=SNaxcYwvBQs5k4UYU9pEd470ui0%3D)

考虑查询rust接口文档,跟着文档实现还是很快的：

reqwest：<https://docs.rs/reqwest/latest/reqwest/>

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/2f24d8225558476db8c7d5f1ab84237a~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=VcghybK71y4Oo%2FAe%2B7SVRR6og2M%3D)

ssh2：<https://docs.rs/ssh2/0.9.5/ssh2/index.html>

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/f8ab1f3398c843b2b66fffad63ca45ba~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=13J6iVnzthjHu6HfDD6ju2EVGGo%3D)

### 打包

框架问题还是查询官方文档最快，按照文档完成打包配置，一行命令打包。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/05cd1d1b287c4967b025eabb3621d0d1~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgNGNvczkw:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjc5Njc5NDk1NTQ0MTc4NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d345f2ac7e96d3d06cdbb035152127018&x-orig-expires=1780751938&x-orig-sign=oLMoaqo0QW3Wx3LxppfOkkShwSY%3D)

## 如果使用trae

出于信息安全考虑本次并没有使用ide来完成整个Vibe Coding的过程。

最近在家里使用trae开发，感到build agent的功能还是非常不错的。给了本地执行cmd的权限后很多事可以通过自然语言来执行。这对于整个开发过程来说无疑更加节省时间和高效。不过当存在网络受限的问题时(如需要切换下载源)时，仍然需要手动排查。
