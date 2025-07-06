# 简介

该工具为我个人23年研发的web3交易员交易工具，有超过一亿美金的交易额测试

前端由react+redux+antd 构成

后端由python + C++ 构成

特色是可以使用键盘像打游戏一样进行交易，而且配置的自由度超过市面上的全部产品

可以同时暂时全合约市场的交易对裸K（目前是440个），并且根据拟定的排序规则快速排序

服务端程序下载地址 https://ggggame.oss-cn-shenzhen.aliyuncs.com/professionTradeServer_public.rar

前端程序下载地址 https://ggggame.oss-cn-shenzhen.aliyuncs.com/react-front-public.rar

此外本人有付费的量化交易实盘源码和思路，具体请进入 http://melelery.com 查看

如github图片加载不出，请通过知乎文章阅读以下内容 https://www.zhihu.com/people/melelery

# 前端操作

下面两个配置截图展示了系统绝大部分功能

![image](https://github.com/user-attachments/assets/e34d99c3-c729-43c0-8a3b-de164c8b21a0)

![image](https://github.com/user-attachments/assets/54f8529d-ef4e-4d9c-91d2-06dc3a30c107)


![image](https://github.com/user-attachments/assets/e5f0b19a-56f3-4aba-911a-6701fb06b97c)

首先，这就是整个交易页面，可同时展示440个交易对的裸k

当时设计这套工具做的策略，其实有两个，一个是在市场联动性暴跌的时候，寻找那些站得稳的交易对，然后开多

基于此，我将btc和eth固定在前两位，方便观察全市场的行情

其次，在右下端配置里面，可以设定排序规则，可以使用快捷键迅速切换排序规则，默认小键盘 4 5 6 7 8 9 分别对应区间涨幅度排序/区间降幅度排序/区间波动率排序/最新一条线涨幅排序(如果你这时候前端设置的是1分钟k线，那就是1分钟k线...）/最新一条线跌幅排序

设定完成后，可以手动刷新（默认数字键盘0），也可以自动刷新，自动刷新的时间间隔同样可以通过配置完成

至此你可以发现，通过这套工具，可以非常快速的筛选出 一定时间比例内，“站得稳的交易对”，或者是在反弹的时候，反弹力度最大的交易对

然后通过快捷键a，可以迅速市价开多（默认）

当时我有市商账号，所以我需要执行仅挂单的交易，同时需要拆单，这些操作均可以通过预设定完成


![image](https://github.com/user-attachments/assets/050c4bb2-329d-49d3-a2c2-c7e9326fdd22)

在配置页面，我可以把a更改为，但我按a的时候，自动按照盘路口买一卖一，每单的价格高万分之一，或者是低万分之一，拆成5/10/15单并且执行...

下单之后，最重要的当然是止损，此时你可以通过快捷键执行你预设的止损方案，也可以通过配置，在检测到有仓位的时候，前端会自动下一个止损单

![image](https://github.com/user-attachments/assets/86500b9f-36ca-4ba8-913c-ea8129454856)

前端自动止损有四种方式，如图

平仓和开仓同理，默认快捷键是r，同样可以拆单形式的平仓

而这套工具执行过的第二个策略

这是做快速涨幅和跌幅币种的反弹

所以上面的快速排序在这个时候就非常有用，我可以最快速度的掌握到市场目前异动的币种，同时还可以判断出他是单独异动，还是跟随市场异动

同时还有声音播报，你可以在设定里面设定，如五分钟涨幅1%就播报提醒等等，声音用的是球探网进球的声音

此外，该工具有最大仓位锁定，可以防止你按的太快下多了额度

也有冷静期和交易对屏蔽，辅助交易员心态



# 服务器配置

服务器执行的文件位于professionTradeServer压缩包内
该工具至少需要6台云服务器，建议搭设在香港或者是所在地（非中国大陆内地地区，内地地区无法读取币安api），因是交易员交易工具，首先考虑的是服务器与自身所在地的延迟而非到币安的延迟
![image](https://github.com/user-attachments/assets/2b8f644d-4e0f-422c-9ba8-c52a91f9fdf9)

添加图片注释，不超过 140 字（可选）
六台云服务器分别命名为
wsServer 
ordersServer
positionServer
depthServer
oneMinKline
otherKline

遵循服务器命名规则后，可以在本地使用 professionTradeServer文件夹里的 uploadTradeServer.py 简单的上传和执行所有程序


请确保所有服务器均已上传binance_f,binance_spot,binance_d三个交易api的包文件夹

其中
wsServer 服务器
运行tradeServer.py文件，作为登录注册，配置保存服务器，要求开启8888端口
运行wsServer.out文件，作为websocket服务器，要求开启3878端口
使用 g++ wsServer.cpp -o wsServer.out -lboost_system 编译，编译服务器需具备websocketpp库，且至少2g内存
运行futuresAllTickToWs.py，读取全币种的最优挂单，前端会根据此形成最新价格，并且对最新的一条K线进行拟合
ordersServer 服务器
运行tradeServer.py文件
读取订单信息，整理交易信息进行盈利统计等等的web服务器

positionServer 服务器
运行tradeServer.py文件
读取挂单信息的web服务器，读取当前挂单（0.5s频率）

depthServer 服务器
运行tradeServer.py文件
读取depth的web服务器，读取选中交易对的盘口信息
此处分成三台web服务器是因为读取的接口有ip限制，web服务器可使用1核1g的服务器

oneMinKline
运行oneMinKline.py文件
读取1分钟k线的服务器
otherKline
运行otherKline.py文件
读取其他级别k线的服务器

前端会使用最新价格拟合k线，每隔30s左右和后端校对一次，所以对于时间幅度比较大的k线，不需要读取频率那么频繁

建议购买抢占式服务器，不需要的时候能随时释放，购买完成，请先确保3878端口和8888端口可以通讯
并且完成config的配置
执行 tradeServer.py 的时候，会自动创建相关需要的mysql表格
执行 uploadTradeServer.py 将币安目前的合约交易对录入数据库
在本地使用 professionTradeServer文件夹里的 uploadTradeServer.py 上传和执行所有程序后
请打开前端程序

将 src/work/constants/serverURL.js 中的两个固定参数，替换成你ws服务器的公网 ip

export const publicServerURL = "";

export const wsServerURL = "";

然后完成注册和登录，此处注册和登录是保存在你自己购买的数据库中，当初我设计这套系统的时候，设想是商业化的，并且还有直播功能，也就是交易员交易，其他人可以通过live页面全程观看，但是所有的信息均只存在于你自己购买的数据库，如果出现泄漏请勿找我

然后回到服务端程序这边，在serverConfig.py里面填写三个http服务器的公网ip信息和你刚才注册的账号，执行

然后回到网页，输入你的api key 信息，http服务器会自动判断该api时候有效，同上，所有信息均保存于你自己的数据库，你可以打开f12查看，没有多余的网络请求

至此即可正常使用


# 前端配置

前端是由react+redux+antd 写的web网页

个人安装环境为 node-v16.20.2 

首先在本地运行npm start

你也可以在配置完成后运行npm run build 后，上传到公网服务器

将 src/work/constants/serverURL.js 中的两个固定参数，替换成你ws服务器的公网 ip

export const publicServerURL = "";

export const wsServerURL = "";

确保你的服务端配置已经完成

进入 http://localhost:3028/#/ 本地页面


![image](https://github.com/user-attachments/assets/633f23da-b1da-4648-bad7-5bf1e27ed688)

先注册一个账号，此处注册是保存在你自己的数据库，主要用于后面配置的云端保存



![image](https://github.com/user-attachments/assets/924535e8-1aa3-479d-b2a5-40ca2cc3efeb)

注册完成自动登录跳转到这个页面

回到服务端程序这边，在serverConfig.py里面填写三个http服务器的公网ip信息和你刚才注册的账号，执行

刷新网页后


![image](https://github.com/user-attachments/assets/da1015cc-2f1f-4c06-8b30-b431ee8ab7e4)


跳转到这个页面，绑定api，请注意，当初我设计这套系统的时候，设想是商业化的，并且还有直播功能，也就是交易员交易，其他人可以通过live页面全程观看，所以有这一系列保存服务器的操作，但是所有的信息均只存在于你自己购买的数据库，如果出现泄漏请勿找我

绑定完成后就可以进入交易系统


