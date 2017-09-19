# 为什么treepanel无法显示数据？ #

首先确认属性是否存在问题，大小写是否写错，查看调试信息，是否存在报错情况，大致上除了上边说的常出现的错误有几种：


一、控制台报错，输出如下面的错误：`Uncaught TypeError: Cannot read property 'getRootNode' of undefined`。


这种情况基本上愿意应该归结于treeStore的问题上。 因为在treepanel属性中，store属性为必须，store不存在或者初始化失败都会出现类似的错误。如何排查？

1. 排查方法

	* 如果你使用Ext.create的手法，请检测大小写，属性等基本信息。 例如：

			//直接设置为store对象。
			store: Ext.create('Ext.data.TreeStore', {
				....
			});

	* 如果你单独定义一个store类，请检测文件是否加载并初始化。 例如：

			//文件 ManageTree.js，请检测该文件ManageTree.js是否加载。
			Ext.define('App.store.layout.ManageTree', {
				extend: 'Ext.data.TreeStore',
			
				....
			});
			
			//设置store属性。
			store: 'layout.ManageTree'
	
	* 如果你没有设置store属性，可以简单的设置root属性来实现简单树的加载。例如：
	
			//设置root属性，store属性可以忽略。
			root: {
		        expanded: true,
		        children: [
		            { text: "detention", leaf: true },
		            { text: "homework", expanded: true, children: [
		                { text: "book report", leaf: true },
		                { text: "algebra", leaf: true}
		            ] },
		            { text: "buy lottery tickets", leaf: true }
		        ]
		    }

	* 如果你即设置了root，也设置了store，那需要同时检测两者是否出现差错。需要注意及时正确store中加载的东西也会被root覆盖。


	查看treepanel组件的构造函数源码，可以发现这样的代码：

		//如果为store实例
		var store = me.store;
		
		//如果是字符串
		if (Ext.isString(store)) {
		    store = me.store = Ext.StoreMgr.lookup(store);
		//如果为空或者不是store对象
		} else if (!store || Ext.isObject(store) && !store.isStore) {
		    store = me.store = new Ext.data.TreeStore(Ext.apply({
		        root: me.root,
		        fields: me.fields,
		        model: me.model,
		        folderSort: me.folderSort
		    }, store));
		//如果存在root属性
		} else if (me.root) {
		    store = me.store = Ext.data.StoreManager.lookup(store);
		    store.setRootNode(me.root);
		    if (me.folderSort !== undefined) {
		        store.folderSort = me.folderSort;
		        store.sort();
		    }
		}
		
		//一般报错位置
		me.viewConfig = Ext.apply({
		    rootVisible: me.rootVisible,
		    animate: me.enableAnimations,
		    singleExpand: me.singleExpand,
		    node: store.getRootNode(),
		    hideHeaders: me.hideHeaders
		}, me.viewConfig);

	和上面的情况一一对应。从代码中可以发现其实查找store的手段很简单，就是使用函数`Ext.data.StoreManager.lookup`和`Ext.StoreMgr.lookup`。查看文档会发现二者其实是一个东西： `Ext.data.StoreManager`。


	查看文档发现Ext.data.StoreManager是一个单例，主要是存储创建过并且存在storeId的store对象。为了验证，使用如下代码创建一个不含有storeId的对象：

		var store = Ext.create('Ext.data.TreeStore', {
		    root: {
		        expanded: true,
		        children: [
		            { text: "detention", leaf: true },
		            { text: "homework", expanded: true, children: [
		                { text: "book report", leaf: true },
		                { text: "algebra", leaf: true}
		            ] },
		            { text: "buy lottery tickets", leaf: true }
		        ]
		    }
		});

	使用`Ext.StoreManager`查看内部项，发现刚刚创建的`store`对象并没有加入到`Ext.StoreManager`中，接着创建一个包含`storeId`的对象。

		var store = Ext.create('Ext.data.TreeStore', {
			storeId: 'identifier',
		    root: {
		        expanded: true,
		        children: [
		            { text: "detention", leaf: true },
		            { text: "homework", expanded: true, children: [
		                { text: "book report", leaf: true },
		                { text: "algebra", leaf: true}
		            ] },
		            { text: "buy lottery tickets", leaf: true }
		        ]
		    }
		});

	查看`Ext.StoreManager`，果然发现了刚刚创建的store对象。并且在创建一样的storeId对象，并不会发生错误，但是后创建的会覆盖前面创建的对象。

	
	总结：如果不使用`root`属性的方法加载`treepanel`对象，问题的排查方法很简单，只需要查看你输入的`store`对象是否存在，只需要使用`Ext.StoreManager.lookup`函数在控制台验证即可(注意大小写）。

2. 解决方法。

	知道了问题所在，解决方法有很多种，主要需要注意两点：
	
	- 需要注意文件需要加载成功（定义store类的文件）。
	- 需要注意大小写、拼写错误。
	- 需要注意创建的store要成功注册。



	这里介绍几种常用方法：

	* 在controller中使用stores属性加载需要使用的文件,并注册。（全自动）

		我们查看controller代码就会发现，stores属性传递的数组对象的内部store全部会自动注册到Ext.StoreManager。所以，只要使用该属性即可一劳永逸，除非笔误，或者文件不存在什么的。自动生成的storeId就是传递的store名称。源码：

		    getStore: function(name) {
		        var storeId, store;
		
		        storeId = (name.indexOf('@') == -1) ? name : name.split('@')[0];
		        store   = Ext.StoreManager.get(storeId);
		
		        if (!store) {
		            name = Ext.app.Controller.getFullName(name, 'store', this.$namespace);
		
		            if (name) {
		                store = Ext.create(name.absoluteName, {
		                    storeId: storeId
		                });
		            }
		        }
		
		        return store;
		    }

	* 不使用继承，直接使用Ext.create函数创建store对象。