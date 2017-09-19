# OSG学习笔记

+ osg核心库

  + osg库。基本数据类，负责基本场景，构造节点，向量矩阵计算等等。

  + osgUtil库。工具类，负责场景优化、遍历、裁剪、统计等等。

  + osgDB库。数据的读写库，负责场景数据读写操作。

  + osgViewer库。视窗系统，负责提供跨平台的窗口库。
+ osg工具库（NodeKit）
  + osgFX库。特殊效果节点工具，用于渲染特效节点、异性光照特效、凹凸纹理、卡通渲染特效等等。
  + osgParticle库。粒子系统节点工具。用于模拟各种天气或自然现象，如雨、雪、爆炸等。
  + osgSim库。虚拟仿真效果节点工具。用于特殊渲染，如地形高程图、DOF变换节点等。
  + osgTerrain库。生成地形数据的节点工具。用于渲染高程数据，如TIF、IMAGE等格式。
  + osgText库。文字节点工具。用于向场景添加文字信息。
  + osgShadow库。阴影节点工具。用于向场景添加阴影、提高真实性。



## 场景

Node、Group、Geode、Drawable

+ Node 场景图中的基类。
+ Group 用于场景的中间节点，负责划分组。
+ Geode （Geometry Node）几何体节点，场景中的叶子节点，负责存放渲染的几何体（osg::Drawable）。
+ Drawable 具体几何体的虚基类。

```
		|---- Group <----- Geode
Node <--|
		|---- Drawable
```

### geode

几何体节点负责保存几何体信息以便渲染，所有需要渲染的几何体都要和Geode节点关联。在Geode类中，提供`addDrawable`函数负责添加几何体信息。

```
Geode <--- Billboard
```

+ #### billboard

Billboard 布告板类，负责实现公告板的几何节点。`setMode`函数用来设置布告板的模式，包括：

```c++
enum Mode
{
	POINT_ROT_EYE,	 //绕视点
  	POINT_ROT_WORLD, //绕世界坐标
  	AXIAL_ROT		 //绕轴
}
```

如果绕轴，需要通过`setAxis`设置旋转轴。

### group

组节点允许用户程序为其添加任意数量的子节点，子节点仍可以为组节点。作为基类，派生出很多实用的节点类。

```
		  |--- Transform 
		  | 
		  |--- Switch
		  |
Group <-- |--- LOD
		  |
		  |--- OccluderNode
		  |
		  |--- CoordinateSystemNode
```

+ #### Transform 

  Transform 节点变换类，主要负责计算常见矩阵运算。该类作为基类，实现了很多常用的转换类：

  ```
  			   |--- AutoTransform
  			   |
  Transform <--- |--- PositionAttitudeTransform
  			   |
  			   |--- MatrixTransform
  ```

  + ##### AutoTransform

    自动转换类，负责将节点自动对齐摄像机或者屏幕。

    ```c++
    enum AutoRotateMode
    {
    	NO_ROTATION,		//无旋转
    	ROTATE_TO_SCREEN,	//自动朝向屏幕
    	ROTATE_TO_CAMERA	//自动朝向相机
    }
    ```

    使用`setAutoRotateMode`设置对齐方式，可以通过`setMinimumScale`和`setMaximumScale`设置缩放比例范围。

  + ##### PositionAttitudeTransform

    位置变换类，负责变换模型的位置、原点、姿态等属性。可以使用如下函数：

    + setPosition	//设置位置

    + getPosition    //获取位置

    + setAttitude    //设置姿态属性，参数为四元数

    + getAttitude    //获取姿态属性

    + setScale         //设置缩放级别

    + getScale         //获取缩放级别

    + setPivotPoint  //设置原点位置（模型变换基于原点）

    + getPivotPoint  //获取原点位置

  + ##### MatrixTransform

    矩阵变换类，负责场景矩阵变换、矩阵运算、坐标系变换等操作。

    + setMatrix 设置变换矩阵
    + getMatrix 获取变换矩阵
    + preMult 矩阵左乘
    + postMult 矩阵右乘
    + getInverseMatrix 获取逆矩阵


