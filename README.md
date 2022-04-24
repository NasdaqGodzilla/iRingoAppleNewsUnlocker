# iRingoAppleNewsUnlocker
Unlock Apple news by iRingo.

基于[iRingo](https://github.com/VirgilClyne/iRingo)的操作步骤，借助iRingo提供的路由配置解锁Apple news的地区限制。

# 前置条件
1. 拥有美区Apple ID（必须能够登录App store，不要求登录iCloud）
2. 拥有支持iRingo模块的App（Loon、Quantumult X
、Shadowrocket）之一。本文是Shadowrocket的操作步骤。

# 步骤

## 1. App store切换账号，登录美区ID
详细步骤略。

## 2. 切换iPhone地区到美国
设置-语言与地区-地区：设置为美国。注意iPhone语言可以不调。地区切换完成后桌面即可看到Apple news，不过此时无法加载。

## 3. 配置模块
安装[iRingo](https://github.com/VirgilClyne/iRingo)提供的模块。需要安装如下三个模块，配置从原仓库拷贝如下：

> 在Shadowrockets中：配置-模块-新建模块将如下4段内容拷贝进去新建为4个新模块即可。注意启用。

```
#!name= Redirect Apple Maps to 🇨🇳CN
#!desc=只作用于Apple Maps，不修改定位服务和地区检测，可以通过搭配使用其他模块达成混合效果。

[URL Rewrite]
# Redirect Apple Maps to CN
(^https?:\/\/gspe\d*-ssl\.ls\.apple\.)(com|cn)(\/.*)(country_code=[A-Z]{2})(.*) $1$2$3country_code=CN$5 header
(^https?:\/\/gspe\d*-ssl\.ls\.apple\.)(com|cn)(\/.*)(environment=[a-z/-]{4,})(.*) $1$2$3environment=prod-cn$5 header

[MITM]
hostname = %APPEND% gspe35-ssl.ls.apple.com
```

```
#!name= Response Geo Services to 🇺🇸US
#!desc=只作用于检测地区，不修改Apple Maps和Apple News，可以通过搭配使用其他模块达成混合效果。

[Script]
# Response Geo Services to US
Geo Services = type=http-request, pattern=^https?:\/\/gspe\d*-ssl\.ls\.apple\.com(\/pep\/gcc), argument=GeoCountryCode=US, requires-body=0, script-path=https://raw.githubusercontent.com/VirgilClyne/iRingo/main/js/Geo_Services.js

[MITM]
hostname = %APPEND% gspe1-ssl.ls.apple.com
```

```
#!name= Unlock Apple News 🇺🇸US
#!desc=解锁🇺🇸US地区的News，注：需配合域名集使用，使用过程中gateway.icloud.com需走代理线路。

[Script]
Apple News = type=http-request, pattern=^https?:\/\/news-(edge|events)\.apple\.com\/(v1\/configs|analyticseventsv2\/async).*, requires-body=1, script-path=https://raw.githubusercontent.com/VirgilClyne/iRingo/main/js/Apple_News.js
Apple News = type=http-request, pattern=^https?:\/\/news-(client-search)\.apple\.com\/(v1\/search\?).*, requires-body=0, script-path=https://raw.githubusercontent.com/VirgilClyne/iRingo/main/js/Apple_News.js

[MITM]
hostname = %APPEND% news-client.apple.com, news-client-search.apple.com, news-edge.apple.com, news-events.apple.com
```

```
#!name= Unlock Apple News 🇺🇸US
#!desc=解锁🇺🇸US地区的News，注：使用过程中gateway.icloud.com会走与News相同的代理线路。

[Rule]
# > Apple News
RULE-SET,https://raw.githubusercontent.com/VirgilClyne/iRingo/main/RuleSet/Apple_News.list,Apple News

[Script]
Apple News = type=http-request, pattern=^https?:\/\/news-(edge|events)\.apple\.com\/(v1\/configs|analyticseventsv2\/async).*, requires-body=1, script-path=https://raw.githubusercontent.com/VirgilClyne/iRingo/main/js/Apple_News.js
Apple News = type=http-request, pattern=^https?:\/\/news-(client-search)\.apple\.com\/(v1\/search\?).*, requires-body=0, script-path=https://raw.githubusercontent.com/VirgilClyne/iRingo/main/js/Apple_News.js

[MITM]
hostname = %APPEND% news-client.apple.com, news-client-search.apple.com, news-edge.apple.com, news-events.apple.com, newsletter-edge.apple.com
```

## 4. 进入飞行模式后启动Apple news
首先启动小火煎，保证上述规则生效。进入飞行模式（需要连接WiFi以使用网络）后启动Apple news。等待Apple news加载完成后即可。

> 只要Apple news首次加载完成后，后续使用不再需要非系模式

# 鸣谢
https://github.com/VirgilClyne/iRingo
