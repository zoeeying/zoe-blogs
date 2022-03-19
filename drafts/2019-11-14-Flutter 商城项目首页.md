# Flutter 商城项目首页

## 1 两种 UI 风格：

**material：**谷歌推出的，全新的设计语言，为手机、平板电脑、台式机以及其它的一些平台提供一致和更广泛的外观，并且提供了一些交互效果。

**cupertino：**iOS 风格。

## 2 initState 初始化方法

```dart
// 可以在initState中获取后台数据
var currentPage;

@override
 void initState() {
   currentPage = tabBodies[currentIndex];
   super.initState();
}
```

## 3 底部导航

```dart
new Scaffold(
  backgroundColor: Color.fromRGBO(244, 245, 245, 1),
  bottomNavigationBar: BottomNavigationBar(
    type: BottomNavigationBarType.fixed,
    currentIndex: currentIndex,
    items: bottomTabs,
    onTap: (index) {
      // 使用setState才会触发页面的重绘
      setState(() {
        currentIndex = index;
        currentPage = tabBodies[currentIndex];
      });
    },
  ),
  body: currentPage,
);
```

## 4 文本框、按钮、文本

```dart
// 文本框的控制器中需要使用
TextEditingController typeController = TextEditingController();
String showText = '欢迎您来到美好人间高级会所';

TextField(
  // 只有设置了控制器才能得到文本框的值，需要先声明一下
  controller: typeController,
  decoration: InputDecoration(
    contentPadding: EdgeInsets.all(10.0),
    labelText: '美女类型',
    helperText: '请输入你喜欢的类型',
  ),
  // 一定要置为false，不然可能会自动打开手机输入键盘
  autofocus: false,
),
RaisedButton(
  onPressed: () {},
  child: Text('选择完毕'),
),
Text(
  showText,
  // 单独用Text，外边没有Container等组件的时候，需要给Text加上一些限制
  // 如果行文字超出了，显示省略号
  overflow: TextOverflow.ellipsis,
  maxLines: 1,
),
```

## 5 Dio 

Dio 是一个 Dart Http 请求库。

post 请求只需要把下面的 `Dio().get()` 换成 `Dio().post()`，queryParameters 换成 data。 

```dart
// 在页面中调试接口用
// 返回Future，这样子then的回调函数中的参数就是return的值
Future getHttp(String TypeText) async {
  try {
    Response response;
    var data = {'name': TypeText};
    response = await Dio().get(
      'https://www.easy-mock.com/mock/5c60131a4bed3a6342711498/baixing/dabaojian',
      queryParameters: data,
    );
    return response.data;
  } catch (e) {
    return print(e);
  }
}
```

项目中，可以在 service 文件夹中建立一个文件 service_method.dart，把公共的请求方法放在这里。

```dart
import 'package:dio/dio.dart';
import 'dart:async';
import 'dart:io';
import '../config/service_url.dart';

// formData外边的花括号表示可选参数
Future request(url, {formData}) async {
  try {
    //print('开始获取数据...............');
    Response response;
    Dio dio = new Dio();
    dio.options.contentType = Headers.formUrlEncodedContentType;
    if (formData == null) {
      response = await dio.post(servicePath[url]);
    } else {
      response = await dio.post(servicePath[url], data: formData);
    }
    if (response.statusCode == 200) {
      return response.data;
    } else {
      throw Exception('后端接口出现异常，请检测代码和服务器情况.........');
    }
  } catch (e) {
    return print('ERROR:======>$e');
  }
}

// 获取首页主体内容
Future getHomePageContent() async {
  try {
    Response response;
    Dio dio = new Dio();
    // 旧的写法，如果上面不引入dart:io，ContentType是不可以用的
    // dio.options.contentType = ContentType.parse('application/x-www-form-urlencoded');
    // 新的写法
    dio.options.contentType = Headers.formUrlEncodedContentType;
    var formData = {'lon': '115.02932', 'lat': '35.76189'};
    response = await dio.post(servicePath['homePageContext'], data: formData);
    if (response.statusCode == 200) {
      return response.data;
    } else {
      throw Exception('首页主体内容获取失败！');
    }
  } catch (e) {
    return print('ERROR：===================$e');
  }
}
```

## 6 点击事件

```dart
// 内部方法，方法名称以下划线开头
// 弹出框、请求接口、把请求到的数据setState给变量
void _choiceAction() {
  print('开始选择喜欢的类型');
  if (typeController.text.toString() == '') {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(title: Text('美女类型不能为空！')),
    );
  } else {
    getHttp(typeController.text.toString()).then((value) {
      setState(() {
        showText = value['data']['name'].toString();
      });
    });
  }
}
```

## 7 伪造请求头获取数据

fiddler 工具可以截取请求。

