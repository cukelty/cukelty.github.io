---
title: Android上的NFC开发
date: 2017-09-02 16:34:33
tags: NFC
---
摘要：
最近了解了一下NFC相关开发，做一下记录哦

<!--more-->
正文：
#NFC开发

NFC是Near Field Communication缩写，即近距离无线通讯技术。NFC提供了一种简单、触控式的解决方案，可以让消费者简单直观地交换信息、访问内容与服务。NFC技术允许电子设备之间进行非接触式点对点数据传输，在十厘米(3.9英吋)内，交换数据，其传输速度有106Kbit/秒、212Kbit/秒或者424Kbit/秒三种。

##工作模式
###卡模式
例如QuickPay。

数据在支持NFC的手机或其它电子设备中，可以简单理解成“刷手机”。本质上就是将支持NFC的手机或其它电子设备当成借记卡、公交卡、门禁卡等IC卡使用。基本原理是将相应IC卡中的信息凭证封装成数据包存储在支持NFC的外设中 。在使用时还需要一个NFC射频器（相当于刷卡器）。将手机靠近NFC射频器，手机就会接收到NFC射频器发过来的信号，在通过一系列复杂的验证后，将IC卡的相应信息传入NFC射频器，最后这些IC卡数据会传入NFC射频器连接的电脑，并进行相应的处理（如电子转帐、开门等操作）。
####虚拟卡模式(Virtual Card Mode)
这些芯片的内部实际上运行一个微型JAVA虚拟机，，一个卡（手机）可以装多个applet，它有自己的证书，上层协议是卡片与芯片内置的Applet进行加密交互。通过OAT（空中发卡）业务可以实现把服务器中的Applet二进制文件下载到手机芯片中（是不是有点像HotFix技术？），俗称“卡包”。
#####主机卡模式(Host Card Mode)
Google提供HCE的方法，在Android开发中集成HostAptuService 服务，就可以实现卡模拟。
	
	CardReader < -- >NFCAdapter <----> HostApduService <----> BackendCloudServer



###读卡器模式
数据在NFC芯片中，可以简单理解成“刷标签”。本质上就是通过支持NFC的手机或其它电子设备从带有NFC芯片的标签、贴纸、名片等媒介中读写信息。通常NFC标签是不需要外部供电的。当支持NFC的外设向NFC读写数据时，它会发送某种磁场，而这个磁场会自动的向NFC标签供电。

sample：公交卡充值查询
###点对点模式
该模式与蓝牙、红外差不多，用于不同NFC设备之间进行数据交换，不过这个模式已经没有有“刷”的感觉了。其有效距离一般不能超过4厘米，但传输建立速度要比红外和蓝牙技术快很多，传输速度比红外块得多，如过双方都使用Android4.2，NFC会直接利用蓝牙传输。这种技术被称为AndroidBeam。所以使用androidBeam传输数据的两部设备不再限于4厘米之内。

<img src="http://upload-images.jianshu.io/upload_images/5443336-a02d3bde35fa7d5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"/>

##NFC的传输层协议栈
	JAVA中 byte 是有符号一字节的，而char是编码过的两个字节的；C中byte(也就是#define byte (unsigned char))是无符号一个字节的，而char是有符号一字节的; 为了方便，我们全部使用byte与16进制进行表示
APDU的数据结构，本质是一种编码。
发送的数据报



##NFC读卡器的Android开发
配置权限与feature
	
	<!--Use NFC feature and Permissions-->
	<uses-permission android:name="android.permission.NFC"/>
	<uses-feature
 	 android:name="android.hardware.nfc"
 	 android:required="true"/>
 	 
	注意配置Activity的模式为singleTop，配置Activity不可转变屏幕（防止Intent丢失，支付宝也是这样做的）,配置NFCTech过滤器，配置Intent接收器。

	<activity
    android:name=".NfcReaderActivity"
    android:launchMode="singleTop"
    android:alwaysRetainTaskState="true"
    android:label="@string/title_activity_nfcscanner">

      \<!--nfc filter-->
	<meta-data android:name="android.nfc.action.TECH_DISCOVERED" android:resource="@xml/nfc_tech_filter" />
	
   	<intent-filter>
      <action android:name="android.nfc.action.TECH_DISCOVERED" />
      <action android:name="android.nfc.action.TAG_DISCOVERED" />
      <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>


配置NFC卡片技术过滤器，每个卡对应一个 \<tech-list>
	
	/<!--file nfc_tech_filter.xml -->
	<resources>

  	/<!--重庆一卡通-->
  	<tech-list>
    <!--ISO 14443-4-->
    <tech>android.nfc.tech.IsoDep</tech>
    <!--ISO 14443-3A-->
    <tech>android.nfc.tech.NfcA</tech>
  	</tech-list>
	</resources>
	
获取Intent

在Activity 中针对收到的Intent进行处理，Oncreate进入的情况。
	
	@Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_nfcscanner);
    handleIntent(getIntent());
	}

	@Override protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    handleIntent(intent);
	}
	
	private void handleIntent(Intent intent) 	{
  	//获取到Intent的Action，注意多打Log
  	Log.d(TAG, "handleIntent: " + intent.getAction());
  	if (!intent.getAction().equals(NfcAdapter.ACTION_TECH_DISCOVERED)) {
    Log.d(TAG, "handleIntent: no valid action");
    return;
  	}
  	//获取Tag对象
  	tag = intent.getParcelableExtra(NfcAdapter.EXTRA_TAG);
  	//获取卡ID，这个ID一般没什么用，有可能是卡自动生成的
  	Log.d(TAG, "Id:" + Util.byteArraytoHexString(tag.getId()));
  	//NFC卡片所支持的技术标准
  	Log.d(TAG, "TechList:" + Arrays.toString(tag.getTechList()));
	}
	
