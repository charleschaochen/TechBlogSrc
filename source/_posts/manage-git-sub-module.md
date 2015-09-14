title: 如何用JGit管理Git子模块
date: 2014-05-07 11:03:35.0
tags:
- JGit
- Git
categories:
- 技术研发

---

原文链接：javacodegeeks   翻译：ImportNew.com - chenchenchao译文链接：http://www.importnew.com/11095.html对于一个较大的Git工程，你可能会想在多个仓库之间共享代码,不管这些代码是在多个不同产品间使用的项目共享库或是一些模板。Git通过子模块来实现这样的需求。子模块允许将其他代码仓库的克隆作为子目录放...

<!-- more -->

**原文链接：**[**javacodegeeks**][javacodegeeks]**   翻译：**[**ImportNew.com** ][ImportNew.com_]**-** [**chenchenchao**][chenchenchao]  
**译文链接：**[**http://www.importnew.com/11095.html**][http_www.importnew.com_11095.html]

对于一个较大的Git工程，你可能会想在多个仓库之间共享代码,不管这些代码是在多个不同产品间使用的项目共享库或是一些模板。Git通过子模块来实现这样的需求。子模块允许将其他代码仓库的克隆作为子目录放到一个父仓库（有时候也称为父项目）中。一个子模块也是一个独立的仓库，你可以像其他仓库一样执行commit，branch，rebase等等操作。

JGit提供了实现大部分Git子模块命令的API。我将在这儿给大家介绍这些API。

### 设置 ###

本文中用到的代码片段将作为学习测试程序。简单的测试程序有助于理解第三方库是如何工作，以及如何使用新的API。你可以将这些测试程序看做是可控制的试验，帮助你更加直观地发现第三方代码是如何执行的。

除此之外，如果你保持编写测试程序，可以帮助你检验第三方代码的新版本。如果你的测试程序涵盖了如何调用这些库，那么第三方代码中不兼容的修改将会尽早展现出来。

回到之前的话题，所有的测试程序共享同一个设置，详细信息请查看[源代码][Link 1]。现在有一个空的仓库，叫parent，以及另一个仓库叫library。测试程序中，library将会作为子模块添加到parent仓库中。library仓库初始化提交了一个readme.txt文件。测试程序中有一个setUp方法，用来创建这两个仓库，如下所示：

    Git git = Git.init().setDirectory( "/tmp/path/to/repo" ).call();

这两个仓库用类型为Git的parent和library变量表示。该类封装了一个仓库并允许访问JGit的所有可用指令。就如较早之前我在[这里][Link 2]中提到，每个Commnad类对应于一条原生的Git pocelain指令。调用一个指令需要用到生成器模式。举个例子，执行Git.commit()的结果实际上相当于一个CommitCommand。你可以提供一些必要的参数去调用它的call()方法，从而执行相应的指令。

### 添加一个子模块 ###

第一步当然是在一个已有的仓库添加子模块。通过上面提到的setUp步骤，library仓库应当作为子模块添加到parent仓库的modules/library目录下。

    @Test
    public void testAddSubmodule() throws Exception {
      String uri 
        = library.getRepository().getDirectory().getCanonicalPath();
      SubmoduleAddCommand addCommand = parent.submoduleAdd();
      addCommand.setURI( uri );
      addCommand.setPath( "modules/library" );
      Repository repository = addCommand.call();
      repository.close();
     
      F‌ile workDir = parent.getRepository().getWorkTree();
      F‌ile readme = new F‌ile( workDir, "modules/library/readme.txt" );
      F‌ile gitmodules = new F‌ile( workDir, ".gitmodules" );
      assertTrue( readme.isF‌ile() );
      assertTrue( gitmodules.isF‌ile() );
    }

SubmoduleAddCommand对象需要知道两件事，第一是子模块从哪里克隆而来，第二是它应该存放在哪里。URI属性表示仓库库的克隆地址，这个克隆地址将会传递给clone命令。path属性则指定了相对于parent仓库根工作目录的路径，子模块将被存放在这个路径。这个指令执行之后，parent仓库的工作目录将会变成这样：

 ![submodule-tree.png][]

library仓库存放在modules/library目录下，而且它的工作目录树被检出。call()方法返回一个Repository对象，你可以把它当做一个常规的仓库来使用。这也意味着，你必须在程序中明确显式地关闭返回的仓库，以避免文件句柄泄露。

从上图我们可以看到，SubmoduleAddCommand做了一件事，它在parent仓库的根工作目录下创建了一个.git模块文件，并把它添加到索引中。

    [submodule "modules/library"]
    path = modules/library
    url = git@example.com:path/to/lib.git

如果你打开过Git的配置文件，你会发现以上句法。这个文件列出了当前仓库的所有子模块。对于每个模块，文件中列出了它仓库URL地址以及本地路径。一旦commit并push了这个文件，克隆这个仓库的一方就知道哪里可以获取相应的子模块（稍后会详细讲解）。

 

### 列出子模块 ###

当我们添加了一个子模块之后，我们可以会想知道，它是否对于父仓库来说是可知的。第一项测试中我们做了一个基础的检测，验证了某些文件和目录的存在。我们也可以使用一个API来列出一个仓库的子模块，如下所示：

    @Test
    public void testListSubmodules() throws Exception {
      addLibrarySubmodule();
     
      Map<String,SubmoduleStatus> submodules 
        = parent.submoduleStatus().call();
     
      assertEquals( 1, submodules.size() );
      SubmoduleStatus status = submodules.get( "modules/library" );
      assertEquals( INITIALIZED, status.getType() );
    }

SubmoduleStatus命令返回了一个子模块的Map集合，其中键是子模块的路径，值是这个模块的状态值。通过以上代码我们能够验证子模块确实已经添加进去，而且它的状态是INITIALIZED的。这个命令还允许添加一个或多个路径来限制子模块状态。

说到状态，JGit的StatusCommand并非原生的Git指令。如果在执行指令时添加选项[‐‐ignore-submodules=dirty][ignore-submodules_dirty]，那么所有对子模块工作目录的修改都会被忽略。

### 更新子模块 ###

子模块通常指向他们所在的仓库的一次特殊的提交。如果之后有人克隆了父仓库，他们也会获得与之完全相同的子模块状态，即便子模块的上游有新的提交。

为了修改子模块，你像一下代码一样明确地对其进行更新：

    @Test
    public void testUpdateSubmodule() throws Exception {
      addLibrarySubmodule();
      ObjectId newHead = library.commit().setMessage( "msg" ).call();
     
      File workDir = parent.getRepository().getWorkTree();
      Git libModule = Git.open( new F‌ile( workDir, "modules/library" ) );
      libModule.pull().call();
      libModule.close();
      parent.add().addF‌ilepattern( "modules/library" ).call();
      parent.commit().setMessage( "Update submodule" ).call();
     
      assertEquals( newHead, getSubmoduleHead( "modules/library" ) );
    }

这个较长的代码片段中，首先第一件事就是提交一些东西到library仓库中（第四行），接着将子模块更新到最近的一次提交。

为了让这种更新持久化保存下来，子模块必须被提交（第10,11行）。这次提交在子模块的名下（例子中是modules/library）保存了此次更新的commit-id。最后，通常需要将修改push上去，使得他们对其他仓库可用。

 

### 在父仓库中更新对子模块的修改 ###

将上游的提交拉取到父仓库中也会修改子模块的配置。然而子模块本身并不会自动得到更新。

SubmoduleUpdateCommand就是用来解决这个问题。使用这个命令并不需要指定其他参数，它会更新所有已注册的子模块。该命令会克隆缺失的子模块并检出其配置中指定的提交。就如其他子模块命令一样，这里也有一个addPath()方法，以保证只更新给定路径下的子模块。

### 克隆一个包含子模块的仓库 ###

此时你可能已经掌握一个规律，所有对子模块的操作都是手动的。克隆一个包含子模块配置的仓库并不会默认克隆它的子模块。但是，CloneCommand命令有一个cloneSubmodules的属性，如果设置为true，那么将会克隆所有配置的子模块。从内部看，在对父仓库进行克隆之后，SubmoduleInitCommand和SubmoduleUpdateCommand命令会被递归地执行，并且父仓库的工作目录会被检出。

### 移除一个子模块 ###

如果要移除一个子模块，你会希望可以这样写：

    git.submoduleRm().setPath( ... ).call();

但是很不幸，不管是原生的Git或者JGit都没有提供内置的移除子模块的指令，希望将来会添加这样的指令，在这之前，我们必须手动去移除子模块。如果你滚动到[removeSubmodule()][Link 1]方法你会发现这并不是一件复杂的事。

首先，各个子模块会从.gitsubmodules和.git/config配置文件中移除。其次，子模块的入口会从索引中被移除。最后，.gitsubmodules文件以及索引的修改会被提交，并且子模块的内容会从工作目录中删除。

### 遍历子模块 ###

原生的Git提供了git submodule foreach命令为每个子模块执行一个shell指令。JGit并没有直接支持这样的指令，而是提供了SubmoduleWalk。该类可以用来迭代仓库中子模块。以下示例程序实现了为所有子模块拉取上游的提交。

    @Test
    public void testSubmoduleWalk() throws Exception {
      addLibrarySubmodule();
     
      int submoduleCount = 0;
      Repository parentRepository = parent.getRepository();
      SubmoduleWalk walk = SubmoduleWalk.forIndex( parentRepository );
      while( walk.next() ) {
        Repository submoduleRepository = walk.getRepository();
        Git.wrap( submoduleRepository ).fetch().call();
        submoduleRepository.close();
        submoduleCount++;
      }
      walk.release();
     
      assertEquals( 1, submoduleCount );
    }

通过next()方法walk对象可以指向下一个子模块，如果没有更多的子模块，该方法会返回false。使用SubmoduleWalk时，通过调用release()方法可以释放子模块相关的资源。再次强调，如果你获得一个子模块的仓库实例可别忘了关闭它。

SubmoduleWalk也可以用来获取子模块的详细信息。通过它的大部分getter方法可以访问到当前子模块的属性，诸如path，head，remote URL等等。

### 同步远程URL ###

从上面我们知道子模块的配置保存在父仓库根工作目录下的.gitsubmodules文件中。而至少，在.git/config文件中，我们可以重写覆盖子模块的远程URL。对于每个子模块，它们本身都有一个配置文件。那么反过来，每个子模块可以有另一个远程URL。SubmoduleSyncCommand命令可以用来将所有远程URL重置为.gitmodules中的配置。

综上所述，JGit对子模块的支持几乎与原生的Git一致。大部分Git指令都在JGit中实现了，或可以通过一些途径进行仿真。如果你发现一些操作缺失或实现不了，可以去友好且帮得上忙的[JGit社区][JGit]去寻求帮助。

 

 


[javacodegeeks]: http://www.javacodegeeks.com/2014/04/how-to-manage-git-submodules-with-jgit.html
[ImportNew.com_]: http://www.importnew.com/
[chenchenchao]: http://www.importnew.com/author/chenchenchao
[http_www.importnew.com_11095.html]: http://www.importnew.com/11095.html
[Link 1]: https://gist.github.com/rherrmann/10130951
[Link 2]: http://www.javacodegeeks.com/2013/11/an-introduction-to-the-jgit-sources.html
[submodule-tree.png]: http://a3ab771892fd198a96736e50.javacodegeeks.netdna-cdn.com/wp-content/uploads/2014/04/submodule-tree.png
[ignore-submodules_dirty]: https://www.kernel.org/pub/software/scm/git/docs/git-status.html
[JGit]: http://www.eclipse.org/jgit/support/