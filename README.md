# android_webview
初衷：
1. 本项目是基于android webview，用于展示H5的android应用；
2. 最初想直接使用AgentWeb，但没找到具体使用说明，看代码发现代码结构有较大改进空间，便在参考网络代码和AgentWeb代码的同时精简为该项目，考虑到不同厂商机型的兼容性问题，且完整的兼容性测试覆盖成本过高，本项目的webview尽可能少的使用Android系统功能的同时，使用“在线真机测试网站”对相对复杂的功能进行较全面的测试；
3. 本项目很精简，关键功能代码仅几百行，无花式装X，无冗余套路，一眼看穿，便于二开手撸，深入灵魂的灵活。

吐槽与额外功能：
1. 习惯于文本编辑器撸码，命令行打包，无法忍受java的各种花式工具满天飞、强制安装IDE等等，本项目使用android-sdk和java-sdk内置命令完成编译、打包，未使用gradle/ant/maven打包工具，代码目录结构使用android-sdk命令生成，所以存在一些冗余文件，未作处理，强迫症人群可自行处理；
2. 一套代码，多端发布，是众多开发者努力的方向，但无论如何，为了获得更大的灵活性，平台差异始终直接或间接恶心着hybird开发者。据我所知的不少hybird平台，都在尝试在提供足够灵活性的同时掩盖平台差异，像AppCan、UniApp，有几点体会“首先，这些三方平台无论如何优秀，在财力投入和技术积累等各方面始终与Android和IOS的系统开发厂商实力相差较大，三方平台经常遇到文档不足或描述不精确、文档更新不及时、测试不完全、莫名其妙问题得不到解释等等；其次，三方平台能挺多久，使用三方平台积累的使用经验是否会直接报废？”，产生这些疑虑都有事实依据，本不想自己费力不讨好造这么个轮子，毕竟以前使用uniapp还是可以的，自从过了几个月啥都没动的uniapp项目再次打包发现就无法签名，搜索无果不了了之，这只是无法签名，什么时候打出的apk如果部分机型不兼容呢？引起重新思考，而这个轮子原则上是“求同存异”不为掩盖平台差异，只把相同的地方提取出来，若想得到平台特定功能，需自己去完善这些功能并把这些依附到提出的公共部分，因此代码量就很少了，使用平台功能越少也更利于测试全面覆盖及减少深度定制系统的兼容性触雷的可能。

目录结构：
 + ./android_webview/     -> 工程目录（本目录下的bat文件用于生成工程、编译打包apk，可用于工程下的所有项目，使用方式参考使用说明）。
    -  conf.bat            -> 配置文件，在其中设置打包生成的apk名称，为目录结构简单，该配置作用于整个工程（所有工程的apk都是这个名字），要生成不同的apk名称，打包前修改即可。
    -  env.bat             -> 配置文件，要配置成你本地的“android-sdk、java-sdk”的路径。（要配置到哪个版本或哪一层子路径，请按照本文件的原始配置举一反三）
    -  myjavac21.bat       -> 编译、打包工具，文件名中的21表示本文件的版本为2.1。
    -  new-project.bat     -> 创建新项目工具。
    +  webview/             -> 项目目录，同一工程目录下可有多个项目目录，用new-project.bat进行创建，本目录下的子目录结构和名称除res外可随意调整，但调整后须对myjavac21.bat中的路径进行相应修改
      +  src/                 -> 【new-project.bat自动生成】在路径下还会生成一个初始的继承自android.app.Activity类的java源文件（apk入口），在本项目中继续使用本目录存放java源代码
        + xxx/xxx/              ->代码目录下共3个文件“MainActivity.java、DefaultWebViewClient.java、DefaultWebChromeClient.java”，其中“DefaultWebViewClient.java”使用AgentWeb中的关键代码进行整理和修改，私以为这些源码有较大重构空间，目前重构了一部分，还未完全完成（代码功能是正常的，“基本”可以放心用）
      +  res/                 -> 【new-project.bat自动生成】android资源文件路径，可在其相应子目录增加些图片、布局等，android-sdk资源打包工具会使用它们，所以其子目录结构一般是固定的
        +  drawable-hdpi/
        +  drawable-ldpi/
        +  drawable-mdpi/
        +  drawable-xdpi/
        +  layout/
        +  values/
      +  bin/                 -> 【new-project.bat自动生成，该目录可删除，myjavac21.bat每次打包也会自动生成】本项目中用来存储java编译出的class、class打包成的classes.dex以及apk文件。
      +  gen/                 -> 【myjavac21.bat自动生成，该目录可删除，每次打包自动生成】存放资源文件生成的R.java，以及自动生成的，用于签名apk的密钥。
      +  jar/                 -> 【手动创建】存放依赖jar包（new-project.bat创建项目时会生成一个libs用于存放jar包，此处为了与存放so文件的lib目录便于区分，手动改名为jar）
        +  link/                -> 【手动创建】存放依赖jar包，这里的jar最终将打包到apk中，“jar/”下的jar文件仅在编译时需要，不会打包到apk
      +  lib/                 -> 【手动创建】存放so库文件，该目录名是固定的，对应于apk包中的lib路径，本项目用不到，所以不需要该目录，myjavac21.bat也未对此路径进行处理，若要添加so库，需自行添加处理过程
      +  assets/              -> 【手动创建】存放本地网页文件，html、js、css、图片等，如果直接使用远程URL，就不用把html等放这了
      -  AndroidManifest.xml -> android项目的基本配置文件，使用方法上网查
      -  ant.properties      -> new-project.bat自动生成该文件，应该是ant打包用的，直接删掉
      -  build.xml           -> new-project.bat自动生成该文件，应该是ant打包用的，直接删掉
      -  local.properties    -> new-project.bat自动生成该文件，应该是ant打包用的，直接删掉
      -  proguard-project.txt-> new-project.bat自动生成该文件，应该是ant打包用的，直接删掉
      -  project.properties  -> new-project.bat自动生成该文件，应该是ant打包用的，直接删掉
      
使用说明：
1. apk打包工具的使用
  第一步：把 conf.bat、env.bat、myjavac?.bat、new-project.bat 拷贝到一个空目录，（?代表版本号，本次为myjavac21.bat）
  第二步：参考env.bat中已有配置，把android-sdk、java-sdk路径修改为自己系统下安装的对应路径
  第三步：创建项目，运行new-project.bat
      第一种方式：直接双击运行，启动后提示输入 新的项目名称；
      第二种方式：把new-project.bat拖拽到cmd窗口并提供项目名称作为参数，像这个样子“C:\Users\Administrator> D:\xxx\android_webview\new-project.bat testproj”
  第四步：增加、修改java代码
  第五步：编译与打包，运行myjavac?.bat（?代表版本号，本次为myjavac21.bat）
  第六步：测试apk，生成的apk存放于“./android_webview/项目目录/bin/”目录下，本项目中apk为xxx.xxx.21.apk和xxx.xxx.21-signed.apk，分别为未签名和已签名apk。
2. 快速用于测试自己的网站
  安装并运行xxx.xxx.21-signed.apk，在首页中填写要跳转到的URL即可
3. android_webview源码的使用
  参考源码及注释
  
