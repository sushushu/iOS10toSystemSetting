
## iOS 10又可以跳转到系统设置页面了 = = ，是国外大神先发展的。

```objectivec	
    NSURL *url = [NSURL URLWithString:@"APP-Prefs:root=WIFI"];
        if ([[UIApplication sharedApplication] canOpenURL:url]) {
            [[UIApplication sharedApplication] openURL:url options:@{} completionHandler:nil];
        }
```

## 0、首先说明iOS 10在应用自身上（除非通知栏）已经不允许任何跳转到系统设置的行为了。下面是关于跳转系统设置的几点说明。

## 1、iOS 9 8 7 可正常跳转

```objectivec		
   NSURL *url = [NSURL URLWithString:@"prefs:root=WIFI"];
                       
   if ([[UIApplication sharedApplication] canOpenURL:url]) {  
                 
        [[UIApplication sharedApplication] openURL:url]; // iOS 9 的跳转
   }
```
（这属于老方法了，iOS 10不可用）


## 2、iOS 10 9 8 7 可正常跳转（会跳到自身应用界面的系统设置）

    NSURL *url = [NSURL URLWithString:UIApplicationOpenSettingsURLString];
    
    if ([[UIApplication sharedApplication] canOpenURL:url]) {
        
        [[UIApplication sharedApplication] openURL:url];
    }
 
（这方法虽然iOS 10也可以用但是并不能去到蓝牙、WIFI、电池，只能去到**自身应用**的系统设置。）


## 3、如果你是通知栏应用（如pin，Launcher）在iOS 10通知栏Widget可以像iOS 9正常跳转

Pin、Launcher，都可以通过简单地设置 URL Scheme 实现此功能，你可以继续在通知中心直接跳转至系统设置的特定页面，比如蜂窝数据、WiFi、定位等等。只需将原来的 `prefs` 开头改成 `Prefs` 即可。
需要注意的是，这个功能只在通知中心的 **Today Widget（即插件）**有效，在应用中则无法直接跳转设置。

（如果你的是通知栏类应用可以用此办法解决，iOS 10 可用）


## 4、iOS 10 新API

```objectivec 
    NSURL *url = [NSURL URLWithString:@"prefs:root=WIFI"];
    
    if ([[UIApplication sharedApplication] canOpenURL:url]) {
        
        [[UIApplication sharedApplication] openURL:url options:@{} completionHandler:nil];
    }
```
和

```objectivec 
- (void)openURL:(NSURL*)url options:(NSDictionary<NSString *, id> *)options completionHandler:(void (^ __nullable)(BOOL success))completion NS_AVAILABLE_IOS(10_0) NS_EXTENSION_UNAVAILABLE_IOS("");
```
- 这里面测试过`options`传`nil`  和  `@{}` 以及随便传个有键值对的字典都是无效。

（如果你知道正确使用该API麻烦指点迷津。）


## 5 私有API

使用`MobileCoreServices.framework`里的私有API:

    - (BOOL)openSensitiveURL:(id)arg1 withOptions:(id)arg2;


#### 使用方法：
    导入MobileCoreServices.framework框架
    
    #import <MobileCoreServices/MobileCoreServices.h>
    
    // Prefs:root=WIFI P要大写

    NSURL *url = [NSURL URLWithString:@"Prefs:root=WIFI"];
    
    Class LSApplicationWorkspace = NSClassFromString(@"LSApplicationWorkspace");
    
    [[LSApplicationWorkspace performSelector:@selector(defaultWorkspace)] performSelector:@selector(openSensitiveURL:withOptions:) withObject:url withObject:nil];

亲测可行，但是随时会有被下架风险。（万能WiFi钥匙、WiFi钥匙上面都用到了。）
要混淆私有API绕过审核的自己去搜下。

## 6 个人觉得比较完美的解决方案（Widget + 3DTouch）

![Alt text](http://ww2.sinaimg.cn/large/70421ae5gw1f9lxc4c16kj20ku112di0.jpg)
