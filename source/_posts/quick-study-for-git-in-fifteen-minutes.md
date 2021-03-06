title: 15分钟学会使用Git和远程代码库
date: 2014-01-15 14:21:09.0
tags:
- Git
- 版本控制
categories:
- 技术研发

---

Git是个了不起但却复杂的源代码管理系统。它能支持复杂的任务，却因此经常被认为太过复杂而不适用于简单的日常工作。让我们诚实一记吧：Git是复杂的，我们不要装作它不是。但我仍然会试图教会你用（我的）基本的Git和远程代码库干活的工作步骤，在15分钟内。 工作步骤我会展示以下的步骤，通常能帮我独自在一台或多台机器上做项目。创建一个远程的空代码库（在BitBucket上）在本地代码库添加一个项...

<!-- more -->

Git是个了不起但却复杂的源代码管理系统。它能支持复杂的任务，却因此经常被认为太过复杂而不适用于简单的日常工作。让我们诚实一记吧：Git是复杂的，我们不要装作它不是。但我仍然会试图教会你用（我的）基本的Git和远程代码库干活的工作步骤，在15分钟内。

 

## 工作步骤 ##

我会展示以下的步骤，通常能帮我独自在一台或多台机器上做项目。

1.  创建一个远程的空代码库（在[BitBucket][]上）
2.  在本地代码库添加一个项目
3.  在分支上开发新功能
4.  a) 保留新功能 或者 b) 丢弃它们
5.  也许，回到某个早先的时间点
6.  将本地代码库推送到远程代码库
7.  在另一台机器上取得远程代码库

 

## **安装Git    ** ##

在大多数\*nix系统（Linux、OS X）上，Git已经被安装了。你通过发送下面的命令，可以通过Git自身，把它更新到最新的的开发版本（不推荐）。

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git clone https:</code>
      <code>//github</code>
      <code>.com</code>
      <code>/git/git</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

在Windows上，你可以在[这里][Link 1]下载Git的安装程序。如果你真的需要其他系统的安装程序，Mac OS X安装文件在[这里][Link 2]，Linux的操作指导在[这里][Link 3]。

 

## 创建一个远程代码库 ##

很多人喜欢用[Github][]。我个人更喜欢[BitBucket][]，因为它提供了不限制的私有代码库，那是我最需要的。你可以将下列指令转换到Github上，这些过程是相同的。

那么，去到[www.bitbucket.org][BitBucket]并注册一个账号。一旦完成，登录后点击最上方的“create（创建）”按钮。照着填写表格，勾选私有代码库。你可不想让其他人来偷窥你的Facebook的杀手级应用的源代码，对吧。

 你现在可以离开BitBucket了，我们在已经有了所有那里需要的东西了。

##  ##

 

## 设置Git ##

在我们能用Git工作之前，我们需要做个一次性的配置。为了Git能跟踪到谁做了修改，我们需要设置你的用户名。我强烈建议你使用与注册BitBucket账号相同的用户名和电子邮箱地址。发送这些命令，相应地替换掉其中的“your\_username”和“your\_email@domain.com”（注意引号）：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git config --global user.name </code>
      <code>"your_username"</code>
     </div>
     <div>
      <code>git config --global user.email your_email@domain.com</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

我们也会设定推送（push）的默认值为‘simple’。要了解这是什么意思，快速阅读我之前发布的[关于推送的默认值][Link 4]（非必须）。发送这条命令：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git config --global push.default simple</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

我们都设好了。你无需在你的机器上再重复这些配置，但如果你在另一台机器上工作的话，不要忘记这些配置。如果你忘记做初始的配置，Git不会允许你提交任何东西，这会让你困扰。

##  ##

 

## **创建一个本地代码库** ##

作为例子，我们会假装我们有一个网站（无所谓技术）存在于我们机器上的‘workspace’文件夹下的’my\_site’文件夹内。在命令行中，去到你的站点的根文件夹。在OS X和Linux上：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>cd</code> 
      <code>~</code>
      <code>/workspace/my_site/</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

在Windows上：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>cd</code> 
      <code>c:\workspace\my_site</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

