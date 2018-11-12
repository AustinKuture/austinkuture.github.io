---
layout: post
title:  Swift,Objective-C混编解析网络数据并实现简单的轮播功能
categories: iOS
description: Swift,Objective-C混编解析网络数据并实现简单的轮播功能
keywords: iOS,swift
comments: true
---

# Swift,Objective-C混编解析网络数据并实现简单的轮播功能

作者:AustinKuture

> 此次的轮播功能主要是在Swift语言环境下实现,并使用之前Objective-C语言封装的网络加载方法进行桥接. Swift,苹果于2014年WWDC（苹果开发者大会）发布的新开发语言,Swift既可以用于移动端的开发同时也是后台语言. Swift 结合了 C 和 Objective-C 的优点并且不受C兼容性的限制。Swift 采用安全的编程模式并添加了很多新特性，这将使编程更简单，更灵活，也更有趣。Swift 是基于成熟而且倍受喜爱的 Cocoa 和 Cocoa Touch 框架，他的降临将重新定义软件开发。

### 最终效果图:

![img](https://static.oschina.net/uploads/space/2017/0719/103008_7Ecm_2728740.gif)

### 原始Json格式为(如下图所示):

![img](https://static.oschina.net/uploads/space/2017/0719/095841_WMvU_2728740.png)

### 转换之后的图片数组中的数据为:

```
["http://img5.imgtn.bdimg.com/it/u=3892625903,3160473172&fm=200&gp=0.jpg",
 "http://img8.zol.com.cn/bbs/upload/12598/12597929.gif", 
"http://img1.imgtn.bdimg.com/it/u=4016983972488925180&fm=200&gp=0.jpg", 
"http://img8.zol.com.cn/bbs/upload/12598/12597929.gif", 
"http://img5.imgtn.bdimg.com/it/u=38926259033160473172&fm=200&gp=0.jpg"]
```

## 1,整体目录结构

![img](https://static.oschina.net/uploads/space/2017/0724/133707_9chI_2728740.png)![img](https://static.oschina.net/uploads/space/2017/0724/133707_ZjhT_2728740.png)

2,创建控制器,并在当前控制器中编写轮播图层 

```
//当前控制器属性
    @IBOutlet weak var homeTableView: UITableView!
    @IBOutlet weak var cycleScrollViewNew: WRCycleScrollView!

    //下拉刷新控制器
    private var refreshControl = UIRefreshControl()

    override func viewDidLoad() {
        super.viewDidLoad()

        view.backgroundColor = UIColor.white

        //首页初始化
        homeViewControllerInitSetting()
        setupHomeObtainDataFromNetWork()

    }

    //home控制器初始化方法
   fileprivate func homeViewControllerInitSetting(){

        //设置Navigation
        navigationController?.navigationBar.barStyle = UIBarStyle.blackTranslucent
        UIApplication.shared.statusBarStyle = UIStatusBarStyle.lightContent
        navigationController?.navigationBar.tintColor = UIColor.white

        //设置刷新视图
        refreshControl.addTarget(self, action: #selector(refreshViewMethod), for: UIControlEvents.valueChanged)
        refreshControl.attributedTitle = NSAttributedString(string: "下拉刷新")
        homeTableView.addSubview(refreshControl)
    }

    //轮播方法
   fileprivate func cycleScrollViewMethod(){


        print("=====PIC:\(self.cyclePicArray)");

        cycleScrollViewNew = WRCycleScrollView(frame: cycleframe , type: .SERVER, imgs: self.cyclePicArray, descs: self.titleLabelArray)

        cycleScrollViewNew?.delegate = self
        homeTableView.tableHeaderView = cycleScrollViewNew
    }

    //下拉刷新方法
    func refreshViewMethod(){

        print("下拉刷新")
        setupHomeObtainDataFromNetWork()

        //延时
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) {

            self.refreshControl.endRefreshing()
        }

    }
}

//轮播代理方法的实现
extension AKHomeController : WRCycleScrollViewDelegate {

    //轮播点击方法
    func cycleScrollViewDidSelect(at index: Int, cycleScrollView: WRCycleScrollView) {

        print("你点击了第\(index + 1)张图片")

        let cycleDetail = AKHomeCycleDetail()

        navigationController?.pushViewController(cycleDetail, animated: true)
    }
}

//当前控制器出现与消失的方法
extension AKHomeController{

    override func viewDidAppear(_ animated: Bool) {

        tabBarController?.tabBar.isHidden = false
    }
}
```

3.调用已经封装的网络加载方法,实现数据的解析

```
//加载网络数据
extension AKHomeController{

    func setupHomeObtainDataFromNetWork(){

        let netPack = AKNetPackegeAFN.shareHttpManager()
        let url = "http://192.168.199.244/iOS/Swift/cycleScroll.html"

       //封装的网络加载方法
       netPack?.netWorkType(AKNetWorkGET, signature: nil, api: url, parameters: nil, requestTimes: 10, success: { (Json) in

        //将网络加载的NSAarray,NSDictionary等类型转换为Data类型
        let datJ = self.jsonToData(jsonDic: Json as! NSArray)
        let model = JSON(data: datJ!)

        //数据数组
        var titleArrayM = Array<String>()
        var detailIDM = Array<String>()
        var cyclePicM = Array<String>()

        for  i in 0 ..< model.count {

            //将要添加的数据转换为String类型并拼接到String类型的Array里
            titleArrayM.append(String(describing: model[i]["cycleTitle"]))
            detailIDM.append(String(describing: model[i]["cycleDetailID"]))
            cyclePicM.append(String(describing: model[i]["cyclePic"]))
        }

        self.titleLabelArray = titleArrayM
        self.detailIDArray = detailIDM
        self.cyclePicArray = cyclePicM

        //轮播
        self.cycleScrollViewMethod()
        self.homeTableView.reloadData()

       }, fail: { (Error) in

        print("HomeCycleError:\(String(describing: Error))")
       })
    }


    //将请求到的数组转换为Data
    fileprivate func jsonToData(jsonDic:NSArray) ->Data?{

    if(!JSONSerialization.isValidJSONObject(jsonDic)) {

    print("is not a valid json object")

    return nil

    }

    //利用自带的json库转换成Data

    //如果设置options为JSONSerialization.WritingOptions.prettyPrinted，则打印格式更好阅读

    let data = try? JSONSerialization.data(withJSONObject: jsonDic, options: [])

    //Data转换成String打印输出

    let str = String(data:data!, encoding: String.Encoding.utf8)

    //输出json字符串

    print("Json Str:\(str!)")

    return data

    }

}
```

4,网络加载方法详见另一篇博客

[iOS 之Https自签名证书认证及数据请求的封装](http://www.kuture.com.cn//2016/12/12/iOS-%E4%B9%8BHttps%E8%87%AA%E7%AD%BE%E5%90%8D%E8%AF%81%E4%B9%A6%E8%AE%A4%E8%AF%81%E5%8F%8A%E6%95%B0%E6%8D%AE%E8%AF%B7%E6%B1%82%E7%9A%84%E5%B0%81%E8%A3%85/)

5,整体图层![img](https://static.oschina.net/uploads/space/2017/0724/133707_SImn_2728740.png)