```dart
// httpHeaders是拷贝的请求头
Future getHttp() async {
  try {
    Response response;
    Dio dio = new Dio();
    // 设置请求头
    dio.options.headers = httpHeaders;
    response =
      await dio.get('https://time.geekbang.org/serv/v2/explore/list');
    print(response);
    return response.data;
  } catch (e) {
    return print(e);
  }
}
```

## 8 Swiper 轮播效果

```dart
import 'package:flutter_swiper/flutter_swiper.dart';

// 首页轮播组件
class SwiperDiy extends StatelessWidget {
  final List swiperDataList; // 用来接收参数
  // 无名无参的参数的父类
  // SwiperDiy({Key key, this.swiperDataList}) : super(key: key);
  SwiperDiy({this.swiperDataList});

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 333,
      child: Swiper(
        // 构造器
        itemBuilder: (BuildContext context, int index) {
          return Image.network(
            '${swiperDataList[index]['image']}',
            fit: BoxFit.fill, // 满容器填充
          );
        },
        itemCount: swiperDataList.length, // 图片张数
        pagination: SwiperPagination(), // 轮播图下面的导航器
        autoplay: true, // 自动播放
      ),
    );
  }
}
```

## 9 处理 JSON 数据、FutureBuilder 组件（很重要）

```dart
import 'dart:convert'; // 用来处理JSON数据

class _HomePageState extends State<HomePage> {
  String homePageContent = '正在获取数据';
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('百姓生活+')),
      // FutureBuilder可以解决异步请求数据然后渲染组件，而且不需要setState
      body: FutureBuilder(
        future: getHomePageContent(),
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            var data = json.decode(snapshot.data.toString());
            // List里面是Map
            List<Map> swiper = (data['data']['slides'] as List).cast(); // 轮播图数据
            List<Map> navigatorList =
                (data['data']['category'] as List).cast(); // 导航数据
            return Column(
              children: <Widget>[
                SwiperDiy(swiperDataList: swiper),
                TopNavigator(navigatorList: navigatorList),
              ],
            );
          } else {
            return Center(child: Text('加载中...'));
          }
        },
      ),
    );
  }
}
```

## 10 屏幕适配

在 Widget 的 build 方法中初始化设计尺寸。一般会把初始化放在第一个页面中，比如本商城案例的 IndexPage 中，这样全局都可以使用 ScreenUtil 了。

```dart
import 'package:flutter_screenutil/flutter_screenutil.dart';

// 初始化设计尺寸
ScreenUtil.instance = ScreenUtil(width: 750, height: 1334)..init(context);

fontSize: ScreenUtil().setSp(28, false) // 设置字体大小，false表示字体大小不会随着系统的字体大小而改变
height: ScreenUtil().setHeight(333) // 设置高度
```

设备的像素密度：`ScreenUtil.pixelRatio`

设备的高：`ScreenUtil.screenHeight`

设备的宽：`ScreenUtil.screenWidth`

## 11 导航区域：GridView、InkWell

```dart
import 'package:flutter/material.dart';
import 'package:flutter_screenutil/flutter_screenutil.dart';

class TopNavigator extends StatelessWidget {
  final List navigatorList;
  TopNavigator({Key key, this.navigatorList}) : super(key: key);

  // 使用内部方法拆分UI，返回Widget
  Widget _gridViewItemUI(BuildContext context, item) {
    return InkWell(
      onTap: () {
        print('点击了导航');
      },
      child: Column(
        children: <Widget>[
          Image.network(
            item['image'],
            width: ScreenUtil().setWidth(95),
          ),
          Text(item['mallCategoryName']),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    // 处理一下navigatorList，如果个数超过十个，就减去一个
    // 用this.navigatorList，而不是直接用navigatorList
    if (this.navigatorList.length > 10) {
      this.navigatorList.removeRange(10, this.navigatorList.length);
    }

    return Container(
      height: ScreenUtil().setHeight(320),
      padding: EdgeInsets.all(3.0),
      child: GridView.count(
        crossAxisCount: 5,
        padding: EdgeInsets.all(5.0),
        children: navigatorList.map((item) {
          return _gridViewItemUI(context, item);
        }).toList(), //这边为什么要toList不太懂
      ),
    );
  }
}
```

## 12 升级包

在 pubspec.yaml 同级目录下，使用命令 `flutter upgrade`

## 13 调用手机的拨打电话功能

使用 url_launcher 插件，可以打开链接、发邮件、打电话、发短信。

```dart
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';

class LeaderPhone extends StatelessWidget {
  final String leaderImage; // 店长图片
  final String leaderPhone; // 店长电话

  LeaderPhone({Key key, this.leaderImage, this.leaderPhone}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      child: InkWell(
        onTap: _launchURL,
        child: Image.network(leaderImage),
      ),
    );
  }

  void _launchURL() async {
    String url = 'tel' + leaderPhone;
    // String url = 'http://jspang.com';
    
    if (await canLaunch(url)) {
      await launch(url);
    } else {
      throw 'url不能进行访问，异常';
    }
  }
}
```

