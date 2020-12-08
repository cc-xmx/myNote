http为tcp的应用层

websocket 可以想象成http的应用层

websocket报文：

````shell
GET /uin=xxxxxxxx&app=xxxxxxxxx&token=XXXXXXXXXXXX HTTP/1.1
Host: server.example.cn:443
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36
Upgrade: websocket
Sec-WebSocket-Version: 13
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: user_id=XXXXX
Sec-WebSocket-Key: 1/2hTi/+eNURiekpNI4k5Q==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
Sec-WebSocket-Protocol: binary, base64
````



http报文：

![img](websocket.assets/20170707143243946)