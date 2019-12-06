# mi-aqara-sdk-ningmod

- 本项目从 jsonzhou 的 [mi-aqara-sdk 项目](https://github.com/zzyss86/mi-aqara-sdk)而来，主要是原工程很久没有维护，已经不适合绿米的最新设备和局域网协议。
- 按照绿米的[AIOT论坛](http://bbs.opencloud.aqara.com:8080/jforum/posts/list/59.page)，局域网开发支持的网关目前只有：空调伴侣升级版（lumi.acpartner.v3），固件版本需达到1.4.1_109以上。jsonzhou 开发该项目时使用的网关，官方已经不支持。
- 原先从 APP 获取网关 key 的方法已经无法使用，需要登录绿米的 AIOT 网站申请个人开发者才可以。
- 请注意！原先工程只在package.json中申明了 ISC 版权，而没有在每个源代码文件中添加。因此商业使用可能存在一定风险。

## 测试代码
```
 mkdir aqara
 cd aqara
 git clone https://github.com/chou-o-ning/mi-aqara-sdk-ningmod
 vim test.js
```
 
## test.js代码
```
const MiAqara = require('./mi-aqara-sdk-ningmod');

MiAqara.create([{
    sid: 'xxxxxxxxxxxx', // mac 地址
    password: 'XXXXXXXXXXXXXXXX'  // key
}], {
    onReady (msg) { // 网关及子设备已找到, 多个网关可能会调用多次
//  console.log('onReady', msg);
    },
    onMessage (msg) { // 所有消息类型
//  console.log('onMessage', msg);
    }
});

MiAqara.start();
```

## 运行测试代码
```
node test.js
```
如果测试的 PC 在网关的局域网内，允许测试代码后，可以看到网关的消息交互和心跳报文。

## *************************************************************
## 注意：以下内容来自原工程的README.md，很多内容已经不合时宜，但为了尊重原作者的版权，这里还是保留。
## *************************************************************

## mi-aqara-sdk

作者：jsonzhou 2018/02/10

小米(xiaomi)、绿米(aqara)、米家智能家庭(mijia)开发工具库。

## 背景

小米IoT开发者平台不支持个人开发者申请，有一些极客的玩法没有API实现不了。通过`mi-aqara-sdk`可以在Node.js平台实现米家智能家庭的互联互通。

`mi-aqara-sdk`只在局域网中通讯，使用的协议或机制主要有：udp协议，组播，AES加解密。

## 基本使用

### 安装

	npm install --save-dev mi-aqara-sdk
	
### 使用

	const MiAqara = require('mi-aqara-sdk');
	MiAqara.create(gateways, opts); // 创建
	MiAqara.start(); // 启动

## 接口

### create(gateways, opts) 创建SDK服务

- gateways 网关列表，支持数组(多个)或对象(单个)
	- sid 网关设备ID （获取方式见文末）
	- password 网关密码 （获取方式见文末）
	- iv [可选]加密初始向量，有默认值
- opts 服务选项
	- multicastAddress 组播IP，默认：224.0.0.50
	- multicastPort 组播端口，默认：4321
	- serverPort 服务监听端口，默认：9898
	- bindAddress SDK所在设备具有多网络时需要设置
	- onReady 网关及子设备已就绪, 多个网关可能会调用多次
	- onMessage 所有消息回调

#### 示例

		MiAqara.create([{
		    sid: '7811dcb28bde',
		    password: '**A68343AD********'
		}], {
		    onReady (msg) { // 网关及子设备已找到, 多个网关可能会调用多次
		        // console.log('onReady', msg);
		    },
		    onMessage (msg) { // 所有消息类型
		        // console.log('onMessage', msg);
		    }
		});
		
#### 事件`onReady`,`onMessage`中的`msg`结构示例

	{
		"cmd": "read_ack",
		"model": "switch",
		"sid": "158d0001bf542b",
		"short_id": 2124,
		"data": "{\"voltage\":3012}"
	}
	
#### `cmd`枚举列表(消息类型)

- iam 查找网关时，网关的响应包
- get_id_list_ack 网关查找子设备列表时消息响应 
- report 网关及子设备状态变化时主动上报
- read_ack 读设备时消息响应
- write_ack 写设备时消息响应
- heartbeat 心跳包，网关每10秒钟发送一次, 主要更新网关token。子设备心跳，插电设备10分钟发送一次，其它1小时发送一次
- server_ack 通用回复, 如发送报文JSON解析出错，会回复此事件
		
### start() 启动，一般创建完后马上启动

	MiAqara.start();

### getGatewayBySid(sid) 根据网关设备ID查找网关对象

	MiAqara.getGatewayBySid('7811dcb28bde')
	
### getGatewayList() 获取所有网关列表数组

	MiAqara.getGatewayList();
	
### controlLight(opts) 控制彩灯

- opts.sid 网关设备ID
- opts.power 开关
- opts.hue 色相
- opts.saturation 饱和度
- opts.brightness 亮度

#### 关灯
	MiAqara.controlLight({sid:'7811dcb28bde',power:false});

#### 开灯
	MiAqara.controlLight({sid:'7811dcb28bde',power:true});
	
### getDeviceBySid(sid) 获取所有子设备(感应器)列表数组

	MiAqara.getDeviceBySid('158d0001b8849f');
	
### getDevicesByGatewaySid(gatewaySid) 根据网关ID查找所属子设备列表

	MiAqara.getDevicesByGatewaySid('7811dcb28bde')；
	
### getDevicesByGatewaySidAndModel(gatewaySid, model) 根据网关ID及子设备型号查找所属子设备列表

#### 获取网关下所有开关子设备

	MiAqara.getDevicesByGatewaySidAndModel('7811dcb28bde', 'switch');
	
#### 子设备型号及中英文名称对照

以下key为model(型号)

	const DEVICE_MAP = {
	    'gateway': {name:'Gateway', name_cn:'网关'},
	    'magnet': {name:'ContactSensor', name_cn:'门窗磁传感器'},
	    'motion': {name:'MotionSensor', name_cn:'人体感应'},
	    'switch': {name:'Button', name_cn:'按钮'},
	    'sensor_ht': {name:'TemperatureAndHumiditySensor', name_cn:'温度湿度传感器'},
	    'ctrl_neutral1': {name:'SingleSwitch', name_cn:'单按钮墙壁开关'},
	    'ctrl_neutral2': {name:'DuplexSwitch', name_cn:'双按钮墙壁开关'},
	    'ctrl_ln1': {name:'SingleSwitchLN', name_cn:'单按钮墙壁开关零火版'},
	    'ctrl_ln2': {name:'DuplexSwitchLN', name_cn:'双按钮墙壁开关零火版'},
	    '86sw1': {name:'SingleButton86', name_cn:'86型无线单按钮开关'},
	    '86sw2': {name:'DuplexButton86', name_cn:'86型无线双按钮开关'},
	    'plug': {name:'PlugBase', name_cn:'插座'},
	    '86plug': {name:'PlugBase86', name_cn:'86型墙壁插座'},
	    'cube': {name:'MagicSquare', name_cn:'魔方'},
	    'smoke': {name:'SmokeDetector', name_cn:'烟雾警报器'},
	    'natgas': {name:'NatgasDetector', name_cn:'天然气警报器'},
	    'curtain': {name:'ElectricCurtain', name_cn:'电动窗帘'},
	    'sensor_magnet.aq2': {name:'ContactSensor2', name_cn:'门磁感应 第二代'},
	    'sensor_motion.aq2': {name:'MotionSensor2', name_cn:'人体感应 第二代'},
	    'sensor_switch.aq2': {name:'Button2', name_cn:'按钮 第二代'},
	    'weather.v1': {name:'TemperatureAndHumiditySensor2', name_cn:'温度湿度传感器 第二代'},
	    'sensor_wleak.aq1': {name:'WaterDetector', name_cn:'水浸传感器'}
	};

### getDevicesByModel(model) 根据型号获取所有对应子设备

#### 所有开关设备，包括挂载在不同网关的上子设备

	MiAqara.getDevicesByModel('switch')
	
### getDeviceList() 获取所有子设备列表数据

	MiAqara.getDeviceList()
	
### change ({sid, gatewaySid, model, data}) 子设备状态变更

- change({sid,data}) 改变指定设备(子设备ID:sid)的状态
- change({gatewaySid, model, data}) 改变指定网关下挂载的指定型号的子设备状态
- change({model, data}) 改变所有指定型号的子设备状态

## 其它说明

### 一、设备状态`data`结构说明 (部份)
	
#### 门窗磁传感器 (model: magnet)

	{
		"status": "close" //close:关闭; open:打开
	}

#### 人体感应器 (model: motion)

	{
		"status": "motion" //motion:有人移动
	}
	// 或
	{
		"no_motion" "120" //120秒没人移动
	}
	
#### 开关/按钮 (model: switch)

	{
		"status": "click" //click:单击; double_click:双击
	}

#### 温度湿度传感器 (model: sensor_ht)

	{
		"temperature": "1741",
		"humidity": "7593"
	}

#### 单按钮墙壁开关 (model: ctrl_neutral1)

	{
		"channel_0": "on" //off是关闭
	}

#### 双按钮墙壁开关 (model: ctrl_neutral2)

	{
		"channel_0": "on",
		"channel_1": "off"
	}

#### 单按钮墙壁开关零火版 (model: ctrl_ln1)

	{
		"channel_0": "on" //off是关闭
	}

#### 双按钮墙壁开关零火版 (model: ctrl_ln2)

	{
		"channel_0": "on",
		"channel_1": "off"
	}

#### 86型无线单按钮开关 (model: 86sw1)

	{
		"channel_0": "click" //click:单击; double_click:双击
	}

#### 86型无线双按钮开关 (model: 86sw2)

	{
		"channel_0": "click" //click:单击; double_click:双击
		"channel_1": "click" //click:单击; double_click:双击
	}

#### 插座 (model: plug)

	{
		"status": "on" //off是关闭
	}

#### 86型墙壁插座 (model: 86plug)

	{
		"status": "on" //off是关闭
	}

#### 魔方 (model: cube)

	{
		"status": ""
	}

status取值：

- move
- flip180
- tap_twice
- shake_air
- flip90

#### 烟雾警报器 (model: smoke)

	{
		"alarm": "0" // 0，1，2
	}

#### 天然气警报器 (model: natgas)

	{
		"alarm": "0" // 0，1，2
	}

#### 电动窗帘 (model: curtain)

	{
		"curtain_level": ""
	}

#### 门磁感应 第二代 (model: sensor_magnet.aq2)

	{
		"status": "close" //close:关闭; open:打开
	}

#### 人体感应器 第二代 (model: sensor_motion.aq2)

	{
		"status": "motion" //motion:有人移动
	}
	// 或
	{
		"no_motion" "120" //120秒没人移动
	}

#### 按钮/开关 第二代 (model: sensor_switch.aq2)

	{
		"status": "click" //click:单击; double_click:双击
	}

#### 温度湿度传感器 第二代 (model: weather.v1)

	{
		"temperature": "1741",
		"humidity": "7593"
	}

#### 水浸传感器 (model: sensor_wleak.aq1)

	{
		"status": "leak" //leak:水浸; no_leak:没有水浸
	}
	
### 二、获取网关sid及密码

#### 1. 打开米家APP - 点击多功能网关 - 点击右上角'...'进入设置 - 关于

![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/1.jpeg)
![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/2.jpeg)
![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/3.jpeg)

#### 2. 在关于页面的下方空白处快速点击，直到隐藏菜单出现

![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/4.jpeg)
![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/5.jpeg)


#### 3. 点击-局域网通信协议，开启并复制随机生成密码

![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/6.jpeg)

#### 4. 点击-网关信息

![](https://raw.githubusercontent.com/zzyss86/mi-aqara-sdk/master/images/7.jpeg)

mac=后面的一串即为网关的SID，去掉“:”，转换成小写使用

----
趁年底周末折腾了一下，祝大家2018春节快乐！

