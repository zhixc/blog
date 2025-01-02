# BottomNavigationBar 组件

## 案例
### 各个文件存放目录：
![img](./bottom_navigation_bar/01.jpg)
### 代码

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
      // appBar: AppBar(
      //   title: const Text('Material App Bar'),
      //   backgroundColor: Colors.yellow,
      // ),
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

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return const Center(
      child: Text('首页'),
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
![img](./bottom_navigation_bar/02.jpg)
![img](./bottom_navigation_bar/03.jpg)
![img](./bottom_navigation_bar/04.jpg)
![img](./bottom_navigation_bar/05.jpg)
