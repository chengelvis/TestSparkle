# TestSparkle
A demo of how to use Sparkle

---

Sparkle是Mac平台上，App更新检测的最佳开源库。

Sparkle的工作原理是，把检测逻辑打包进framework，同时提供一些列生成签名和打包工具。特别优秀的是，还支持delta打包。

完整文档见：https://sparkle-project.org/documentation/

我把摸索过程中，需要理解的概念和注意的坑整理如下：

###常用脚本

```bash
# generate public and private keys
./Sparkle-1.17.0/bin/generate_keys

# generate appcast.xml and delta package
./Sparkle-1.17.0/bin/generate_appcast ./dsa_priv.pem ./build-dist/
```

特别要注意的是，上面签名的私钥，一定要跟程序中导入的公钥匹配。我第一次测试的时候，就在这里栽坑了，浪费了有三个小时左右。

###升级包准备

所有的新版App都必须将*.app打成*.zip包。 命名上需要注意的地方有：

* .zip包里面包含的.app的名称，必须同程序以往的名称保持一致，否则无法替换升级；
* .zip包的包名中需要包含版本号，建议是程序名称-版本号.zip的形式。譬如：TestSparkle-1.1.zip。

Feed文件也作简单说明如下：

* Sparkle利用“SUFeedURL”这个设置中，设置的xml作为检测更新的对比参考，建议保持默认文件名：“appcast.xml”；
* appcast.xml 和 delta升级包 都由 generate_appcast 负责生成，每次重新run generate_appcast以后，需要更新appcast.xml 和 所有的zip包文件。


###配置

常用的设置项有以下这些：（完整的见：https://sparkle-project.org/documentation/customization/）

* `SUFeedURL`
String 
The URL of your appcast, e.g. https://example.com/appcast.xml. It’s recommended to always set it in Info.plist, even if you change it later programmatically. 
* `SUPublicDSAKeyFile`
String 
The filename of the public DSA key in your app’s Resources folder, e.g. dsa_pub.pem. 
SUEnableAutomaticChecks
Boolean 
Setting to YES (recommended) enables checking for updates (but not installation) by default, without asking your users for permission first. 
By default, if it’s not set to any value, users will be prompted for permission before the first update check. 
Setting to NO disables update checks, but can be overridden by a call to SUUpdater’s setAutomaticallyChecksForUpdates:
* `SUScheduledCheckInterval`
Number 
The number of seconds between updates. The default is 86400 (1 day). Setting to 0 disables updates. 
Note: this has a minimum bound of 1 hour in order to keep you from accidentally overloading your servers. 
* `SUAutomaticallyUpdate`
Boolean 
Default: NO. Enables automatic download and installation of updates. If set to YES, users will not be informed about updates, and updates will be silently installed when the app quits. 
* `SUAllowsAutomaticUpdates`
Boolean 
Default: YES. Sparkle presents your users with the option to allow to automatically download and install any available updates. Set this to NO to disallow automatic updates and require manual installation every time. 