+ #### switch

  switch 开关节点类。主要负责是否渲染节点的控制，可以跳过指定的节点，优化当前的渲染性能。

  + setNewChildDefaultValue 设置新增子节点的默认值
  + getNewChildDefaultValue 获取新增节点的默认值
  + setValue 设置索引pos的值
  + getValue 获取索引pos的值
  + setChildValue 设置节点的值
  + getChildValue 获取节点的值
  + setAllChildrenOff 设置所有节点不显示
  + setAllChildrenOn 设置所有节点显示
  + setSingleChildOn 设置索引pos节点显示


+ #### LOD

  细节层次节点类。主要负责控制不同层次下的物体渲染，基本思想，当视点靠近物体的时候显示详细模型，当视点远离物体的时候显示简化的模型。

  ```
  		|----PagedLOD
  LOD <---|
  		|----Impostor
  ```

  LOD的计算方法主要是根据距离物体中心的控制，包括两个设定，一是范围控制方式，二是中心位置的设置。

  前者包括两种模式：

  ```c++
  enum RangeMode
  {
    	DISTANCE_FROM_EYE_POINT,	//根据距离视点距离
    	PIXEL_SIZE_ON_SCREEN		//根据屏幕像素的大小
  }
  ```

  后者包括两种模式：

  ```c++
  enum CenterMode
  {
    	USE_BOUNDING_SPHERE_CENTER,	//距离包围盒中心
    	USER_DEFINED_CENTER			//自定义中心，需要调用setCenter()设置
  }
  ```

  + PagedLOD

    ​分页细节层次节点。负责实现动态分页加载。可以通过setFileName设置相应级别显示的内容，使用setRange控制级别的范围。

  + Impostor

    ​替代节点。负责使用纹理渲染替代模型渲染，它通过从当前视点将一个复杂物体对象绘制成一幅图像纹理，然后将图像纹理映射到布告板上。使用setImpostorThreshold控制距离参数。


+ #### OccluderNode

  遮挡裁剪节点。如果自定义的遮挡形状能够将观察者的视线和场景的某个节点隔离开，那么遮挡节点将负责隐去这个节点（除非该节点是遮挡节点的子节点）。通过setOccluder设置遮挡面，该平面需要为凸多边形。

+ #### CoordinateSystemNode

  坐标系节点类。主要使场景中的对象和坐标系进行关联，常见的坐标系统：WKT、PROJ4、USGS等。

## 几何体

创建几何体常用的手段有三种：一是使用松散封装的OpenGL绘图基元；二是使用OSG中基本的几何体；三是使用文件导入场景模型。

### 向量

Vec2 一般用来保存纹理坐标。

Vec3 一般用来保存顶点、法线坐标。

Vec4 一般用来保存颜色信息。

三者还存在相对应的数组形式，如：Vec2Array、Vec3Array和Vec4Array。

### PrimitiveSet

该类封装了OpenGL绘制的基本图元信息，主要用于指定几何体如何进行顶点绘制，以及绘制所需要的顶点信息数据的位置。改类并不保存具体的顶点数据，只是指定绘制方法，具体的数据保存在几何体（Geometry）本身。

该类有很多派生类：

```
				 |---- DrawArrays
				 |
PrimitiveSet <---|---- DrawElements
				 |
				 |---- DrawArrayLengths
```

DrawArrays 主要封装了OpenGL的顶点数组绘制`glDrawArrays()`命令。

DrawElements 主要封装了OpenGL的索引绘制`glDrawElements`命令。

DrawArrayLengths主要是进行多次绘制，即多次调用`glDrawArrays()`，并且每次绘制的长度和范围并不相同。

osg内部虽然提供了OpenGL一样的顶点机制，但是内部使用的渲染方式会有一定的区别，根据渲染环境的不同，可能采用顶点、顶点数组、显示列表、或者glBegin/glEnd来渲染几何体。集成自Drawable类的对象，默认会使用显示列表绘制。可以使用setUseDisplayList进行切换。

### Geometry

几何体类存放相应几何体的具体数据，包括顶点、法线、纹理等信息，并且可以指定这些数据所表达的图元信息，按照相应的PrimitiveSet进行渲染。

该类常用的函数包括：

+ setVertexArray	//指定顶点数组
+ getVertexArray
+ setColorArray	//指定颜色数组
+ getColorArray
+ setNormalArray	//指定法线数组
+ getNormalArray

