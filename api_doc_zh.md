#

2019/7/23
 
目录
一、 接口标准概述	3
1.	通讯规范	3
2.	响应格式	3
3.	响应码列表	3
4.	资金划拨状态列表	3
5.	HTPP请求头token	3
6.	白名单ip列表	4

二、 接口列表	4
1.	资金账号开户接口	4
2.	资金转账接口接口	6
3.	总帐转账接口	7
4.	资金批量转账接口	9
5.	账户余额查询接口	11
6.	转账明细查询接口	12
7.	登陆接口(目前过期时间8小时)	14


一、接口标准概述
1.	通讯规范
传输方式	为保证安全性，采用HTTPS传输
提交方式	采用POST方法提交
数据格式	提交和返回数据都为JSON
字符编码	统一采用UTF-8字符编码



2.	响应格式
{
	"code": 200,
	"msg": "成功",
	"data": {
 	}
}

3.	响应码列表
code	message
200	调用成功返回
401	认证不通过/token无效
500	系统有误
403	不在白名单内

4.	资金划拨状态列表
code	name	desc
0	SUC	资产转移成功
-1	RAE	转移资产账户不存在
-2	TAE	接收资产账户不存在
-3	NSF	金额不足
-4	AOV	金额溢出
-5	OPE	其他错误
-6	ACC
	已授理资产划拨

5 HTTP请求头token（目前过期时间8小时）
Authorization：<token>，除登录接口API外，其他接口都必须带上token请求头  key 为 “token”进行认证
  
6 白名单ip列表
 
修改sdk springboot包部署机器 配置目录下的block-chain.properties 文件(目前为/opt/block-chain.properties)
ip-white-list=${ip1},,,${ipn}

注意为逗号分隔的字符串，修改保存文件1秒钟后生效

二、接口列表
1.	资金账号开户接口
➢	URL
https://<host>:<port>/financial/asset/register

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
account	资金账号	String	是	-
currency	币种	Integer	是						
status	账号状态	Integer	是						

	例子：
{"account":"Bob", "currency":1, "status": 1}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	开户结果	Object	-
•	Data Body：
	
参数	中文描述	数据类型	是否必填	备注
assetAccount	开户账号详情	Object	是	
registerStatus	开户结果状态	String	是	-

•	AssetAccount Object
参数	中文描述	数据类型	是否必填	备注
account	资金账号	String	是	-
currency	币种	Integer	是						
status	账号状态	Integer	是						

	例子
{
    "code": 500,
    "message": "资产账号开户失败.",
    "data": {
        "assetAccount": {
            "account": "Bob",
            "amount": 0,
            "currency": "CNY",
            "accountStatus": "ACTIVATE"
        },
        "registerStatus": "资产账户已存在"
    }
}


2.	资金转账接口
➢	URL
https://<host>:<port>/financial/asset/transfer	

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	转出账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						
remark	备注	String	否						

	例子：

{"txnNo":"10001","fromAccount":"Alice", "toAccount":"Bob", "currency":1, "amount": 1 ,"remark":"测试备注" }

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	转账结果	Object	转账结果
•	 Data Body：
	
参数	中文描述	数据类型	是否必填	备注
assetTransfer	交易详情	Object(同传入参数)		
transferStatus	划拨状态	String		资金划拨状态列表
transactionHash	交易哈希	String		通过交易哈希获取交易状态

•	AssetTransfer Object
参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	转出账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						


	例子
{
    "code": 200,
    "message": "资金划拨成功.",
    "data": {
        "assetTransfer": {
            "txnNo": "10001",
            "fromAccount": "Alice",
            "toAccount": "Bob",
            "amount": 1,
            "currency": "CNY"
        },
        "transferStatus": "资产转移成功" ,
        "transactionHash": "0xebacdcf5a160419e2cce4c9b579a7ea601d07f39136149d0dd145aa967fda2a8"
    }
}

3.	总账转账接口
➢	前置条件需要初始化总账账户及其金额
➢	URL
https://<host>:<port>/financial/asset/ledger/transfer	

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						

	例子：

{"txnNo":"10001","toAccount":"Bob", "currency":1, "amount": 1}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	转账结果	Object	转账结果
•	 Data Body：
	
参数	中文描述	数据类型	是否必填	备注
assetTransfer	交易详情	Object	是	
transferStatus	划拨状态	String	是	资金划拨状态列表
transactionHash	交易哈希	String		通过交易哈希获取交易状态

•	AssetTransfer Object
参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	总账账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						


	例子
{
    "code": 200,
    "message": "资金划拨成功.",
    "data": {
        "assetTransfer": {
            "txnNo": "10001",
            "fromAccount": "Alice",
            "toAccount": "Bob",
            "amount": 1,
            "currency": "CNY"
        },
        "transferStatus": "资产转移成功" ,
        "transactionHash": "0xebacdcf5a160419e2cce4c9b579a7ea601d07f39136149d0dd145aa967fda2a8"
    }
}

