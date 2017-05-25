### 开发安卓插件


#### IntelliJ Plugin开发
官方文档 

* [插件开发帮助文档](https://www.jetbrains.com/help/idea/2016.3/plugin-development-guidelines.html#d1348165e8)
* [IntelliJ 插件SDK文档](http://www.jetbrains.org/intellij/sdk/docs/basics/getting_started.html)
* [IntelliJ 平台SDK文档](http://www.jetbrains.org/intellij/sdk/docs/index.html)
* [IntelliJ API](http://grepcode.com/snapshot/repository.grepcode.com/java/ext/com.jetbrains/intellij-idea/11.0/)

**支持Gradle的第三方库**

[Gralde Tooling API](https://docs.gradle.org/current/userguide/embedding.html#sec:embedding_introduction)

将Gradle嵌入到工程中，并提供API让用户实现gradle的功能，包括执行并监控gradle的构建，查询构建的详细信息等。
examples：
			
			//创建连接
			BuildLauncher build = connection.newBuild();
			
			//任务名
            build.forTasks(taskName);
            //设置任务执行参数
            List<String> buildArgs = new ArrayList<String>();
            buildArgs.add("--parallel");
            buildArgs.add("--max-workers=8");
            buildArgs.add("--configure-on-demand");
            buildArgs.add("--offline");
            if (arguments != null && arguments.size() > 0) {
                for (int i = 0; i < arguments.size(); i++) {
                    buildArgs.add(arguments.get(i));
                }
            }
            build.withArguments(buildArgs.toArray(new String[]{}));
            //设置任务输出流
            ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
            build.setStandardOutput(outputStream);
            build.setStandardError(System.err);
            //添加监听事件
            build.addProgressListener((ProgressListener) progressEvent -> {
                String outResult = outputStream.toString();
                if (outResult != null && outResult.length() > 0) {
                    NotificationUtils.info(outResult, project);
                    outputStream.reset();
                }
            });
            //执行任务，添加回调
            build.run(resultHandler);

**Log的输出**

* 在eventLog中输出的Log：
 
 普通日志
 
		new NotificationGroup("Gradle sync", NotificationDisplayType.NONE, true).createNotification(infoMsg, MessageType.INFO).notify(project);
		
 错误日志(同事会在右下角弹气泡,balloon)
 
		new NotificationGroup("com.maihaoche.mazda", NotificationDisplayType.BALLOON, true).createNotification(infoMsg, MessageType.INFO).notify(project);
 

* 弹窗提示用户错误

		StatusBar statusBar = WindowManager.getInstance()
                .getStatusBar(DataKeys.PROJECT.getData(event.getDataContext()));
        JBPopupFactory.getInstance()
                .createHtmlTextBalloonBuilder(errorMsg, MessageType.ERROR, null)
                .setFadeoutTime(7500)
                .createBalloon()
                .show(RelativePoint.getCenterOf(statusBar.getComponent()),
                        Balloon.Position.atRight);


**数据的本地存储**

[官方文档](http://www.jetbrains.org/intellij/sdk/docs/basics/persisting_state_of_components.html)

* 存储数据

		Application级别： PropertiesComponent.getInstance().setValues(key, setValue)
		Project级别： PropertiesComponent.getInstance(Project).setValues(key, setValue)
* 读取数据

		Application级别： PropertiesComponent.getInstance().getValue(key, defaultValue)
		Project级别： PropertiesComponent.getInstance(Project).getValue(key, defaultValue)


**Swing 的UI**

[官方文档](https://docs.oracle.com/javase/tutorial/uiswing/components/index.html))




#### 构建和发布

**pulgin.xml 配置**

*  \<description>标签（介绍插件的说明文字）和 <change-notes> 标签(版本更新日志)。这两个标签需要添加英文描述，否则无法通过IntelliJ官方的插件审批。

* <idea-version>标签决定了插件支持的IntelliJ版本。例如

		<idea-version> since-build="162.2228.14"/>
这里的since-build后面对应的版本号不能超过运行该插件的[android studio对应的IntelliJ 的版本号](http://tools.android.com/build/studio/idea-tags).否则在AndroidStudio中安装该插件的时候，会报不兼容错误，既安装不了。

**插件的发布**

* 代码编写完毕，并且debug后，点击Build->Prepare Plugin Module 'xxx' For Deployment.会在根目录下生成 xxx.jar文件(如果包含了第三方库，会生成xxx.zip文件)。该文件可以直接在android studio中作为插件安装。

* 插件的发布。到[IntelliJ IDEA Plugins仓储](https://plugins.jetbrains.com/)官网注册账号，并创建新的项目，讲开发生成的jar或者zip文件上传。等待官方的审批。一般是在10个工作日左右。审批通过了之后才能在AndroidStudio中的"Browse repositories..."搜索并安装。



##### gradle脚本

[官方中文文档](https://dongchuan.gitbooks.io/gradle-user-guide-/)

[官方API](https://docs.gradle.org/3.3/javadoc/)

##### Groovy语言
[Groovy中文文档(IBM中国)](https://www.ibm.com/developerworks/cn/education/java/j-groovy/j-groovy.html)


##### XML

[官方文档](https://www.w3.org/TR/2013/WD-xinclude-11-20130115/#XML11)

[xml相对路径和调试方法](https://mail.gnome.org/archives/xml/2004-February/msg00354.html)

[xml复用xinclude](https://www.w3.org/TR/2013/WD-xinclude-11-20130115/) 

xinclude是xml中的一个语法标记。它的提供了在xml包含其他内容的功能。其他包含的内容可以为 文字片段，网络链接， xml内部的节点等等。

xinclude example：

	<model>
	<item id="1">I want this</item>
 	 <item id="2"/>
 	 <group>
 	   <xi:include xpointer="xpointer(/model/item[ id='1'])" xmlns:xi="http://www.w3.org/2003/XInclude"/>
 	 </group>
	</model>

几个常用的语法：
对资源进行定位的两个属性：

1. 属性 [xpointer](https://www.w3.org/TR/2003/REC-xptr-framework-20030325/) 为一套专门的资源定位语法
2. 属性 href 值为[XML资源定位符](https://www.w3.org/TR/2004/REC-xml11-20040204/#sec-external-ent)



  
  