## 14 TextStyle 删除线

```dart
Text(
  '￥${recommendList[index]['price']}',
  style: TextStyle(
    decoration: TextDecoration.lineThrough, // 删除线
    color: Colors.grey,
  ),
)
```

## 15 保持页面状态

比如我们首页滚动了很多屏，切换到其它页面再切换回来，首页会重新加载，回到顶部。但是我们需要在切换回来的时候，首页不重新加载，且首页保持在之前滚动到的屏幕状态。

```dart
// 混入AutomaticKeepAliveClientMixin，里面需要重写getter，
// 混入AutomaticKeepAliveClientMixin的Widget必须是StatefulWidget
class _HomePageState extends State<HomePage> with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;
  ...
}

// index_page.dart中
final List<Widget> tabBodies = [
  HomePage(),
  CategoryPage(),
  CartPage(),
  MemberPage(),
];

// 页面索引
int currentIndex = 0;

@override
Widget build(BuildContext context) {
  // 初始化设计尺寸，一般只在第一个页面中初始化一次就可以
  ScreenUtil.instance = ScreenUtil(width: 750, height: 1334)..init(context);

  return Scaffold(
    backgroundColor: Color.fromRGBO(244, 245, 245, 1),
    bottomNavigationBar: BottomNavigationBar(
      type: BottomNavigationBarType.fixed,
      currentIndex: currentIndex,
      items: bottomTabs,
      onTap: (index) {
        // 使用setState才会触发页面的重绘
        setState(() {
          currentIndex = index;
        });
      },
    ),
    // PageView和IndexedStack可以有页面保持效果
    body: IndexedStack(
      index: currentIndex,
      children: tabBodies, // children必须是ListView
    ),
  );
}
```

## 16 变量形式的组件

```dart
Widget hotTitle = Container(
  margin: EdgeInsets.only(top: 10.0),
  alignment: Alignment.center, // 居中对齐
  color: Colors.transparent, // 透明颜色
  child: Text('火爆专区'),
);
```

## 17 流式布局

流式布局比 GridView 性能高。

```dart
Widget _wrapList() {
    if (hotGoodsList.length != 0) {
      // 需要把List<Map>拼装成一个List<Widget>
      List<Widget> listWidget = hotGoodsList.map((val) {
        return InkWell(
          onTap: () {},
          child: Container(
            width: ScreenUtil().setWidth(372),
            color: Colors.white,
            padding: EdgeInsets.all(5.0),
            margin: EdgeInsets.only(bottom: 3.0),
            child: Column(
              children: <Widget>[
                Image.network(val['image'], width: ScreenUtil().setWidth(370)),
                Text(
                  val['name'],
                  maxLines: 1,
                  overflow: TextOverflow.ellipsis,
                  style: TextStyle(
                    color: Colors.pink,
                    fontSize: ScreenUtil().setSp(26),
                  ),
                ),
                Row(
                  children: <Widget>[
                    Text('￥${val['mallPrice']}'),
                    Text(
                      '￥${val['price']}',
                      style: TextStyle(
                        color: Colors.black26,
                        decoration: TextDecoration.lineThrough,
                      ),
                    ),
                  ],
                ),
              ],
            ),
          ),
        );
      }).toList();
      // 流式布局
      return Wrap(
        spacing: 2, // 一行两列
        children: listWidget,
      );
    } else {
      return Text('');
    }
  }
```

## 18 上拉加载、下拉刷新

使用插件 [EasyRefresh](https://github.com/xuelongqy/flutter_easyrefresh)。插件需要在当前页面的主方法中，才能实现上拉加载，下拉刷新的效果。把组件都包裹在 EasyRefresh 中。可以自定义头部和底部，也可以使用指定的 header 和 footer。

```dart
import 'package:flutter_easyrefresh/easy_refresh.dart';
import 'package:flutter_easyrefresh/material_header.dart';
import 'package:flutter_easyrefresh/material_footer.dart';

EasyRefresh(
  header: MaterialHeader(),
  footer: MaterialFooter(),
  child: ScrollView(),
  onRefresh: () async{
    ....
  },
  onLoad: () async {
    ....
  },
)
```

## 19 琐碎知识点

pages 文件夹中放 UI 文件。

点击按钮执行的方法叫做内部方法，内部方法名称以下划线开头。

容器越界：容器外边包一个组件 SingleChildScrollView，但是它跟 ListView 一起使用的时候会有冲突。

451：访问不合法。

```dart
// 无名无参的父类
SwiperDiy({Key key, this.swiperDataList}) : super(key: key);

// 可以代替上面的方法
SwiperDiy({this.swiperDataList});

```

