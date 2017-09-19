#Sencha Cmd

Sencha Cmd 是一个跨平台的命令行工具，从应用程序的创建到上线部署的整个生命周期，它提供了许多自动化的命令来帮助你完成。

Sencha Cmd提供了一组强大的工具集合：

+ Code Generation Tools： 代码生成工具可以生成整个应用程序并可以进行一些新的拓展。
+ JS Compiler: Javascript 编译器，它可以压缩你的javascript代码，减少应用程序部署后的加载时间。
+ Web Server： 提供一个轻量级的Web服务器帮助你本地开发。
+ Native Packaging： 用来支持Sencha Touch应用打包。
+ Package Management System：分布式包管理系统可以非常方便的集成一些其他人开发的模块，从Sencha Package Repository。
+ Build Scripts: 生成应用程序的构建脚本并提供before和after拓展点，帮助你自定义所需。
+ Tuning Tools： 代码调优工具。
+ Workspace Management：
+ Image Capture：
+ Flexible Configuration System:
+ Logging:
+ Third-party Software: 
+ Code Generation Hooks

##安装

+ JRE 1.7+(全部） 1.6+(大部分）
+ Ruby

##生成

生成一个应用骨架，使用下面的命令：

	sencha generate app -ext MyApp /path/to/MyApp

默认情况，改程序会下载Ext JS的商业试用版本来生成你的程序。如果你有一个其他版本的Extjs授权，你需要将其解压到你的硬盘的某个目录，然后用下面的命令来生成。

	sencha -sdk /path/to/ext generate app MyApp /path/to/MyApp

生成后的目录如下：

	.sencha/                        # Sencha 相关的文件
	    app/                        # Application 相关内容目录
	        sencha.cfg              # Application 配置文件
	        Boot.js                 # Private, 动态加载js和CSS
	        Microloader.js          # Loads app based on app.json content
	        build-impl.xml          # Standard application build script
	        *-impl.xml              # Implementations of various build phases
	        defaults.properties     # Default values and docs for build properties
	        ext.properties          # Build property values specific to Ext JS
	        *.defaults.properties   # Build properties by environment (e.g. "testing")
	        plugin.xml              # Application-level plugin for Sencha Cmd
	        codegen.json            # Data for merging generated code during upgrade
	    workspace/                  # Workspace-specific content (see below)
	        sencha.cfg              # Workspace configuration file for Sencha Cmd
	        plugin.xml              # Workspace-level plugin for Sencha Cmd
	
	ext/                            # A copy of the Ext JS SDK
	    cmd/                        # Framework-specific content for Sencha Cmd
	        sencha.cfg              # Framework configuration file for Sencha Cmd
	    packages/                   # Framework supplied packages
	        ext-theme-classic/      # Ext JS Theme Package for Classic
	        ext-theme-neptune/      # Ext JS Theme Package for Neptune
	        ...                     # Other theme and locale packages
	    src/                        # The Ext JS source
	    ...
	
	index.html                      # The entry point to your application
	app.json                        # Application manifest
	app.js                          # Launches the Application class
	app/                            # Your application's source code in MVC structure
	    model/                      # Folder for application model classes
	    store/                      # Folder for application stores
	    view/                       # Folder for application view classes
	        main/                   # Folder for the classes implementing the Main View
	            Main.js             # The Main View
	            MainModel.js        # The `Ext.app.ViewModel` for the Main View
	            MainController.js   # The `Ext.app.ViewController` for the Main View
	    Application.js              # The `Ext.app.Application` class
	
	packages/                       # Sencha Cmd packages
	
	build/                          # The folder where build output is placed


##构建你的应用

运行下面的命令，生成你的程序。

	sencha app build

这个命令执行后，会生成一些文件到build目录。

> 注意：执行该命令需要切换到程序的具体目录，如/path/to/MyApp。

##Sencha Cmd的web服务器

Sencha Cmd Web server允许从你的应用程序目录下的文件成为可以通过服务器访问的内容。使用下面的命令开启web服务。

	sencha web start

这条命令默认使用1841端口和当前目录。停止程序可以使用Ctrl+C，或在其他窗口使用下面的命令:

	sencha web stop

你可以访问该web服务器，通过下面的地址：

	http://localhost:1841/

你可以使用-port来指点其他端口。

	sencha web -port 8080 start

##拓展你的应用

这个sencha generate可以快速的帮助你生成一些常用的MVC组件，如控制器和模型：

	sencha help generate

你会看到下面的信息：

	Sencha Cmd v5.1.0.26
	sencha generate
	
	This category contains code generators used to generate applications as well
	as add new classes to the application.
	
	Commands
	  * app - Generates a starter application
	  * controller - Generates a Controller for the current application
	  * form - Generates a Form for the current application (Sencha Touch Specific)
	  * model - Generates a Model for the current application
	  * package - Generates a starter package
	  * profile - Generates a Profile for the current application (Sencha Touch Spec
	ific)
	  * theme - Generates a theme page for slice operations (Ext JS Specific)
	  * view - Generates a View for the current application (Ext JS Specific)
	  * workspace - Initializes a multi-app workspace

> 注意：执行下面的命令，需要你切换到程序所在目录。

##生成Models

加入一个model到你的程序，你需要切换到应用程序目录，然后执行下面的命令：

	cd /path/to/MyApp
	sencha generate model User id:int,name,email

这个命令增加一个叫做User的model类。其中包含三个字段。

> 注意字段间不要留空格，否则会出现错误。如果文件已存在，会覆盖原有文件。

##生成Views

加入一个view到应用程序：

	cd /path/to/MyApp
	sencha generate view foo.Thing

你将会看到生成如下文件：

	app/
    view/
        foo/                    # Folder for the classes implementing the new view
            Thing.js            # The new view
            ThingModel.js       # The `Ext.app.ViewModel` for the new view
            ThingController.js  # The `Ext.app.ViewController` for the new view

默认情况生成的view会继承Ext.panel.Panel，你可以使用-b来指定该view继承的基类，命令如下：

	cd /path/to/MyApp
	sencha generate view -base Ext.tab.Panel foo.Thing

> 注：上面的情况是Extjs5.0，使用Extjs4.2.3发现继承参数无法使用。默认继承`Ext.Component`。还有一点Ext4生成文件只是单一文件。

##生成Controllers

在Extjs5里面，这个控制器生成是不需要的，因为在生成view的时候，已经生成了一个继承自`Ext.app.ViewController`的控制器了。如果想要生成某个控制器，只需要如下命令：

	cd /path/to/MyApp
	sencha generate controller Central

生成的控制器继承自`Ext.app.Controller`。

##自定义构建过程



http://docs-origin.sencha.com/cmd/5.x/extjs/cmd_app.html
http://blog.csdn.net/sushengmiyan/article/details/38313537