该类还存枚举变量 osg::Array::Binding，用来设置数据绑定的方式：

```c++
BIND_OFF   					//不启用绑定
BIND_OVERALL   				//用一条法向绑定所有的顶点（顶点数组中）
BIND_PER_PRIMITIVE_SET   	//为每个图元集绑定一条法线（法线的个数要与图元集的个数一致）
BIND_PER_PRIMITIVE 			//为每个图元绑定一条法线（法线的个数要与图元的个数一致）
BIND_PER_VERTEX  			//为每个顶点绑定一条法线（此时法线的个数要与顶点的个数一致）
```

使用addPrimitiveSet进行设置基本图元的显示设置。

### Shape

几何体类，包括osg中常用的几何体。

```c++
osg::Box			//正方形
osg::Capsule		//太空舱
osg::Cone			//椎体
osg::Cylinder		//柱体
osg::HeightField	//高度图
osg::InfinitePlane	//无限平面
osg::Sphere			//球体
osg::TriangleMesh	//三角片
```

osg预定义的几何体如果显示，需要和drawable进行关联，而ShapeDrawable就是完成这个工作的类。

该类和Geometry一样派生自类drawable，支持geode对象直接关联。该类内部提供一个关联osg::Shape的方法，如下：

```
ShapeDrawable (Shape *shape, TessellationHints *hints=0)
```

第一个参数就是Shape对象本身，第二个参数是细化对象。



## NodeVisitor类

节点访问类。使用方式非常简单，重载NodeVisitor类，实现apply方法，添加相应的功能，然后调用accept方法关联想要遍历的节点。traverse函数可以继续进行下一层的节点遍历。

遍历的类型：

```c++
enum VisitorType
{
	NODE_VISITOR	//节点访问器
	UPDATE_VISITOR	//更新访问器
	EVENT_VISITOR	//事件访问器
	COLLECT_OCCLUDER_VISITOR	//遮挡访问器
	CULL_VISITOR	//拣选访问器
}
```

遍历的方式：

```C++
enum TraversalMode
{
	TRAVERSE_NONE		//仅传递到当前节点
	TRAVERSE_PARENTS	//传递给当前节点及其父节点
	TRAVERSE_ALL_CHILDREN	//传递给场景所有节点和其子节点
	TRAVERSE_ACTIVE_CHILDREN	//传递给场景中所有的活动节点和其子节点
}
```

## osg环境变量

+ OSG_CONFIG_FILE	viewer配置文件
+ OSG_THREADING     线程模型
  + SingleThreaded
  + CullDrawThreadPerContext
  + DrawThreadPerContext
  + CullThreadPerCameraDrawThreadPerContext
+ OSG_SCREEN   屏幕数
+ OSG_WINDOW  窗口显示 x y width height
+ OSG_RUN_FRAME_SCHEME
  + ON_DEMAND
  + CONTINUOUS (默认)
+ OSG_RUN_MAX_FRAME_RATE



+ OSG_DISPLAY_TYPE						//显示器类型
  + MONITOR							//监视器 (默认)
  + POWERWALL                                                 //威力墙
  + REALITY_CENTER                                          // 虚拟实境中心
  + HEAD_MOUNTED_DISPLAY                         //头盔显示器



+ OSG_STEREO_MODE						//立体显示模型
  + QUAD_BUFFER                                              //四方体缓存
  + ANAGLYPHIC                                                 //互补色（默认）
  + HORIZONTAL_SPLIT                                      //水平分割
  + VERTICAL_SPLIT                                               //垂直分割
  + LEFT_EYE                                                            //左眼
  + RIGHT_EYE                                                            //右眼
  + HORIZONTAL_INTERLACE                              //水平交错
  + VERTICAL_INTERLACE                                         //垂直交错
  + CHECKERBOARD                                                  //棋盘交错(用于DLP显示器)


+ OSG_STEREO
  + OFF
  + ON
+ OSG_EYE_SEPARATION
+ OSG_SCREEN_WIDTH
+ OSG_SCREEN_HEIGHT
+ OSG_SCREEN_DISTANCE
+ OSG_SPLIT_STEREO_HORIZONTAL_EYE_MAPPING
  + LEFT_EYE_LEFT_VIEWPORT
  + LEFT_EYE_RIGHT_VIEWPORT
