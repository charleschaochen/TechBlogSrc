title: Git分支管理实践
date: 2016-02-16 14:13:00
tags:
- git flow
- branch
categories:
- 版本控制

---

我认为一个优秀的版本控制系统解决的问题不仅仅是代码的托管，而是与日常软件开发流程的融合和对多人协作、版本发布存在问题的改进。而目前Git无疑是最受欢迎的版本控制解决方案。

![](http://7x00n0.com1.z0.glb.clouddn.com/git-icon.png)

<!-- more -->

我们知道Git最大的特点在于分布式版本控制，Git还有另外一大优点就是它的分支管理，区别于传统的版本控制如SVN，创建新的分支会生成一份新的代码拷贝，Git只是生成一个指向当前版本的指针，不同分支共享同一份文件系统，所以创建和删除分支的开销很小，所以Git分支在日常开发过程中被广泛使用。

这时候问题就来了，如何有效和规范地管理一个仓库的分支，如何将分支管理与团队的开发流程相结合，指定出一套规范高效的项目代码管理机制？ Vincent Driessen发布的一个成功的Git分支模型文章中提到的Git分支管理策略，我觉得挺有借鉴价值的。具体的分支模型如下：

1. 主要“常驻”分支

master和develop是仓库“常驻”的两个分支，master是项目的主干，master的代码应该是项目可发布的最新版本。develop是开发分支，日常的开发在develop上进行。

![](http://7x00n0.com1.z0.glb.clouddn.com/git-1.png)

Git创建develop分支：
	
	git checkout -b develop master

将develop分支merge到master上：

	git checkout master
	git merge --no-ff develop 


注意：--no-ff意思是not fast forward merge，即不适用fast-forward（快速合并）的合并形式。Git默认情况下在merge的时候会采用fast-forward的形式，即直接将develop分支先于master分支的部分直接接到master上，如果指定了--no-ff，merge这个操作会生成一个新的提交节点，也就是说我们可以在历史中清晰地看到每次的merge操作，这可以保证版本演进的清晰。


2. 临时分支

除了master和develop两个"常驻"分支外，我们也可以创建一些临时分支来解决一些特定的问题，临时分支主要分为：

- 需求分支（feature）
- 预发布分支（release）
- 临时bug修复分支（hotfix）

这些临时分支正常情况下应该在merge后删除，避免仓库中残留太多不用到的分支

#### 需求分支feature ####

当我们开发某个特定的需求时，可以从develop分支上checkout一个feature分支，命名可以是feature-开头，后面带上需求的简述，或者是团队协作系统上的任务单号。开发完成后，将feature分支merge回develop分支。

![](http://7x00n0.com1.z0.glb.clouddn.com/git-2.png)

创建feature分支：

	git checkout -b feature-x develop

将feature分支合并到develop：

	git checkout develop
	git merge --no-ff feature-x

删除feature分支：

	git branch -d feature-x

需求分支应该在项目某个周期版本开始时，每个开发成员各自创建好并进行开发。


#### 预发布分支（release） ####

预发布分支其实相当于一个测试分支，是在版本发布前进行测试，问题点修复的分支。预发布分支从develop分支checkout出来，最终merge回develop分支和master分支。release分支的命名可以以release-开头，带上版本号，或者版本计划发布的日期。

![](http://7x00n0.com1.z0.glb.clouddn.com/git-3.png)

创建release分支：

	git checkout -b release-2.3 develop

合并release分支：

	git checkout develop
	git merge --no-ff release-2.3
	git checkout master
	git merge --no-ff release-2.3

	# 对合并生成的新节点，做一个标签
	git tag -a 2.3

删除release分支：

	git branch -d release-2.3

预发布分支应该在项目的测试期开始时创建，测试过程中发现的问题，可以在预发布分支上直接修改。可以在release分支上创建hook钩子，当release分支有push操作，就同步到远程测试服进行构建和部署，然后进行测试工作。


#### 临时bug修复分支（hotfix） ####

如果发布之后线上出现bug，需要发布临时bug fix补丁，可以创建一个hotfix分支。命名可以是hotfix-开头，带上bug的简述，或者团队协作系统的bug单号。hotfix分支是从master分支checkout出去，最终merge回master和develop分支。

![](http://7x00n0.com1.z0.glb.clouddn.com/git-4.png)

创建hotfix分支：
	git checkout -b hotfix-0.1

将hotfix分支合并到master和develop：

	git checkout develop
	git merge --no-ff hotfix-0.1
	git checkout master
	git merge --no-ff hotfix-0.1

删除hotfix分支：

	git branch -dhotfix-0.1


