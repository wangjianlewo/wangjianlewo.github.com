---
layout: post
title: "iOS集成支付宝快捷支付"
date: 2014-01-21 11:18:23 +0800
comments: true
categories: 
---

越来越多的应用中,使用支付宝快捷支付了,支付宝也提供了快捷支付的sdk.
而且只要集成了支付宝的sdk后,sdk会自动判断设备是否安装了支付宝客户端:若安装,则直接调用支付宝客户端;若没有安装,会提示下载,点击取消下载则直接跳到网页支付,sdk考虑的很周到了,不用大家费心啦.

### 1,支付宝快捷支付链接地址(其实很难找):
https://b.alipay.com/order/productDetail.htm?productId=2013080604609654&tabId=4#ps-tabinfo-hash
### 2, 下载后运行:

下载集成开发包 之后,一路: 客户端demo--iOS_SDK--AlipaySdkDemo ,打开运行, 一个错误 :Linker command failed with exit code 1 (use -v to see invocation)

 <img src="/images/2014-01-21/2014-01-21-buildError.png" alt="me">
 
是不是很悲伤?不要悲伤,只要将项目中Frameworks文件夹下的.a 文件链接删除后,重新添加即可.

### 3,关于PartnerConfig.h :

关键的支付宝账户信息在 PartnerConfig.h 中,首先可以将此文件中的数据,替换成自己的,先测试自己活得id和keys是否正确.

获取方式如下:    
a,合作者身份ID（partner）和交易安全校验码（key）链接地址:http://help.alipay.com/support/help_detail.htm?help_id=243726    
b,客户的私钥链接地址:  http://help.alipay.com/support/help_detail.htm?help_id=253654&sh=Y&tab=null&info_type=9    
c,支付宝公钥都是相同的,直接使用demo中的公钥即可

注意事项:    
1,如果客户私钥错误,运行程序后,会报这个错: 请求参数错误,请稍后重试.     
2,如果客户私钥与网站上上传的不匹配,会报这个错: 支付宝rsa_private read error : private key is NULL

使用demo,测试通过后,就可以将支付宝添加到自己的项目中去了.

### 4,具体添加流程 :
结合查看---集成包中的移动快捷支付应用集成接入包支付接口接入与使用规则.pdf

####a,  添加必要的头文件、bundle、库文件 
####b,  添加自定义URL Scheme  
####c,  goToPay 
	-(void)goToPay
	{	
		NSString *appScheme = @"TestAliPay";
		NSString* orderInfo = [self getOrderInfo];
		NSString* signedStr = [self doRsa:orderInfo];
		NSLog(@"%@",signedStr);
		
		NSString *orderString = [NSString stringWithFormat:@"%@&sign=\"%@\"&sign_type=\"%@\"",orderInfo, signedStr, @"RSA"];
		[AlixLibService payOrder:orderString AndScheme:appScheme seletor:_result target:self];
		NSLog(@"payOrder %@ \nscheme %@ \nselector ",orderString,appScheme);		 
	}

注意事项:    
记得给_result 赋值: SEL  _result = @selector(paymentResult:);   否则应用内支付成功后,不会调paymentResult 这个函数的.

####d,  getOrderInfo (关于那些订单信息) 
		-(NSString*)getOrderInfo
		{
			AlixPayOrder *order = [[AlixPayOrder alloc] init];
			order.partner = PartnerID;
			order.seller = SellerID;
			order.tradeNO = [self generateTradeNO]; //order id
			order.productName = @"AlipayTest"; 
			order.productDescription = @"AlipayTest";
			order.amount = [NSString stringWithFormat:@"0.01"];
			order.notifyURL =  @"http://www.xxx.com";
			return [order description];
		}
注意事项:   
1,generateTradeNO 这个函数,仅仅是支付宝为了给广大开发者进行测试,写的一个随机生成订单号的函数! 自己支付的时候,需要将自己的后台返回的唯一订单号赋值给tradeNO. (真的有人照抄支付宝的这个函数的,然后订单号不对,还很疑问...)    
2,支付宝服务器通知:  notifyURL记得写哦,不然,后台收不到支付成功的通知
####e,  支付成功后 
应用外快捷支付(即网页支付)返回结果:通过 AppDelegate 的方法 handleOpenURL 获取 (详情可参考 demo 中 AppDelegate.m)的 handleOpenURL 方法,支付结果的详细信息详见接口文档中的同步返回参数说明。    
注意事项:应用内和应用外的两个成功支付的接口需同时实现!

###iOS添加支付宝快捷支付,正式完成!