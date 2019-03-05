# 规范

无规矩不成方圆。规范了就正如阅兵,整齐划一气势磅礴。 一个团队任何操作都是需要规则的。有哪些方面？

## 日常开发：

- IDE
  - 不论公司环境还是到个人开发，统一Eclipse。这样代码formatter统一。
- Commit
  - 所有的提交信息都要有意义辅助追踪和发布
- Naming
  - 简单易懂帮助别人和自己理解，如java 默认naming规则
- API
  - 参考spring naming/rest api 规范
- 编码
  - 码代码的习惯风格，防止bug，文档&注释，复杂度，测试 
- Exception Handling
  - 错误如何处理，如何抛出
- Git Branch Model
  - 代码提交流程，release/develop/hotfix/master/ 和snapshot&tag
- Logging
  - 日志太重要了，日志的规范尤其重要

## 系统：

- Development Process
  - 需求管理/代码评审/架构评审

## 我的开团java代码规范极简版
- IDE
> 统一ide: eclipse
> eclipse extra plugin: gradle,json editor
> eclipse setting:  preference -> java/javascript -> editor -> save actions -> perform selected action on save
<img src="/resources/pics/Screen%20Shot%202019-03-05%20at%209.36.14%20AM.png" alt='checkAllOptions' width='600'>
当你每次save一个文件时，自动删除没用的import/载入import class，自动format改动的代码，自动cast，自动annotate
- Commit 
> 参考[google commit convention](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines), [convetion commits](https://www.conventionalcommits.org/en/v1.0.0-beta.3/#specification)
```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>

例如:
refactor(sample.css): clean up css

modified .button to be more general style

important style change
```

除了好看有意义意外，有什么好处呢？ 可以根据type 直接按种类生成release's changelog。以此推广在公司环境下在type前加一个jira id, 完美。	 
有什么工具可以辅助一下吗？ 有， [cli工具](https://commitizen.github.io/cz-cli/),安装完如下：
<img src="/resources/pics/Screen%20Shot%202019-03-05%20at%2010.10.18%20AM.png" alt='commitTool' width='600'>







https://github.com/conventional-changelog/conventional-changelog



Logger规范

api 接口规范

# References:
[API_Design_Principles](https://wiki.qt.io/API_Design_Principles)
[Commit message 和 Change log 编写指南](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
[如何成为优秀的技术主管？你要做到这三点](https://baijiahao.baidu.com/s?id=1626542538622659949)
[how-to-write-an-open-source-javascript-library](https://egghead.io/series/how-to-write-an-open-source-javascript-library)
[get-started-contributing-to-javascript-open-source](https://egghead.io/articles/get-started-contributing-to-javascript-open-source)