---
layout: page
title: 跳一跳刮臂之路
---

## 获取微信应用跳一跳的session_id（抓包）

获取session_id 修改指定微信号的跳一跳成绩。fiddler+https的fiddler证书，其中手机中得包括fiddler的https证书，才能被fiddler截获，同时电脑也得安装fiddler证书。具体过程如下

### 下载fiddler

[Free Download](https://www.telerik.com/download/fiddler)

### fiddler安装ca证书以及IOS安装fiddler发放的证书

[使用Fiddler进行iOS APP的HTTP/HTTPS抓包](http://blog.csdn.net/skylin19840101/article/details/43485911)

TIPS：这一步需要注意的点是ca证书下载完成之后，必须在设置->通用->关于本机->证书信任设置里面开启

### 登录微信跳一跳

打开fiddler，打开微信可以发现session_id（右侧）：

![fiddler](/public/source/draft.png)

记下此时的session_id。

## 模拟发送https数据包

python写的，改了一下知乎大神商雅森爱萝莉的代码。因为他的那个代码不行了，action_data验证不过关。

```python
import requests

import json

from Crypto.Cipher import AES  # AES数据加密库

import base64

action_data = {

    "score": 2018,  # 分数

    "times": 444    # 尝试次数

}

session_id = "你的session_id"

aes_key = session_id[0:16]

aes_iv  = aes_key

cryptor = AES.new(aes_key, AES.MODE_CBC, aes_iv)

str_action_data = json.dumps(action_data).encode("utf-8")

length = 16 - (len(str_action_data) % 16)

str_action_data += bytes([length])*length

cipher_action_data = base64.b64encode(cryptor.encrypt(str_action_data)).decode("utf-8")  # 加密之后的AES分数数据格式，可以print出来看

post_data = {

  "base_req": {

    "session_id": session_id,

    "fast": 1,

  },

  "action_data": cipher_action_data

} # 携带的post数据

headers = {

    "charset": "utf-8",

    "Accept-Encoding": "gzip",

    "referer": "https://servicewechat.com/wx7c8d593b2c3a7703/5/page-frame.html",

    "content-type": "application/json",

    "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 1101 like Mac OS X) AppleWebKit/604.1.38 (KHTML, like Gecko) Mobile/15A402 MicroMessenger/6.6.1 NetType/WIFI Language/zh_CN",

    "Host": "mp.weixin.qq.com",

    "Connection": "Keep-Alive"

}  # http报文头部

url = "https://mp.weixin.qq.com/wxagame/wxagame_settlement"

response = requests.post(url, json=post_data, headers=headers)

print(json.loads(response.text))  # 成功返回errcode:0

```

填入session_id，`python jump.py`运行。如果errcode:0 ，重新登陆一下微信即可看到。

写的比较简单，不懂电联。感谢[@商雅森爱萝莉](https://zhuanlan.zhihu.com/p/32473340)。

email:[heyunfan1996@gmail.com](mailto:heyunfan1996@gmail.com)

weibo:[@城南走位王何老汉](https://weibo.com/u/2923167807)

weixin也行。

**datagrdview**







