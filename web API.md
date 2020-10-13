## 蓝牙 WebBluetooth
- 从浏览器连接到设备，调用函数 `navigator.bluetooth.requestDevice()`  
- 使用`filters`过虑器，查看前缀`PLAYBULB`的设备，指定`0xff0f`服务 
```JavaScript
let device = await navigator.bluetooth.requestDevice({
    filters: [ 
        { namePrefix: 'PLAYBULB' } 
    ],
    optionalServices: [ 0xff0f ]
});
// 用户必须通过选择设备来手动连接 
// 通过调用设备gatt属性上的connect()函数连接到GATT服务器并等待返回结果 
let server = await device.gatt.connect();
// 连上服务器后 调用getPrimaryService()并传递服务UUID，等待结果返回
let service = await server.getPrimaryService(0xff0f);
// 使用特性的 UUID 作为参数调用服务上的 getCharacteristic()并再次等待结果返回
// 等到可用于数据读写的特性
let characteristic = await service.getCharacteristic(0xfffc);
// 写数据 
characteristic.writeValue(new Unit8Array9([0,r,g,b]));
// 读数据
let value = await characteristic.readValue();
let r = value.getUint8(1);
let g = value.getUint8(2);
let b = value.getUint8(3);
// 获取 通知变更
characteristic.addEventListener(
    'characteristicvaluechanged', e => {
        let r = e.target.value.getUint8(1); 
        let g = e.target.value.getUint8(2);
        let b = e.target.value.getUint8(3);
    }
);
//由于蓝牙网络上的带宽有限，我们必须通过调用特性上的 startNotifications() 来手动启动这个通知机制。 否则，网络将被不必要的数据淹没。
// 此外，由于这些设备通常使用电池供电，因此没有必要的数据通信会影响设备的电池寿命，所以内置无线发射器不需要常开
characteristic.startNotifications();
```
## 摄像头 麦克风 
- `navigator.getUserMedia`(已经从标准中删除,不支持safari)
- `navigator.mediaDevices.getUserMedia`(safari浏览器可能不支持)
```HTML
<body>
    <video id="v"></video>
    <script>
        !(function () {
            // 老的浏览器可能根本没有实现 mediaDevices，所以我们可以先设置一个空的对象
            if (navigator.mediaDevices === undefined) {
                navigator.mediaDevices = {};
            }
            if (navigator.mediaDevices.getUserMedia === undefined) {
                navigator.mediaDevices.getUserMedia = function (constraints) {
                    // 首先，如果有getUserMedia的话，就获得它
                    var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia || navigator.msGetUserMedia;

                    // 一些浏览器根本没实现它 - 那么就返回一个error到promise的reject来保持一个统一的接口
                    if (!getUserMedia) {
                        return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
                    }
                        // 否则，为老的navigator.getUserMedia方法包裹一个Promise
                    return new Promise(function (resolve, reject) {
                        getUserMedia.call(navigator, constraints, resolve, reject);
                    });
                }
            }
            const constraints = {
                video: true,
                audio: false
            };
            
            let promise = navigator.mediaDevices.getUserMedia(constraints);
            promise.then(stream => {
                let v = document.getElementById('v');
                // 旧的浏览器可能没有srcObject
                if ("srcObject" in v) {
                    v.srcObject = stream;
                } else {
                    // 防止再新的浏览器里使用它，应为它已经不再支持了
                    v.src = window.URL.createObjectURL(stream);
                }
                v.onloadedmetadata = function (e) {
                    v.play();
                };
            }).catch(err => {
                console.error(err.name + ": " + err.message);
            })

        })();
    </script>
</body>
```