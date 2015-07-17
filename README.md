# 用于监听IOS网络状态切换(WIFI和移动蜂窝网络) 
## 有什么作用
在Socket网络游戏中，经常会用到BSDSocket。用户切换网络状态时，Socket链接会断开，如果通过Receive的返回值来判断网络是否断开，需要等待很长时间(4-20s)  
这个时候就需要监听IOS网络状态切换，当发现用户切换网络状态时，直接重连Socket。  
## 如何在Cocos中使用  
首先将NetworkInfo.h 和 NetworkInfo.m导入项目  
然后修改AppController.mm文件  
首先引用刚刚导入的两个文件#import `"NetworkInfo.h"`  
接下来在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`函数中加入如下代码（注册监听）  

    //开启网络状况的监听  
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reachabilityChanged:) name:kReachabilityChangedNotification object:nil];
    NetworkInfo* hostReach = [NetworkInfo reachabilityWithHostName:@"www.baidu.com"] ;//网址可以修改一个连接速度较快的
    [hostReach startNotifier];  //开始监听，会启动一个run loop

并且在文件中中加入网络状态切换的回调方法     
    
    //网络链接改变时会调用的方法  
    -(void)reachabilityChanged:(NSNotification *)note
    {
        NetworkInfo *currReach = [note object];
        NSParameterAssert([currReach isKindOfClass:[NetworkInfo class]]);
        //TODO：重连网络
    }
    
最后在项目中加入库文件SystemConfiguration.framework