###I/O处理
我们通过Tag获取到I/O对象，通过字节流进行处理，这里的例子按照如下的标准进行解析

	ISO-DEP (ISO 14443-4) 注意它与ISO-7816也是兼容的

目前最完善的代码如下，基于RxAndroid进行异步处理，可以处理所有的异常，注意这里的subscriber是一个RxAndroid的回掉接口，它处理成功，下一个，异常这三个回掉

	public final Observable<ResponseAPDU> getResponseAPDUObservable(final Tag tag,
    final byte... bytes) {

  	return Observable.create(new Observable.OnSubscribe<ResponseAPDU>() {
    @WorkerThread @Override public void call(Subscriber<? super ResponseAPDU> subscriber) {
      if (tag == null) {
        subscriber.onError(new NullPointerException(
            "Tag is null,try again to turn on NFC and keep your card close to your phone!"));
        return;
      }
      if (bytes == null || bytes.length == 0) {
        subscriber.onError(
            new NullPointerException("apdu is null or empty, cheak your command!"));
        return;
      }
      IsoDep iso = IsoDep.get(tag);
      byte[] result_all;
      //NfcA iso = NfcA.get(tag);
      if (iso == null) {
        subscriber.onError(new NullPointerException("Tech was not enumerated in NfcTechList"));
        return;
      }
      iso.setTimeout(5000);//ms
      try {
        if (!iso.isConnected()) {
          iso.connect();
        } else {
          iso.close();
          iso.connect();
        }
        result_all = iso.transceive(bytes);
        subscriber.onNext(ResponseAPDU.createFromPdu(result_all));
        subscriber.onCompleted();
      } catch (IOException e) {
        subscriber.onError(e);
      } finally {
        if (iso != null) {
          try {
            iso.close();
          } catch (IOException ignored) {
            subscriber.onError(ignored);
          }
        }
      }
    }
  	});
	}

在UI线程下，进行如下的调用
	
	//RxAndroid的回调
	Subscriber subscriber = new Subscriber<ResponseAPDU>() {
  	@Override public void onCompleted() {
    Log.d(TAG, "onCompleted:");
  	}

  	@Override public void onError(Throwable e) {
    	mTextView_response.setText(e.getMessage());
  	}

  	@Override public void onNext(ResponseAPDU responseAPDU) {
    Log.d(TAG, "onNext:" + responseAPDU.toString());
    mTextView_response.setText(responseAPDU.toString());
  	}
		};

	getResponseAPDUObservable(tag, data)
    .subscribeOn(Schedulers.newThread())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(subscriber);
    
    这里还有很大的优化空间，比如加入 keep-alive 机制，避免每读写一次都重新打开关闭流，当然这个需要与场景进行适配
    
###封装成高级对象(DTO)
通过以上步骤，我们能够得到一个简单的基于字节流的NFC读取器，但是更加进一步的开发（比如查询命令，SDK等）需要卡厂商提供内部公开的SDK，均有私钥的，就不能具体写了。有兴趣的可以去网上搜索PBOC2.0，网上有泄露的代码，貌似是GPL协议，各位慎重学一下。这个开发一点都不难，封装好I/O后，写改删查即可，主要难点在能够与公交公司谈拢（人家说不定看不上你，当然这个活不是程序员干的），以及谈拢后与对方开发进行联调。

##NFC卡模拟
从12年开始，随着O2O的发展，国外的品牌手机都开始加入了对SE芯片的支持，中华酷联也开始加入了对SE卡的支持。

###是否支持SE
###AndroidSE的通信架构
###开发卡模拟




[Java card Oracle](http://www.oracle.com/technetwork/java/embedded/javacard/index2-136727.html)

[cardwerk ISO 7816-4: Interindustry Commands for Interchange](http://www.cardwerk.com/smartcards/smartcard_standard_ISO7816-4_5_basic_organizations.aspx#table18)


