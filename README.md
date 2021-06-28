# qcloud-iotexplorer-bluetooth-adapter

这个 sdk 实现了发现蓝牙设备，管理蓝牙连接等功能，支持 h5 和小程序两种环境。

## 安装

```bash
npm i qcloud-iotexplorer-bluetooth-adapter
```
## Get Started

```ts
import { BlueToothAdapter4Mp, BlueToothActions, DeviceAdapter } from 'qcloud-iotexplorer-bluetooth-adapter';

const blueToothActions: BlueToothActions = {
  registerDevice: () => {}, // 
}

const deviceAdapters: DeviceAdapter[] = [];
const blueToothAdapter = new BlueToothAdapter({
  blueToothActions,
  deviceAdapters
});
```
## API

### BlueToothAdapter4Mp
<br/>
这个类提供小程序环境下蓝牙设备的搜索，设备的连接，DeivceAdapter 的管理等功能。

**new BlueToothAdapter4Mp(options: BlueToothAdapterProps)** 

实例化一个BlueToothAdapter。`options`可传入：

```ts
interface BlueToothAdapterProps {
  /**
   * 一个 DeviceAdapter 数组，用来在连接设备时使用对应的 deviceAdapter。 
   */
  deviceAdapters?: DeviceAdapter[];
  /**
   * 
   */
  actions?: BlueToothActions;
  /**
   * 当面板是 h5 面板时，用于和 h5 面板通信
   */
  h5Websocket?: H5Websocket;
  /**
   * 判断当前是不是处于开发模式，对于 h5 面板有用
   */
  devMode?: (() => boolean) | boolean;
}
```

`options.actions`用于提供一系列的 action 供 blueToothAdapter 在发现设备，绑定设备的过程中调用， 需要由实现以下函数:

```ts
export interface BlueToothActions {
  initProductIds?: () => Promise<{ [propKey: string]: string; }>;
  /**
   * 向云端某个产品下注册找到的设备
  */
  registerDevice:(params: {
    deviceId?: string;
    deviceName?: string;
    productId?: string;
  }) => Promise<any>;
  // familyId 和 roomId 不一定会传，外面需要处理默认取当前家庭及房间
  bindDevice: (params: {
    deviceId?: string;
    deviceName?: string;
    productId?: string;
    familyId?: string;
    roomId?: string;
  }) => Promise<any>;
  reportDeviceData: (params: {
    deviceId?: string;
    deviceName?: string;
    productId?: string;
    data: any;
    timestamp: number;
  }) => Promise<any>;
}
```
### `blueToothAdapter.startSearch(options) => Promise<void>`

搜索蓝牙外围设备列表，options可以传入下面属性

```ts
export interface StartSearchParams {
  serviceIds?: string[]; // 用于筛选出特定 serviceId 的设备
  ignoreDeviceIds?: string[]; // 需要忽略的设备
  ignoreServiceIds?: string[]; // 需要忽略的设备
  timeout?: number; // 搜索超时时间，默认20s
  extendInfo?: any;
  onSearch?: (devices: BlueToothDeviceInfo[]) => any; // 这个回调函数中可以获得搜索到到设备列表
  onError?: (error: Error | object | string) => any; // 搜索发生错误时的回调
}

```

### `blueToothAdapter.stopSearch() => Promise<void>`

停止搜索设备

### blueToothAdapter.connectDevice(device: BlueToothDeviceInfo) => Promise<DeviceAdapter>

连接到某个设备，并获得一个`DeviceAdapter`实例，用于与蓝牙设备进行通信

### `blueToothAdapter.disconnectDevice()`

和某个蓝牙设备断开连接，也可以调用`deviceAdapter.disconnectDevice()`来断开蓝牙连接

---

### DeviceAdapter Class
<br/>
一个 deviceAdapter 可以用来接收设备的数据，也可以向设备发送数据，以及管理和设备的连接。要实现一个自定义的 diveceAdapter， 需要继承 DeviceAdapter 基类，并提供以下静态属性和方法：

```ts
export class ThermometerDeviceAdapter extends DeviceAdapter {
  // 当搜索到设备时，通过 serviceId 匹配设备适用的adapter
  static serviceId = '0000FFF0-0000-1000-8000-00805F9B34FB';

  static deviceFilter: DeviceFilterFunction = (deviceInfo, extendInfo) => {
    // 过滤出需要的设备
  }

  handleBLEMessage(hex): BLEMessageResponse {
    // 将蓝牙发过来的数据转换成 BLEMessageResponse
  }
}
```

### Static `DeviceAdapter.deviceFilter`

这个方法在

DeviceAdapter 实例在调用`blueToothAdapter.connectDevice`方法时返回，有以下实例方法

### `deviceAdapter.handleBLEMessage`

这个方法用来处理设备过来的消息


```
```
### 预置的 deviceAdapters


## h5版

由于在h5环境下无法调用小程序的蓝牙api，因此通过h5和小程序之间的 websocket 连接，实现蓝牙的搜索，绑定相关功能。一个可用的 demo 可以看[这里](https://github.com/tencentyun/iotexplorer-h5-panel-demo/blob/master/BLUETOOTH-README.md)

查看 h5 环境下，blueToothAdapter 的使用方式, 请移步： https://www.npmjs.com/package/qcloud-iotexplorer-h5-panel-sdk


