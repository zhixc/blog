# AppBar、TabBar、TabView仿头条演示案例

## 文件示意图
![img](./AppBar2/00.png)

## 源码

main.dart

```dart
import 'package:flutter/material.dart';
import 'pages/tabs.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Material App',
      home: Tabs(),
    );
  }
}
```

tabs.dart

```dart
import 'package:flutter/material.dart';
import 'tabs/home.dart';
import 'tabs/category.dart';
import 'tabs/setting.dart';
import 'tabs/user.dart';

class Tabs extends StatefulWidget {
  const Tabs({super.key});

  @override
  State<Tabs> createState() => _TabsState();
}

class _TabsState extends State<Tabs> {
  int _index = 0; // 给个默认值
  Widget myBody = const HomePage();
  final List<Widget> _pages = const [
    HomePage(),
    CategoryPage(),
    SettingPage(),
    UserPage(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('仿头条演示'),
        backgroundColor: Colors.red,
        foregroundColor: Colors.white,
      ),
      // body: myBody,
      body: _pages[_index],
      bottomNavigationBar: BottomNavigationBar(
        // 注意，里面的 items 必须要2项以上，才能正常使用
        iconSize: 35.0, // 配置底部菜单项的大小
        fixedColor: Colors.red, // 选中的颜色
        // type: BottomNavigationBarType.fixed, // 如果 2 <= items <= 3 那么可以不用管这个
        type: BottomNavigationBarType.fixed, // 如果 items > 3  那么必须设置 type
        onTap: (index) {
          // 这个是选中变化回调函数
          // 点击事件
          print('-----------------------');
          print('index => $index');
          if (index == 0) {
            // 首页
            print('当前是首页被打开了！');
            // myBody = const HomePage();
          } else if (index == 1) {
            // 分类
            print('当前是分类被打开了！');
            // myBody = const CategoryPage();
          } else if (index == 2) {
            // 设置
            print('当前是设置被打开了！');
            // myBody = const SettingPage();
          }
          setState(() {
            // 必须要用这个才能改遍组件的状态，用了这个，才会重新执行 build() 函数重构组件
            _index = index;
          });
          print('-----------------------');
        },
        currentIndex: _index, // 默认选中的是第几个
        items: const [
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            label: '首页',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.category),
            label: '分类',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings),
            label: '设置',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.people),
            label: '用户',
          ),
        ],
      ),
    );
  }
}
```

keepAliveWrapper.dart

```dart
import 'package:flutter/material.dart';

class KeepAliveWrapper extends StatefulWidget {
  const KeepAliveWrapper({
    super.key,
    required this.child,
    this.keepAlive = true,
  });

  final Widget? child;
  final bool keepAlive;

  @override
  State<KeepAliveWrapper> createState() => _KeepAliveWrapperState();
}

class _KeepAliveWrapperState extends State<KeepAliveWrapper>
    with AutomaticKeepAliveClientMixin {
  @override
  Widget build(BuildContext context) {
    return widget.child!;
  }

  @override
  bool get wantKeepAlive => widget.keepAlive;

  @override
  void didUpdateWidget(covariant KeepAliveWrapper oldWidget) {
    if (oldWidget.keepAlive != widget.keepAlive) {
      // keepAlive 状态需要更新
      updateKeepAlive();
    }
    super.didUpdateWidget(oldWidget);
  }
}
```

category.dart

```dart
import 'package:flutter/material.dart';

class CategoryPage extends StatefulWidget {
  const CategoryPage({super.key});

  @override
  State<CategoryPage> createState() => _CategoryPageState();
}

class _CategoryPageState extends State<CategoryPage> {
  @override
  Widget build(BuildContext context) {
    return ListView(
      children: const [
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表',
            textAlign: TextAlign.center,
          ),
        ),
        ListTile(
          title: Text(
            '我是一个列表222',
            textAlign: TextAlign.center,
          ),
        ),
      ],
    );
  }
}
```

home.dart

```dart
import 'package:flutter/material.dart';
import '../../tools/keepAliveWrapper.dart';

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 9, vsync: this);
    // 监听 TabController 的改变事件
    _tabController.addListener(() {
      //print(_tabController.index); // 会获取2次
      if (_tabController.animation!.value == _tabController.index) {
        print(_tabController.index); // 这样就正常了
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: PreferredSize(
        // 可以配置 AppBar 的高度
        preferredSize: const Size.fromHeight(40),
        child: SizedBox(
          // 借助 SizedBox 设置高度来更改 AppBar 的高度
          height: 30,
          child: AppBar(
            elevation: 0.2, // AppBar的下划线宽度
            title: TabBar(
              tabAlignment: TabAlignment.start,
              isScrollable: true,
              indicatorColor: Colors.red,
              labelColor: Colors.red,
              // indicatorSize: TabBarIndicatorSize.tab,
              controller: _tabController,
              onTap: (index) {
                // 注意这个只能监听点击事件，无法监听滑动事件
              },
              tabs: const [
                Text('热门'),
                Text('推荐'),
                Text('视频'),
                Text('娱乐'),
                Text('事件'),
                Text('关注'),
                Text('宠物'),
                Text('竞赛'),
                Text('表演'),
              ],
            ),
          ),
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: [
          KeepAliveWrapper(
            // 自定义的缓存组件
            child: ListView(
              children: const [
                ListTile(title: Text('我是热门列表1')),
                ListTile(title: Text('我是热门列表1')),
                ListTile(title: Text('我是热门列表1')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表')),
                ListTile(title: Text('我是热门列表222')),
                ListTile(title: Text('我是热门列表(切换到其他选项卡后再次切换回来，位置不变)')),
                ListTile(title: Text('我是热门列表333')),
                ListTile(title: Text('我是热门列表(有缓存)')),
              ],
            ),
          ),
          ListView(
            children: const [
              ListTile(title: Text('我是推荐列表')),
            ],
          ),
          ListView(
            children: const [
              ListTile(title: Text('我是视频列表')),
            ],
          ),
          const Text('来了'),
          const Text('来了'),
          const Text('来了'),
          const Text('来了'),
          const Text('来了'),
          const Text('来了'),
        ],
      ),
    );
  }
}
```

setting.dart

```dart
import 'package:flutter/material.dart';

class SettingPage extends StatefulWidget {
  const SettingPage({super.key});

  @override
  State<SettingPage> createState() => _SettingPageState();
}

class _SettingPageState extends State<SettingPage> {
  @override
  Widget build(BuildContext context) {
    return const Center(
      child: Text('设置'),
    );
  }
}
```

user.dart

```dart
import 'package:flutter/material.dart';

class UserPage extends StatefulWidget {
  const UserPage({super.key});

  @override
  State<UserPage> createState() => _UserPageState();
}

class _UserPageState extends State<UserPage> {
  @override
  Widget build(BuildContext context) {
    return const Center(
      child: Text('用户页面'),
    );
  }
}
```



## 运行示意图
![img](./AppBar2/01.png)
![img](./AppBar2/02.png)
![img](./AppBar2/03.png)
