![](http://img.kaiyanapp.com/0-262c2ce15c1d04f5cfb59deffa43dd7f.jpeg)
# 开眼视频 Flutter 高仿版
> 「开眼」是一款充满设计感的精品短视频应用。汇聚全球优质的短视频内容，推荐创意的短视频作品，从视觉到脑洞，带给用户全方位的惊喜与享受。

[开眼视频 Flutter 高仿版](https://github.com/mmtou/eyepetizer_flutter) 是基于「开眼」非开放 API 、使用 Flutter 开发。

项目源码: [https://github.com/mmtou/eyepetizer_flutter](https://github.com/mmtou/eyepetizer_flutter)  
下载体验: [https://fir.im/eyepetizer](https://fir.im/eyepetizer)  
![扫码下载体验](https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/d1.png)

## 预览
<img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/1.png" width="375"><img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/2.png" width="375"><img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/3.png" width="375"><img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/4.png" width="375"><img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/5.png" width="375"><img src="https://github.com/mmtou/eyepetizer_flutter/raw/master/demo/images/6.png" width="375">

## 实现功能
- [x] 静态启动图
- [x] 首页
- [x] 分类查询
- [x] 视频详情
- [x] 图片预览
- [x] 相关推荐
- [x] 查询评论
- [x] 分享
- [ ] 登录
- [ ] 评论
- [ ] 性能优化

## 关键代码
1. 全局Toast轻提示/loading，在初始化`MaterialApp`时包上Toast组件，实现无`context`的Toast、Loading等。
```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BotToastInit(
        child: MaterialApp(
      title: '开眼视频',
      theme: ThemeData(
        primarySwatch: Colors.blueGrey,
        primaryColor: Color(0xFF2E3034),
        indicatorColor: Colors.white,
      ),
      navigatorObservers: [BotToastNavigatorObserver()],
      home: Index(),
    ));
  }
}
```

2. 基于`dio`的网络请求封装，拦截器在发起请求前可以 处理请求参数、在header中添加令牌等信息；在接收到服务器响应后，可以判断httpStatus、响应体的code等，做出响应处理。
```dart
class Http {
  Dio _dio;

  Http() {
    if (_dio == null) {
      _dio = Dio(
        BaseOptions(
          baseUrl: Constant.host,
          connectTimeout: 5000,
          receiveTimeout: 3000,
          headers: {
            'User-Agent': 'eyepetizer_flutter/1.0.0',
            'Accept': '*/*',
            'Cache-Control': 'no-cache',
            'Host': 'baobab.kaiyanapp.com',
            'Accept-Encoding': 'gzip, deflate, br',
            'Connection': 'keep-alive'
          },
        ),
      );

      _dio.interceptors
          .add(InterceptorsWrapper(onRequest: (RequestOptions options) async {
        // 判断参数、添加令牌等
        return options;
      }, onResponse: (Response response) async {
        // 处理响应内容
        return response; // continue
      }, onError: (DioError e) async {
        BotToast.showText(text: '服务器异常');
        return e;
      }));
    }
  }

  Future get(uri, {queryParameters}) async {
    try {
      Response response = await _dio.get(uri, queryParameters: queryParameters);
      return response.data;
    } catch (e) {
      print(e);
    }
  }
}
```

3. 下拉刷新、上拉加载更多，支持多种炫酷的加载效果
```dart
EasyRefresh(
  child: ListView(...),
  header:
      BezierCircleHeader(backgroundColor: Theme.of(context).primaryColor),
  footer:
      BezierBounceFooter(backgroundColor: Theme.of(context).primaryColor),
  onRefresh: () async {
    // 下拉刷新
  },
  onLoad: () {
    // 上拉加载更多
  },
);
```

## 插件
- [dio ](https://github.com/flutterchina/dio)是一款由 [ flutterchina ](https://github.com/flutterchina)开发的http client插件，和大前端的`axios`可以媲美。
- [bot_toast](https://github.com/MMMzq/bot_toast)是一款方便的不需要每次传context的Toast插件。
- [photo_view](https://github.com/renancaraujo/photo_view)是一款图片预览、缩放的插件。
- [flutter_ijkplayer](https://github.com/CaiJingLong/flutter_ijkplayer)是一款通过纹理方式接入 bilibili/ijkplayer 的视频播放插件。
- [flutter_easyrefresh](https://github.com/xuelongqy/flutter_easyrefresh)是一款强大的多风格的下拉刷新插件。
- [cached_network_image](https://github.com/Baseflow/flutter_cached_network_image)是一款带缓存的网络图片插件。

## 小工具
1. [Android Launcher icon generator](https://romannurik.github.io/AndroidAssetStudio/icons-launcher.html) 一款在线快速生成安卓 icon 的工具
2. [Flutter国内插件库](https://pub.flutter-io.cn/)，速度会比`https://pub.dev`更快
3. `Charles`是一款方便的抓包工具，开眼的API可以通过`Charles`直接抓到

## 本地运行
```shell
git clone https://github.com/mmtou/eyepetizer_flutter
cd eyepetizer_flutter
flutter pub get
`Android Studio / VS Code`运行
```

## 总结
1. 动手写过一些代码后发现 Flutter 和 Vue 编码效率不相上下（有些时候可能会消耗好多时间找问题解决方案）
> [开眼视频 Vue 高仿版 传送门](https://github.com/mmtou/eyepetizer_vue)
2. Flutter 的组件实在是太多了，需要花费一定时间了解
3. 官方插件不太完善，经常会有些功能需要找第三方插件
4. google开发，发展势头不错

## 最后
只争朝夕，不负韶华~
