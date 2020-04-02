# 在 Flutter 中使用 Fluro 管理路由

**步骤一：** 在入口文件 main.dart 中的 MyApp 类的 build 方法中初始化 fluro，声明路由。

  ```dart
  import 'package:fluro/fluro.dart';
  import './routers/routes.dart';
  import './routers/application.dart';
  
  class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      final router = Router();
      Routes.configureRoutes(router); // 一定要写
      Application.router = router; // 静态化
  
      return Container(
        child: MaterialApp(
          title: '小畅叙的App',
          debugShowCheckedModeBanner: false,
          // flutter自带的属性，可以放flutter原生路由，这里换成我们配置的fluro路由
          // 全局注入，注入到每个页面，这样每个页面都可以使用
          onGenerateRoute: Application.router.generator,
          theme: ThemeData(
            primaryColor: Colors.pink,
          ),
          home: IndexPage(),
        ),
      );
    }
  }
  ```

**步骤二：** 在 lib 文件夹下面新建文件夹 routers。

**步骤三：** 在 routers 文件夹下新建文件 router_handler.dart，用于放置所有 handler（路由规则）。

  ```dart
  import 'package:flutter/material.dart';
  import 'package:fluro/fluro.dart';
  import '../pages/details_page.dart';
  
  // 商品详细页的handler
  Handler detailsHandler = Handler(
      handlerFunc: (BuildContext context, Map<String, List<String>> params) {
    String goodsId = params['id'].first;
    print('index>details goodsID is $goodsId');
    return DetailsPage(goodsId); // DetailsPage在声明的时候就是需要传递goodsId参数的
  });
  ```

**步骤四：** 上面的 handler 只是针对单个路由的配置，所有 handler 配置好后，还需要总体路由配置。在 routers 文件夹下新建文件 routes.dart，用于总体路由配置。

  ```dart
  import 'package:flutter/material.dart';
  import 'package:fluro/fluro.dart';
  import './router_handler.dart';
  
  class Routes {
    static String root = '/';
    static String detailsPage = '/detail';
    // router是fluro包中自带的
    static void configureRoutes(Router router) {
      // 找不到handler，路由路径不存在的写法
      router.notFoundHandler = new Handler(
        handlerFunc: (BuildContext context, Map<String, List<String>> params) {
          print('ERROR====>ROUTE WAS NOT FONUND!!!');
        },
      );
      // 配置路由，配置写好的handler
      router.define(detailsPage, handler: detailsHandler);
    }
  }
  ```

**步骤五：** 在 routers 文件夹下新建一个文件 application.dart，把 fluro 静态化。

  ```dart
  import 'package:fluro/fluro.dart';
  // 静态化一下，以后可以直接通过Application.router来使用路由，不需要new对象了
  class Application{
    static Router router;
  }
  ```

**步骤六：** 在需要使用路由的文件中，比如 home_page.dart 文件，引入上面的静态化文件 application.dart

  ```dart
  onTap: () {
    Application.router.navigateTo(context, "/detail?id=${val['goodsId']}");
  }
  ```