+ OSG_SPLIT_STEREO_HORIZONTAL_SEPARATION
+ OSG_SPLIT_STEREO_VERTICAL_EYE_MAPPING
  + LEFT_EYE_TOP_VIEWPORT
  + LEFT_EYE_BOTTOM_VIEWPORT
+ OSG_SPLIT_STEREO_AUTO_ADJUST_ASPECT_RATIO
  + OFF
  + ON
+ OSG_SPLIT_STEREO_VERTICAL_SEPARATION
+ OSG_MAX_NUMBER_OF_GRAPHICS_CONTEXTS
+ OSG_COMPIlE_CONTEXTS
  + OFF
  + ON
+ OSG_SERIALIZE_DRAW_DISPATCH
  + OFF
  + ON
+ OSG_USE_SCENEVIEW_FOR_STEREO
  + OFF
  + ON
+ OSG_NUM_DATABASE_THREADS
+ OSG_NUM_HTTP_DATABASE_THREADS
+ OSG_MULTI_SAMPLES
+ OSG_TEXTURE_POOL_SIZE
+ OSG_BUFFER_OBJECT_POOL_SIZE

....

osg/DisplaySettings.cpp    readEnvironmentalVariables()函数

## osg自带操作器

osgGA::StateSetManipulator:该事件响应类的功能是对渲染状态进行控制，openGL的渲染管线使用了状态机的机制，此事件响应类对状态进行控制体现在当用户按w键时，可在体线点三种模式下进行切换。按1，照明与非照明切换；b是否开启背面剔除模式切换；t在是否开启纹理的情况下切换。

osgViewer::ThreadingHandler：该事件响应的功能是改变线程模式，也可以改变线程同步点的位置。按m键对线程模式切换；e键对同步点位置进行切换。

WindowSizeHandler：对窗口的大小和分辨率进行改变，f键全屏；shift+<或者>对窗口分辨率进行调整。

HelpHandler：h键显示帮助信息。从osg::ArgumentParser类传入，可继承该类自定义帮助信息。

RecordCameraPathHandler：对当前Camera所经过的路径进行记录，实现录像与回放功能，z启动记录；拖动窗口按大写Z，回放。改变环境变量OSG_RECORD_CAMERA_PATH_FPS可以改变帧速。默认25。

 LODScaleHandler：通过*与/热键（+sgift？）改变当前渲染的LOD级别。

ScreenCaptureHandler：输出当前屏幕的截屏信息到当前文件夹，c键截屏，大写C停止。生成screen_shot_*_X.jpg.

## GIS 相关知识

### 1. 大地测量与地图制图的基本原理

地球是一个自然表面极其复杂与不规则的椭球体，而地图是在平面上描述各种制图现象，如何建立地球表面与地图平面的对应关系？为解决这一问题，人们引入大地体的概念。

大地体是由**大地水准面**包围而成。大地水准面是假定在重力作用下海水面静止时的平均水面，并设想此面穿过大陆与岛屿，连续扩展形成处处与铅垂线成正交的闭合曲面。由于地壳内部物质密度分布不均匀，大地水准面也有高低起伏。虽然此高低起伏已经不大，比地球自然表面规则得多，但仍不能用简单的数学公式表示。为了测量成果的计算和制图的需要，人们选用一个同大地体相近的可以用数学方法来表达的旋转椭球体来代替，简称**地球椭球体**。它是一个规则的曲面，是测量和制图的基础。地球自然表面点位坐标系的确定包括两个方面的内容：一是地面点在地球椭球体面上的**投影位置**，采用***地理坐标系***；二是地面点至大地水准面上的**垂直距离**，采用***高程系***。

### 2. 经纬度

+ 经度

  **经度**是地球面上的一点与两极的连线与0度经线所在平面的夹角。0度经线叫**本初子午线**，这条线是人为规定的，英国的制图学家使用经过**伦敦格林尼治天文台**的子午线作为起点。

  经度是通过某地的**经线面**与**本初子午线**所成的二面角。本初子午线以东叫做**东经**，使用字母`E`表示，为正；本初子午线以西叫做**西经**，使用字母`W`表示，为负。经度的每一度被分为60角分，每一分被分为60秒。例如：`23° 27′ 30"`