我们首先需要告诉Git这个文件夹是我们需要跟踪的项目。所以我们发送这个命令来初始化一个新的本地Git代码库

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git init</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

Git会在my\_site文件夹内创建一个名为.git的隐藏文件夹，那就是你的本地代码库。

##  ##

 

## 加载（Stage）文件 ##

我们现在需要命令Git我们需要加载（stage）所有项目文件。发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git add .</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

最后的“.”符号的意思是“所有文件、文件夹和子文件夹”。假如我们只想要把特定文件添加到源代码控制中去，我们可以指定它们：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git add my_file, my_other_file</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

##  ##

 

## 提交文件 ##

现在，我们想要提交已加载（staged）的文件。阅读“添加一个时间点，在这里你的文件处在一个可还原的状态”。我们提交我们的文件时，总是附带着有意义的注释，描述了它们现在的状态。我一直用“initial commit”来作为第一个提交的注释。

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git commit -m </code>
      <code>"initial commit"</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

就这样。现在你随时都可以回滚到这个提交状态。如果你有需要检查你现在的已加载（staged）和未加载（unstaged）文件的状态、提交等，你可以询问git的状态：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git status</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

##  ##

 

## 创建分支 ##

建立分支是你创建代码的独立版本的动作，独立于你的主干分支。默认地，每次你提交到Git的文件都会被储存到“master（主干）”分支。

现在我们来说说，你想要向项目里添加一个功能，但你想要能够回滚到现在版本，以防出现差错，或者你决定要放弃这个功能。这就是你创建分支的时候了。创建并同时切换到你新建的分支，发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git checkout -b new_feature</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

或者，你可以先创建一个分支然后手动切换，就像这样：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git branch new_featuregit checkout new_feature</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

要看你现在项目下所有的分支，发送这个：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git branch</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

现在你可以在你的项目上无所顾忌地做任何你想做的：任何时候，你都可以回到你创建分支前的状态。注意，你同时可以有多个分支，甚至可以从一个分支上再创建一个分支。

##  ##

 

## 合并分支 ##

当你对你的新功能满意了的时候，你想要把它加到主干分支上。当你在你的新功能分支上时，你首先需要加载（stage）并且提交你的文件：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git add .git commit -m </code>
      <code>"adds my new feature"</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

然后你移到你的主干分支：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git checkout master</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

像这样合并：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git merge new_feature</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

此时，你的主干分支和你的新功能分支会变成一样的了。

##  ##

 

## 丢弃分支 ##

相反，如果你打算丢弃你在分支里做的修改，你首先需要加载（stage）你的文件并且在分支里提交：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git add .git commit -m </code>
      <code>"feature to be discarded"</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

然后，你移到主干分支：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git checkout master</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

现在，你的代码处于你创建分支之前的状态了。

##  ##

 

## 删除一个分支 ##

如果你要把你的分支合并到主干分支，从主干（master）分支上发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git branch -d new_feature</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

假如修改已经合并了，它*只会*删除分支。假如分支没有合并，你会得到一个错误信息。删除一个未合并的分支（通常你不想保留的修改），你需要发送一样的命令附带一个大写D。意思是“强制删除分支，无论如何我不想要它了。”：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git branch -D new_feature</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

##  ##

 

## 回滚到之前的提交状态 ##

在某些时候，你可能想要回到之前的代码版本。首先，你需要找到你想回到哪个版本。要看所有的完成了的提交，发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git log</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

这会输出你的提交的历史记录，像这样：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>commit ca82a6dff817ec66f44342007202690a93763949Author: your_username your_email@domain.comDate:&nbsp;&nbsp; Mon Nov 4 12:52:11 2013 -0700&nbsp;&nbsp;&nbsp; changes the frontpage layout</code>
     </div>
     <div>
      <code>commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7Author: your_username your_email@domain.comDate:&nbsp;&nbsp; Mon Nov 4 11:40:33 2013 -0700&nbsp;&nbsp;&nbsp; adds my new feature</code>
     </div>
     <div>
      <code>commit a11bef06a3f659402fe7563abf99ad00de2209e6Author: your_username your_email@domain.comDate:&nbsp;&nbsp; Mon Nov 4 10:37:28 2013 -0700&nbsp;&nbsp;&nbsp; initial commit</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

