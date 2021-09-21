# zbluenet-tankgame
基于zbluenet 实现的网络版坦克游戏(帧同步)

##### 技术

```
1. zbluenet-cpp 创建游戏业务服，简称 zoneServer
2. zbluenet-go  创建游戏数据库服, redis 存储业务数据, 简称 DBServer
3. zbluenet-go  创建游戏战斗服, 简称 battleServer

数据传输
1. 登录: 玩家通过tcp 发送login data 请求到 zoneserver, zoneserver 到dbserver 检验数据，返回到数据zone, zone 返回应答给client
2. 匹配: 玩家A 通过tcp 发送匹配请求到 zoneserver, zoneserver 将请求转发到battlerserver,
        如果已经有房间， 则随机分配一个房间A，将玩家加入房间A等待， 应答返回给zone， zone返回战斗服的地址信息给玩家A, 同时广播玩家A加入房间A的消息给房间A 的所有玩家
        如果没有房间， 创建一个新房间A， 玩家A 是房主，等待其他人加入, 应答返回给zone， zone返回战斗服的地址信息给玩家A
3. 玩家准备战斗
       玩家A 发送准备战斗的请求到zoneserver, zoneserver 转发请求到battleserver, 找到玩家A所在房间A， 修改房间内玩家A 的状态, 同时遍历房间A内所有玩家的状态是否都就绪，
       如果都已就绪, 则通过tcp将房间内的userid 发送到zoneserver, zone遍历userid， 广播进入战斗消息给所有玩家， 玩家收到消息后连接到战斗服开始战斗
4, 玩家战斗
   战斗服按房间进行管理， 所有玩家通过udp 协议和战斗服通讯， 玩家可以移动， 发送子弹， 子弹可以造成伤害, 当一方死亡， 则游戏结束
5， 游戏结束
   battleserver 销毁房间， 玩家退出战斗场景， 战斗服销毁房间内玩家的udp连接资源， 玩家客户端弹出结算界面
6. 断线重连
   battleserver 会缓存一场战斗的所有帧序列，玩家断线重连上时， 会下发所有历史帧， 玩家客户端做追帧处理
```