+ 纬度

  **纬度**可分为天文纬度、大地纬度和地心纬度。地心纬度是指某点与地球球心的连线和地球赤道面所成的线面角，大地纬度是指某地地面法线对赤道面的夹角,天文纬度指该地铅垂线方向对赤道面的夹角。通常纬度是只大地纬度，数值为0到90度之间。0纬度叫做**赤道**，赤道以被叫做**北纬**，使用字母`N`表示，为正；赤道以南叫做**南纬**，使用字母`S`表示，为负。

### 3. 大地坐标系
大地坐标系是根据其原点的位置不同，分为**地心坐标系**和**参心坐标系**。

***地心坐标系*** 的原点和**地球质心**重合。

***参心坐标系*** 的原点与某地区或者国家采用的**参考椭球中心**重合。目前，1954年国家建立的北京坐标系、1980年西安坐标系、新1954年北京坐标系都是参心坐标系。

参心坐标系通常分为**参考空间直角坐标系**（以XYZ为其坐标元素）和**参心大地坐标系**（以BLH为其元素）。

+ BLH

  B代表大地纬度，L代表大地精度，H代表大地高度。

+ XYZ

  X轴与赤道面和零子午线的交线重合，向东为正。Z轴与旋转椭球的短轴重合，向北为正。Y轴与XZ平面构成右手系。

### 4. 投影

+ UTM投影。全称为：通用横轴墨卡托投影。

+ 高斯-克吕格投影。又名"等角横切椭圆柱投影”。

  ​

WGS-84坐标系

WGS-84坐标系是一种国际上采用的地心坐标系。坐标原点为地球质心，其地心空间直角坐标系的Z轴指向国际时间局（BIH）1984.0定义的协议地极（CTP）方向，X轴指向BIH1984.0的协议子午面和CTP赤道的交点，Y轴与Z轴、X轴垂直构成右手坐标系，称为1984年世界大地坐标系。这是一个国际协议地球参考系统（ITRS），是目前国际上统一采用的大地坐标系。

#### EPSG 

#### http://blog.csdn.net/zhangrongde/article/details/8979523

http://www.cnblogs.com/jackdong/archive/2010/12/20/1911558.html

http://blog.csdn.net/breaker892902/article/details/9069581


开发的GIS **标准**：

+ OGC (开放地理空间信息联盟)
  + WKT/WKB 空间数据规范，用于表示矢量几何对象、空间参照系统及空间参照系统之间的转换。
+ ISO/TC211(国际标准化组织地理信息技术委员会)



好文：

http://blog.csdn.net/rsyaoxin/article/details/9122981





## osgEarth 驱动加载

TileSource.cpp 749行

```C++
    std::string driverExt = std::string( ".osgearth_" ) + driver;
    result = dynamic_cast<TileSource*>( osgDB::readObjectFile( driverExt, dbopt.get() ) );
    if ( !result )
    {
        OE_WARN << LC << "Failed to load TileSource driver \"" << driver << "\"" << std::endl;
    }

    OE_DEBUG << LC << "Tile source Profile = " << (result->getProfile() ? result->getProfile()->toString() : "NULL") << std::endl;

```

Registry.cpp 1117行

