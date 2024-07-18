---
title: 关于UniApp使用的个人笔记
date: 2024-07-18 17:56:20
tags: vue
---

# UniApp

## 开发者中心

用于注册应用以及申请对应证书

https://dev.dcloud.net.cn/pages/app/list

https://blog.csdn.net/fred_kang/article/details/124988303

下载证书后，获取SHA1关键cmd

```lua
keytool -list -v -keystore test.keystore  
Enter keystore password: //输入密码，回车
```

## 解决h5跨域问题

通过manifest.json里的h5配置来解决跨域问题(注：如果要部署到服务器仍然需要配置nginx)

```bash
"h5": {
		"router": {
			"mode": "hash"
		},
		"devServer": {
			"port": 8080,
			"disableHostCheck": true,
			"proxy": {
				"/": {
					"target": "http://localhost:3000",
					"changeOrigin": true,
					"secure": false
				}
			}
		}
	}

```

## app文件无法上传非媒体类文件问题

[参考文档](https://blog.csdn.net/IT_UZI/article/details/132986912)

核心思路：使用renderjs，uniapp自带的

完整样例

```html
<template>
	<button type="primary" size="mini" @tap="attchChoose.onClick">选择文件</button>
</template>
<script>
	import {
		API_SITE
	} from '@/config/config'
	import pop from '@/util/pop'
	import {
		uploadFile
	} from '@/common/request.js'
 
 
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			async upload(path) {
				try {
					// pop.showLoading()
                    // 参数一:本地路径，参数二：后端对应字段名，我这里只是进行了简单的封装
					const result = await uploadFile(path,"files");
					console.log(result);
					// pop.showToast("上传成功")
				} catch (e) {
					console.log(e);
					// pop.showToast(e)
				}
			},
			async chooseFile(data) {
				try {
					const fileUrl = await this.base64toPath(data.base64Str, data.attachName);
					this.upload(fileUrl.localAbsolutePath)
				} catch (e) {
					console.log("err", e);
				}
			},
			/**
			 * @param {Object} base64 文件base64
			 * @param {Object} attachName //文件名需要后缀，如：张三.jpg
			 */
			async base64toPath(base64, attachName) {
				let _that = this;
				return new Promise(function(resolve, reject) {
					const filePath = `_doc/yourFilePath/${attachName}`;
					plus.io.resolveLocalFileSystemURL('_doc', function(entry) {
						entry.getDirectory("yourFilePath", {
							create: true,
							exclusive: false,
						}, function(entry) {
							entry.getFile(attachName, {
								create: true,
								exclusive: false,
							}, function(entry) {
								entry.createWriter(function(writer) {
									writer.onwrite = function(res) {
										const obj = {
											relativePath: filePath,
											localAbsolutePath: plus.io
												.convertLocalFileSystemURL(
													filePath)
										}
										resolve(obj);
									}
									writer.onerror = reject;
									writer.seek(0);
									writer.writeAsBinary(_that
										.getSymbolAfterString(base64,
											','));
								}, reject)
							}, reject)
						}, reject)
					}, reject)
				})
			},
			// 取某个符号后面的字符
			getSymbolAfterString(val, symbolStr) {
				if (val == undefined || val == null || val == "") {
					return "";
				}
				val = val.toString();
				const index = val.indexOf(symbolStr);
				if (index != -1) {
					val = val.substring(index + 1, val.length);
					return val;
				} else {
					return val
				}
			}
		}
	}
</script>
<script module="attchChoose" lang="renderjs">
	let fileInputDom = null;
	export default {
		methods: {
			createFileInputDom() {
				fileInputDom = document.createElement("input");
				fileInputDom.setAttribute('type', 'file');
				fileInputDom.setAttribute('accept', '*');
			},
			onClick(event, ownerInstance) {
				if (!fileInputDom) {
					this.createFileInputDom();
				}
				fileInputDom.click(); // 模拟click
				fileInputDom.addEventListener('change', (e) => {
					fileInputDom = null;
					let choicesFiles = e.target.files[0];
					let reader = new FileReader();
					//读取图像文件 result 为 DataURL, DataURL 可直接 赋值给 img.src
					reader.readAsDataURL(choicesFiles);
					reader.onload = function(event) {
						const base64Str = event.target.result; // 文件的base64
						ownerInstance.callMethod('chooseFile', {
							attachName: choicesFiles.name,
							size: choicesFiles.size,
							base64Str,
						})
					}
					e.target.value = "";
				})
			}
		}
	}
</script>
```

## 本地打包

生成key的方法

```bash
keytool -genkey -alias costmgr -keyalg RSA -keysize 2048 -validity 36500 -keystore costmgr.keystore
```

查看内容

```bash
keytool -list -v -keystore  costmgr.keystore
```

不推荐，虽然打包很快，但很多设置需要加入，还有对应sdk（消息推送各个厂商sdk，uniapp自己的sdk等）

### H5打包

需要nginx进行代理api接口

nginx 修改配置

```bash
 server {
     listen      80;
     server_name obsidianlyg.top;
     location / {
            root   /root/mobile/html;
            index  index.html index.htm;
        }
      location /rest/ {
        proxy_pass http://obsidianlyg.top/rest/;
      }
 }
```

web文件修改，需要将api接口改为相对url

以当前nginx配置为例：

这里仅做参考，后期可以直接放入store中通过uniapp独有的#ifdef H5注释可以自动判别

```bash
// 封装请求方法
function request(url, method, data) {
  return new Promise((resolve, reject) => {
    uni.request({
	  // app
    // url: 'https://obsidianlyg.top' + url,
	  // 网页专属
      url: url,
      method: method,
      data: data,
      header: {
        'Content-Type': 'application/json', 
      },
      success: (res) => {
        if (res.statusCode === 200) {
          resolve(res.data);
        } else {
          reject(res);
        }
      },
      fail: (err) => {
        reject(err);
      },
    });
  });
}
```

### App远程打包

[命令行打包](https://hx.dcloud.net.cn/cli/pack)

## 消息发送

### 本地消息发送

```java
// 开启Socket
let socket;

export function connectWebSocket(empId) {
  socket = uni.connectSocket({
    url: 'ws://obsidianlyg.top/ws/' + empId,
    complete: () => {}
  });
  
  socket.onOpen(() => {
    console.log('WebSocket连接已打开');
  });
  
  socket.onMessage((res) => {
    console.log('收到消息：', res.data);
    sendNotification('新消息', res.data);
  });
  
  socket.onClose(() => {
    console.log('WebSocket连接已关闭');
    // 可以在这里实现重连逻辑
  });
  
  socket.onError((err) => {
    console.error('WebSocket连接错误：', err);
  });
}

function sendNotification(title, message) {
  uni.createPushMessage({
    title: title,
    content: message,
    success: function (res) {
      console.log('推送消息发送成功', res);
    },
    fail: function (err) {
      console.error('推送消息发送失败', err);
    }
  });
}

// 在页面加载时调用connectWebSocket
// connectWebSocket();
```

### 消息推送

采用uni-cloud-push，实际也是使用的WebSocket，接入云端，app离线后仍然接收不到消息，但是再次打开app后可以接收到原来发送的消息

需要申请各个手机品牌的appid接入后才能实现离线接收消息

[参考文档](https://blog.csdn.net/weixin_53339757/article/details/132745746)

实现云函数代码

```
'use strict';  
const uniPush = uniCloud.getPushManager({appId:"__UNI__A0D029F"}) 
exports.main = async (event) => {  
    let obj = JSON.parse(event.body)  //这是重点 解析json字符串
        const res = await uniPush.sendMessage({  
        "push_clientid": obj.cids, // 设备id，支持多个以数组的形式指定多个设备，如["cid-1","cid-2"]，数组长度不大于1000  
        "title": obj.title, // 标题  
        "content": obj.content, // 内容  
        "payload": obj.data, // 数据  
        "force_notification": true,  // true 自动创建通知栏，没有回调；false 无通知栏，触发onMessage回调 
        "request_id": obj.request_id ,//请求唯一标识号，10-32位之间；如果request_id重复，会导致消息丢失  
        "options":obj.options //消息分类，没申请可以不传这个参数  
    })  
    return res;  
};
```

### 设置手机通知权限提示

> setPermissions,这个方法可以放入onLaunch，但是其下方不能放入其他方法，会被阻止，所以需要将对应方法加入到created中（虽然可以放到上方，但是测试发现使用uni自带api获取远端信息的方法加入后，setPermissions无法正常运行）
> 

```jsx
// 设置手机通知权限
			setPermissions() {
				let bool = uni.getStorageSync("notificationStatus");
				// #ifdef APP-PLUS  
				if (plus.os.name == 'Android') { // 判断是Android
					var main = plus.android.runtimeMainActivity();
					var pkName = main.getPackageName();
					var uid = main.getApplicationInfo().plusGetAttribute("uid");
					var NotificationManagerCompat = plus.android.importClass("android.support.v4.app.NotificationManagerCompat");
					//android.support.v4升级为androidx
					if (NotificationManagerCompat == null) {
						NotificationManagerCompat = plus.android.importClass("androidx.core.app.NotificationManagerCompat");
					}
					var areNotificationsEnabled = NotificationManagerCompat.from(main).areNotificationsEnabled();
					// 未开通‘允许通知’权限，则弹窗提醒开通，并点击确认后，跳转到系统设置页面进行设置  
					if (!areNotificationsEnabled && !bool) {
						uni.showModal({
							title: '通知权限开启提醒',
							content: '您还没有开启通知权限，无法接受到消息通知，请前往设置！',
							// showCancel: false,
							confirmText: '去设置',
							success: function(res) {
								if (res.confirm) {
									var Intent = plus.android.importClass('android.content.Intent');
									var Build = plus.android.importClass("android.os.Build");
									//android 8.0引导  
									if (Build.VERSION.SDK_INT >= 26) {
										var intent = new Intent('android.settings.APP_NOTIFICATION_SETTINGS');
										intent.putExtra('android.provider.extra.APP_PACKAGE', pkName);
									} else if (Build.VERSION.SDK_INT >= 21) { //android 5.0-7.0  
										var intent = new Intent('android.settings.APP_NOTIFICATION_SETTINGS');
										intent.putExtra("app_package", pkName);
										intent.putExtra("app_uid", uid);
									} else { //(<21)其他--跳转到该应用管理的详情页  
										intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
										var uri = Uri.fromParts("package", mainActivity.getPackageName(), null);
										intent.setData(uri);
									}
									// 跳转到该应用的系统通知设置页  
									main.startActivity(intent);
								} else {
									console.log(res,"cancel");
									uni.setStorageSync("notificationStatus", false);
									
								}
							}
						});
					}
				} else if (plus.os.name == 'iOS') { // 判断是ISO
					var isOn = undefined;
					var types = 0;
					var app = plus.ios.invoke('UIApplication', 'sharedApplication');
					var settings = plus.ios.invoke(app, 'currentUserNotificationSettings');
					if (settings) {
						types = settings.plusGetAttribute('types');
						plus.ios.deleteObject(settings);
					} else {
						types = plus.ios.invoke(app, 'enabledRemoteNotificationTypes');
					}
					plus.ios.deleteObject(app);
					isOn = (0 != types);
					if (isOn == false && !bool) {
						uni.showModal({
							title: '通知权限开启提醒',
							content: '您还没有开启通知权限，无法接受到消息通知，请前往设置！',
							// showCancel: false,
							confirmText: '去设置',
							success: function(res) {
								if (res.confirm) {
									var app = plus.ios.invoke('UIApplication', 'sharedApplication');
									var setting = plus.ios.invoke('NSURL', 'URLWithString:', 'app-settings:');
									plus.ios.invoke(app, 'openURL:', setting);
									plus.ios.deleteObject(setting);
									plus.ios.deleteObject(app);
								} else {
									uni.setStorageSync("notificationStatus", false);
								}
							}
						});
					}
				}
				// #endif  
			},
```

### 清除缓存

1. 移除指定缓存

```
uni.removeStorageSync("key值"); 
```

1. 清空所有缓存

```
uni.clearStorageSync(); 
```