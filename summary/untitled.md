# README



![](../.gitbook/assets/image.png)



**注意事项：**

1.分页查询需要带入QryIndex\(带空则回第一页\)，每页50笔数据，拿最后一笔QryIndex信息可以往下查，最后一页则回空数据。

2.一分钟内需要向service发送PING或者有发送请求讯息，超过一分钟service没收到响应则会清掉该联机session。