```C++
ReaderWriter::ReadResult Registry::read(const ReadFunctor& readFunctor)
{
    for(ArchiveExtensionList::iterator aitr=_archiveExtList.begin();
        aitr!=_archiveExtList.end();
        ++aitr)
    {
        std::string archiveExtension = "." + (*aitr);

        std::string::size_type positionArchive = readFunctor._filename.find(archiveExtension+'/');
        if (positionArchive==std::string::npos) positionArchive = readFunctor._filename.find(archiveExtension+'\\');
        if (positionArchive!=std::string::npos)
        {
            std::string::size_type endArchive = positionArchive + archiveExtension.length();
            std::string archiveName( readFunctor._filename.substr(0,endArchive));
            std::string fileName(readFunctor._filename.substr(endArchive+1,std::string::npos));
            OSG_INFO<<"Contains archive : "<<readFunctor._filename<<std::endl;
            OSG_INFO<<"         archive : "<<archiveName<<std::endl;
            OSG_INFO<<"         filename : "<<fileName<<std::endl;

            ReaderWriter::ReadResult result = openArchiveImplementation(archiveName,ReaderWriter::READ, 4096, readFunctor._options);

            if (!result.validArchive()) return result;

            osgDB::Archive* archive = result.getArchive();

            //if valid options were passed through the read functor clone them
            //otherwise make new options
            osg::ref_ptr<osgDB::ReaderWriter::Options> options = readFunctor._options ?
                readFunctor._options->cloneOptions() :
                new osgDB::ReaderWriter::Options;

            options->setDatabasePath(archiveName);

            std::auto_ptr<ReadFunctor> rf(readFunctor.cloneType(fileName, options.get()));

            result = rf->doRead(*archive);

            if (rf->isValid(result))
            {
                OSG_INFO<<"Read object from archive"<<std::endl;
                return result;
            }
            OSG_INFO<<"Failed to read object from archive"<<std::endl;
        }
    }

    // record the errors reported by readerwriters.
    typedef std::vector<ReaderWriter::ReadResult> Results;
    Results results;

    // first attempt to load the file from existing ReaderWriter's
    AvailableReaderWriterIterator itr(_rwList, _pluginMutex);
    for(;itr.valid();++itr)
    {
        ReaderWriter::ReadResult rr = readFunctor.doRead(*itr);
        if (readFunctor.isValid(rr)) return rr;
        else results.push_back(rr);
    }

    // check loaded archives.
    AvailableArchiveIterator aaitr(_archiveCache, _archiveCacheMutex);
    for(;aaitr.valid();++aaitr)
    {
        ReaderWriter::ReadResult rr = readFunctor.doRead(*aaitr);
        if (readFunctor.isValid(rr)) return rr;
        else
        {
            // don't pass on FILE_NOT_FOUND results as we don't want to prevent non archive plugins that haven't been
            // loaded yet from getting a chance to test for the presence of the file.
            if (rr.status()!=ReaderWriter::ReadResult::FILE_NOT_FOUND) results.push_back(rr);
        }
    }

    // now look for a plug-in to load the file.
    std::string libraryName = createLibraryNameForFile(readFunctor._filename);
    if (loadLibrary(libraryName)!=NOT_LOADED)
    {
        for(;itr.valid();++itr)
        {
            ReaderWriter::ReadResult rr = readFunctor.doRead(*itr);
            if (readFunctor.isValid(rr)) return rr;
            else results.push_back(rr);
        }
    }

    //If the filename contains a server address and wasn't loaded by any of the plugins, try to find a plugin which supports the server
    //protocol and supports wildcards. If not successfully use curl as a last fallback
    if (containsServerAddress(readFunctor._filename))
    {
        ReaderWriter* rw = getReaderWriterForProtocolAndExtension(
            osgDB::getServerProtocol(readFunctor._filename),
            osgDB::getFileExtension(readFunctor._filename)
        );

        if (rw)
        {
            return readFunctor.doRead(*rw);
        }
        else
        {
            return  ReaderWriter::ReadResult("Warning: Could not find the .curl plugin to read from server.");
        }
    }

    if (results.empty())
    {
        return ReaderWriter::ReadResult("Warning: Could not find plugin to read objects from file \""+readFunctor._filename+"\".");
    }

    // sort the results so the most relevant (i.e. ERROR_IN_READING_FILE is more relevant than FILE_NOT_FOUND) results get placed at the end of the results list.
    std::sort(results.begin(), results.end());
    ReaderWriter::ReadResult result = results.back();

    if (result.message().empty())
    {
        switch(result.status())
        {
            case(ReaderWriter::ReadResult::FILE_NOT_HANDLED): result.message() = "Warning: reading \""+readFunctor._filename+"\" not supported."; break;
            case(ReaderWriter::ReadResult::FILE_NOT_FOUND): result.message() = "Warning: could not find file \""+readFunctor._filename+"\"."; break;
            case(ReaderWriter::ReadResult::ERROR_IN_READING_FILE): result.message() = "Warning: Error in reading to \""+readFunctor._filename+"\"."; break;
            default: break;
        }
    }

    return result;
}

```

