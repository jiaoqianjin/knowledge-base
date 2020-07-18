# 使用 GitHub flow
#document/流程

### 推荐使用 GitHub flow 协作方式
	1. 不在主仓库的分支上开发，而是 fork 到自己 namespace 下。每次开发，需要先checkout 一个新的分支，commit 之后推送到自己的仓库，再向主仓库提 Merge Request。所以开发过程中对于大多数项目来说，都会经过如下步骤：
		* Fork(Once)
		* Create a branch
		* Add commits
		* Open a Merge Request
		* Code review
		* Merge
		* Deploy
![](%E4%BD%BF%E7%94%A8%20GitHub%20flow/WeChatfdc901f28fb0c395a7cc5d54d2891336.png)

### 提交 Merge Request 
	1. Commit Message 每次提交，commit message 应该清晰明了，说明本次提交的改动和目的，禁止使用无意义的乱码或单个词语。
	2. 示例：
		* feat：新功能（feature）
		* fix：修补bug
		* docs：文档（documentation）
		* Style： 格式（不影响代码运行的变动）
		* Refactor：重构（即不是新增功能，也不是修改bug的代码变动）
		* Test：增加测试
		* Chore：构建过程或辅助工具的变动
	3. 禁止使用 **git push -f**
	4. 提交什么，不提交什么。
		* 正确使用 .gitignore 文件很重要，下面这些文件，不应该被提交到代码仓库中：.vscode/   .idea/  等工程管理目录， IDE free 的、.pyc  .class  等自动生成的中间状态文件、 	￼.DS_Store  等操作系统目录管理文件等￼其他自动生成的文件
		* 关于  .gitignore 文件，可以参考 GitHub 的 gitignore 模板：https://github.com/github/gitignore。
	4. 分支使用规则
		* master 分支始终是可部署的
		* ￼开发过程中，分支名没有特殊要求，但推荐见名知意的合理命名，使用 feature, fix 等作为前缀，单词之间使用连字符（-）。
		* ￼不要把分支推送到 upstream repo
		* ￼checkout 前先拉取新的 upstream master
	5. 创建 MR创建之前，请检查：
		* 不会跟 master 冲突
		* ￼如果 master 已有新的 commit，建议先 rebase。如果 commit 太多，可以把多个 commit 合并成一个或几个（通过 rebase -i 命令）
		* 创建 Merge Request 的时候，需要填写一些信息：
		**￼一定要填写的**：
				1. 标题：见名知意的标题非常重要，通过 MR title 可以做很多事情
				2. Assignee：￼Approvers：你希望来 review 你本次 MR 的同学，可以为多个
		￼**不强制但是建议填写的**：
				1. ￼描述：如果是小改动，这里可以为空。但当改动较大或者较重要时，强烈推荐认真填写这里。如果有对应 JIRA task，建议补充上。
				2. ￼勾选 Squash commit 选项（关于 Squash and merge）
				3. 推荐项目使用 MR template，使用后在提交 MR 的时候，可以选择对应的模板（如 bugfix/feature 等），描述填写框内会出现对应模板内容，提交 MR 时填写即可。
				4. ￼确认所有的讨论被标记为「解决」
				5. 推荐先 rebase
				6. MR 的粒度Do one thing! 每次 MR 的粒度不宜太大，这样才能保证 review 的效果。每个 MR 都应该是可以单独上线的，方便定位问题和回滚。
				7. 如果可以，不要等一个功能的全部开放完成再提交。例如一些基本的代码，如果先合到 master 也不影响现有的代码，就可以先提交。特殊情况下测试部分的代码，也可以单独提交。
### Code Review 
	1. Review 的目的是什么
		* 发现错误：人都会不可避免的出现一些纰漏，而这些纰漏在另一个人眼中也许显而易见。
		* 健壮性检查：代码是否健壮，是否有潜在安全、性能风险。代码是否可以回滚。
		* ￼质量保证：在一般情况下，新提交的代码一定需要写测试，测试不只可以保证你的提交符合预期，还可以在后人改你的代码时多一层保障。
		* ￼统一风格：对于整个团队来说，代码风格的统一很重要。
		* ￼完善注释：包括 commit message、代码中复杂实现是否有解释性的注释、紧急 hack 是否明确标注等。
		* ￼互相学习
		* 哪些人需要参与 Review
			1. 项目的维护者_Tech leader_Mentor
			2. ￼上下游影响的相关人员
	2. Reviewer 关注的重点是什么
		* 功能的正确性
		* ￼是否符合代码规范（文件和变量命名、接口设计）
		* ￼有没有引入 bug 和隐患（性能影响、内存泄漏、安全漏洞）
		* ￼有没有注释、文档和相关测试
	3. Developer 应做到
		* 保证自测通过
		* ￼提交完 MR 先自行 review 一遍
		* ￼描述要简洁、清晰，可以附上对应的 Task 或 RFC。还在进行中的 MR 应在标题加上 WIP
		* ￼视 MR 的改动大小，选择合适的 review 人员
	4. Reviewer 应做到
		* 认真 review，对码不对人，对好的设计和代码给予鼓励和肯定
		* ￼一次性 Review 完所有代码，如果通过，给予 approve；如果没有通过，持续关注 MR 的更新和讨论


### 相关资料
	1. Git 学习地址：[Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)