如果你想回到“adds my new feature”这个提交，简单地用提交的ID做签出（checkout）（我通常只用到ID开头的9个字符）

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git checkout 085bb3bcb</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

你也可以签出到一个新的分支，像这样：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git checkout -b my_previous_version 085bb3bcb</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

*只是别太疯狂了！*你的分支越复杂，就越难确定你真正在做什么。

##  ##

 

## 推送到远程代码库 ##

在第一次你想推送一个本地代码库到远程代码库时，你需要把它添加到你的项目配置里。像这样做：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git remote add origin https:</code>
      <code>//your_username</code>
      <code>@bitbucket.org</code>
      <code>/your_username/name_of_remote_repository</code>
      <code>.git</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

注意这里的“origin”只是一个习惯。它是你的远程代码库的别名，但是你可以用其他任何你喜欢的词。你甚至可以有多个远程代码库，你只需要给它们起不同的别名。

之后，你想要推送你的本地代码库的主干分支到你的远程代码库：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git push origin master</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

如果你使用Bitbucket，在这时，你会被请求输入你的密码。照做，你的本地代码库会被推送到你的远程代码库上。

##  ##

 

## 取得远程代码库的一份本地拷贝 ##

如果你还没有一份远程代码库的本地版本（例如，如果你在另一台机器上开始工作，这台机器上还没有用过这个项目），你首先需要拷贝（clone）它。去到你的代码库想要拷贝到的文件夹下，并发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git clone https:</code>
      <code>//your_username</code>
      <code>@bitbucket.org</code>
      <code>/your_username/name_of_remote_repository</code>
      <code>.git</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

另一方面，如果你已经在本地的项目上工作了，只是想从远程代码库上取得它最新的版本，移动到项目的根目录下，并发送：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git pull origin master</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

##  ##

 

## 别名 ##

Git允许你为你常用的命令创建快捷方式（别名）。例如，如果你不想每次都输入git commit -m “some comment”，而是输入git c “some comment”，你可以向你的git全局配置里添加一个别名来实现，像这样：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.c </code>
      <code>'commit -m'</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

这是我使用的别名列表：

<table>
 <tbody>
  <tr>
   <td>
    <div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.c </code>
      <code>'commit -m'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.co </code>
      <code>'checkout'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.cob </code>
      <code>'checkout -b'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.br </code>
      <code>'branch'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.m </code>
      <code>'merge'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.a </code>
      <code>'add .'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.s </code>
      <code>'status'</code>
     </div>
     <div>
      <code>git config --global </code>
      <code>alias</code>
      <code>.dbr </code>
      <code>'branch -d'</code>
     </div>
    </div></td>
  </tr>
 </tbody>
</table>

##  ##

 

## 进一步 ##

当然，还有比这些更多的Git内容。如果你想要更了解Git，我推荐官方文档和教程，你可以在[http://git-scm.com/documentation][http_git-scm.com_documentation]找到。

 

原文链接： [Nico][]   翻译： [ 伯乐在线 ][Link 5] \- [cjpan][]  
译文链接： [http://blog.jobbole.com/53573/][http_blog.jobbole.com_53573]  
\[ **转载必须在正文中标注并保留原文链接、译文链接和译者等信息。**\]  



[BitBucket]: https://bitbucket.org/
[Link 1]: http://git-scm.com/download/win
[Link 2]: http://code.google.com/p/git-osx-installer/downloads/list?can=3
[Link 3]: http://book.git-scm.com/2_installing_git.html
[Github]: https://github.com/
[Link 4]: http://blog.nicoschuele.com/?p=217
[http_git-scm.com_documentation]: http://git-scm.com/documentation
[Nico]: http://blog.nicoschuele.com/?p=219
[Link 5]: http://blog.jobbole.com/
[cjpan]: http://blog.jobbole.com/author/cjpan/
[http_blog.jobbole.com_53573]: http://blog.jobbole.com/53573/