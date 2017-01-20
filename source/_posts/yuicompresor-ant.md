title: YUICompressor+ANT压缩Java Web工程前端js/css文件
date: 2016-11-18 10:24:00
tags:
- yuicompressor
- ant
categories:
- 技术研发

---

最近team里有个jsp工程想要做一些性能上的优化， 需要对工程里的前端部分（主要是js和css）做一些压缩（这是一个老旧重的工程，重构的成本非常大，也没有人力，只能一步一步去优化）。想到与传统ANT工具结合的比较好的yuicompressor。

<!-- more -->

### yuicompressor下载 ###

下载地址：[http://maven.aliyun.com/nexus/service/local/repositories/central/content/com/yahoo/platform/yui/yuicompressor/2.2.5/yuicompressor-2.2.5.jar?spm=0.0.0.0.jwwQsz&file=yuicompressor-2.2.5.jar](http://maven.aliyun.com/nexus/service/local/repositories/central/content/com/yahoo/platform/yui/yuicompressor/2.2.5/yuicompressor-2.2.5.jar?spm=0.0.0.0.jwwQsz&file=yuicompressor-2.2.5.jar)

这里推荐下阿里云的maven镜像，下载速度很快。maven repository在国内经常被墙。


### yuicompressor结合ant ###

yuicompressor做的东西和gulp类似，可以对js/css文件做压缩混淆，但在使用的过程中还是觉得不是很灵活方便，比如说它构建的目标文件必须在构建前先创建好，这就要求我们在做构建之前先做一次copy操作。

我要实现的目的很简单，在执行ant任务时，编译java工程之前对工程里的js/css文件做混淆压缩，最后打包成war包。所以我希望压缩后的文件能覆盖旧的文件，这样一来我不需要去修改html/jsp文件中对静态资源的引用。


折腾一番后，对js或css文件的压缩的步骤大概总结为：

1. 创建一个编译目标目录，将源目录下的文件复制到目标目录

2. 将源目录中的文件压缩编译后保存到目标目录

3. 将目标目录移动覆盖源目录


以下是ant中压缩js和css的任务：
	
	<!--定义变量-->
	<property name="yuicompressor.jar" location="${web.dir}/WEB-INF/lib/yuicompressor-2.2.5.jar"/>
    <property name="js.src" location="${web.dir}/js/test_yui"/>
    <property name="js.build" location="${web.dir}/js/test_yui_build"/>
    <property name="css.src" location="${web.dir}/style"/>
    <property name="css.build" location="${web.dir}/style_build"/>

	<!--压缩js-->
    <target name="js.compress">
        <!-- START: 构建的目标文件必须存在，所以现复制 -->
        <delete dir="${js.build}"/>
        <mkdir dir="${js.build}" />
        <copy todir="${js.build}" includeEmptyDirs="true" overwrite="true" preservelastmodified="true">
            <fileset dir="${js.src}" >
                <exclude name="**/*.*" />
            </fileset>
        </copy>
        <!-- END: 构建的目标文件必须存在，所以现复制 -->

        <!-- START: js压缩混淆 -->
        <apply executable="java" parallel="false" verbose="true" dest="${js.build}" >
            <fileset dir="${js.src}">
                <include name="**/*.js" />
                <exclude name="**/*.min.js" />
            </fileset>
            <arg line="-jar"/>
            <arg path="${yuicompressor.jar}" />
            <arg line="--type js --charset UTF-8 -o" />
            <mapper type="glob" from="*.js" to="*.js" />
            <targetfile />
        </apply>
        <!-- END: js压缩混淆 -->

        <!-- START: 覆盖源目录 -->
        <move todir="${js.src}">
            <fileset dir="${js.build}"/>
        </move>
        <!-- END: 覆盖源目录 -->
    </target>