4.	资金批量转账接口
➢	URL
https://<host>:<port>/financial/asset/batch/transfer	

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=utf-8
➢	request
参数	中文描述	数据类型	是否必填	备注
body	批量转账列表	Object Array	是	
	
•		Object Body：
	
参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	转出账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						


例子：
[
  {"txnNo":"10002","fromAccount":"Alice", "toAccount":"Bob", "currency":1, "amount": 1},
  {"txnNo":"10003","fromAccount":"Alice", "toAccount":"Bob", "currency":1, "amount": 1}	
]

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	转账结果	Object Array	-

•	data Object
	
参数	中文描述	数据类型	是否必填	备注
assetTransfer	交易详情	Object	是	
transferStatus	划拨状态	String	是	资金划拨状态列表
•	AssetTransfer Object
参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	转出账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						

例子:
{
    "code": 200,
    "message": "资金划拨结果.",
    "data": [
        {
            "assetTransfer": {
                "txnNo": "10002",
                "fromAccount": "Alice",
                "toAccount": "Bob",
                "amount": 1,
                "currency": "CNY"
            },
            "transferStatus": "资产转移成功"
        },
        {
            "assetTransfer": {
                "txnNo": "10003",
                "fromAccount": "Alice",
                "toAccount": "Bob",
                "amount": 1,
                "currency": "CNY"
            },
            "transferStatus": "资产转移成功"
        }
    ]
}
5.	账户余额查询接口
➢	URL
https://<host>:<port>/financial/asset/balance

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
account	资金账号	String	是	-
currency	币种	Integer	是	币种列表
				

	例子：
{"account":"Bob", "currency":1}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	账户余额/错误描述	Object	-

	例子
{
    "code": 200,
    "message": "查询资金余额成功。",
    "data": 922337180000000
}
6.	转账明细查询接口
➢	URL
https://<host>:<port>/financial/asset/detail

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
account	资金账号	String	是	-
currency	币种	Integer	是						
txnNo	交易号	String	是						

	例子：
{"account":"Bob", "currency":1, "txnNo":"10002"}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	响应码	Integer	详情
message	响应信息描述	String	详情
data	转账结果	Object Array	-

•	data Object
参数	中文描述	数据类型	是否必填	备注
txnNo	交易号	String	是	
fromAccount	转出账号	String	是	-
toAccount	转入账号	String	是	-		转入账号	String	是	-
amount	转账金额	Float(32,4）	是						
currency	币种	Integer	是						
remark	备注	String	是						

例子:
{
    "code": 200,
    "message": "资金划拨结果.",
    "data": [
        {
            "txnNo": "10002",
            "fromAccount": "Alice",
            "toAccount": "Bob",
            "amount": 1,
            "currency": "CNY",
		"status": "资产转移成功" ,
            "remark": "测试备注"	
        },
        {
            "txnNo": "10003",
            "fromAccount": "Alice",
            "toAccount": "Bob",
            "amount": 1,
            "currency": "CNY",
		"status": "资产转移成功" ,
            "remark": "测试备注"
         }
    ]
}
7.	登录接口(目前过期时间8小时)
➢	URL
https://<host>:<port> /financial/asset/login

➢	Http Headers
	Method：POST
	Content-Type：application/json;charset=UTF-8

➢	Body

参数	中文描述	数据类型	是否必填	备注
username	帐号	String	是	-
password	密码	String	是	-

	例子：
{
"username":"帐号",
"password":"密码"
}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	错误码	Integer	详情
msg	错误信息	String	错误的文字描述
data	登录信息	Object	-

data Object
参数	中文描述	数据类型	备注
token	认证标识	String	-

	例子
{
	"code": 200,
	"msg": " SUCCESS",
	"data": {
		"token": "<token>"
	}
}
8.	通过交易哈希获取交易状态
➢	URL
https://<host>:<port>/financial/asset/getTransactionInfoByTransactionHash

➢	Http Headers
Method：Get

➢	Params

参数	中文描述	数据类型	是否必填	备注
transactionHash	交易哈希	String	是	-

	例子：
{
" transactionHash":"0xfc1c51f416ef6b59bd49ae5895be022d070d8aa5cb46504b285e5631cd1ba980"
}

➢	响应
•	Content-Type：application/json;charset=utf-8
•	Body：
参数	中文描述	数据类型	备注
code	错误码	Integer	详情
msg	错误信息	String	错误的文字描述
data	登录信息	Object	-

data Object
参数	中文描述	数据类型	备注
code	交易状态编码	int	资金划拨状态列表
message	描述信息	String	资金划拨状态列表

	例子
{
	"code": 200,
	"msg": " SUCCESS",
	"data": {
		"code": 0,
        "message": "Successful asset transfer"
	}
}
