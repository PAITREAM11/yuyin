# yuyin
语音合成代码
##百度API网址 ：https://ai.baidu.com/docs#/ASR-Online-Python-SDK/top  
以下内容是从网站中查找到的！！！
Python SDK文档
简介
目前本SDK的功能同REST API，需要联网调用http接口, 具体功能见REST API 文档, REST API 仅支持整段语音识别的模式，即需要上传完整语音文件进行识别，时长不超过60s，支持、自定义词库设置， 没有其他额外功能。

接口能力
接口名称	接口能力简要描述
语音识别	将人类的语音中的词汇内容转换为计算机可读的输入，例如按键、二进制编码或者字符序列
支持的语音格式
原始 PCM 的录音参数必须符合 16k 采样率、16bit 位深、单声道，支持的格式有：pcm（不压缩）、wav（不压缩，pcm编码）、amr（压缩格式）。

注意事项
如果需要使用实时识别、长语音、唤醒词、语义解析等其它语音功能，请使用Android或者iOS SDK 或 Linux C++ SDK 等。

请严格按照文档里描述的参数进行开发，特别请关注原始录音参数以及语音压缩格式的建议，否则会影响识别率，进而影响到产品的用户体验。

目前系统支持的语音时长上限为60s，请不要超过这个长度，否则会返回错误。

反馈
在百度云控制台内提交工单，咨询问题类型请选择人工智能服务；

QQ群快速沟通： AI开放平台官网首页底部“QQ支持群”中，查找“百度语音”。

版本更新记录
上线日期	版本号	更新内容
2017.5.11	1.0.0	语音识别服务上线
快速入门
安装语音识别 Python SDK
语音识别 Python SDK目录结构

├── README.md
├── aip                   //SDK目录
│   ├── __init__.py       //导出类
│   ├── base.py           //aip基类
│   ├── http.py           //http请求
│   └── speech.py //语音识别
└── setup.py              //setuptools安装
支持Python版本：2.7.+ ,3.+

安装使用Python SDK有如下方式：

如果已安装pip，执行pip install baidu-aip即可。
如果已安装setuptools，执行python setup.py install即可。
新建AipSpeech
AipSpeech是语音识别的Python SDK客户端，为使用语音识别的开发人员提供了一系列的交互方法。

参考如下代码新建一个AipSpeech：

from aip import AipSpeech

""" 你的 APPID AK SK """
APP_ID = '你的 App ID'
API_KEY = '你的 Api Key'
SECRET_KEY = '你的 Secret Key'

client = AipSpeech(APP_ID, API_KEY, SECRET_KEY)

在上面代码中，常量APP_ID在百度云控制台中创建，常量API_KEY与SECRET_KEY是在创建完毕应用后，系统分配给用户的，均为字符串，用于标识用户，为访问做签名验证，可在AI服务控制台中的应用列表中查看。

配置AipSpeech
如果用户需要配置AipSpeech的网络请求参数(一般不需要配置)，可以在构造AipSpeech之后调用接口设置参数，目前只支持以下参数：

接口	说明
setConnectionTimeoutInMillis	建立连接的超时时间（单位：毫秒
setSocketTimeoutInMillis	通过打开的连接传输数据的超时时间（单位：毫秒）
接口说明
语音识别
接口描述
向远程服务上传整段语音进行识别

请求说明
举例，要对段保存有一段语音的语音文件进行识别：

# 读取文件
def get_file_content(filePath):
    with open(filePath, 'rb') as fp:
        return fp.read()

# 识别本地文件
client.asr(get_file_content('audio.pcm'), 'pcm', 16000, {
    'dev_pid': 1536,
})
参数	类型	描述	是否必须
speech	Buffer	建立包含语音内容的Buffer对象, 语音文件的格式，pcm 或者 wav 或者 amr。不区分大小写	是
format	String	语音文件的格式，pcm 或者 wav 或者 amr。不区分大小写。推荐pcm文件	是
rate	int	采样率，16000，固定值	是
cuid	String	用户唯一标识，用来区分用户，填写机器 MAC 地址或 IMEI 码，长度为60以内	否
dev_pid	Int	不填写lan参数生效，都不填写，默认1537（普通话 输入法模型），dev_pid参数见本节开头的表格	否
lan(已废弃)	String	历史兼容参数，请使用dev_pid。如果dev_pid填写，该参数会被覆盖。语种选择,输入法模型，默认中文（zh）。 中文=zh、粤语=ct、英文=en，不区分大小写。	否
dev_pid 参数列表

dev_pid	语言	模型	是否有标点	备注
1536	普通话(支持简单的英文识别)	搜索模型	无标点	支持自定义词库
1537	普通话(纯中文识别)	输入法模型	有标点	支持自定义词库
1737	英语		有标点	不支持自定义词库
1637	粤语		有标点	不支持自定义词库
1837	四川话		有标点	不支持自定义词库
1936	普通话远场	远场模型	有标点	不支持
语音识别 返回数据参数详情

参数	类型	是否一定输出	描述
err_no	int	是	错误码
err_msg	int	是	错误码描述
sn	int	是	语音数据唯一标识，系统内部产生，用于 debug
result	int	是	识别结果数组，提供1-5 个候选结果，string 类型为识别的字符串， utf-8 编码
返回样例：

// 成功返回
{
    "err_no": 0,
    "err_msg": "success.",
    "corpus_no": "15984125203285346378",
    "sn": "481D633F-73BA-726F-49EF-8659ACCC2F3D",
    "result": ["北京天气"]
}

// 失败返回
{
    "err_no": 2000,
    "err_msg": "data empty.",
    "sn": null
}
错误信息
错误返回格式
若请求错误，服务器将返回的JSON文本包含以下参数：

error_code：错误码。
error_msg：错误描述信息，帮助理解和解决发生的错误。
错误码
错误码	用户输入/服务端	含义	一般解决方法
3300	用户输入错误	输入参数不正确	请仔细核对文档及参照demo，核对输入参数
3301	用户输入错误	音频质量过差	请上传清晰的音频
3302	用户输入错误	鉴权失败	token字段校验失败。请使用正确的API_KEY 和 SECRET_KEY生成。或QPS、调用量超出限额。或音频采样率不正确（可尝试更换为16k采样率）。
3303	服务端问题	语音服务器后端问题	请将api返回结果反馈至论坛或者QQ群
3304	用户请求超限	用户的请求QPS超限	请降低识别api请求频率 （qps以appId计算，移动端如果共用则累计）
3305	用户请求超限	用户的日pv（日请求量）超限	请“申请提高配额”，如果暂未通过，请降低日请求量
3307	服务端问题	语音服务器后端识别出错问题	目前请确保16000的采样率音频时长低于30s。如果仍有问题，请将api返回结果反馈至论坛或者QQ群
3308	用户输入错误	音频过长	音频时长不超过60s，请将音频时长截取为60s以下
3309	用户输入错误	音频数据问题	服务端无法将音频转为pcm格式，可能是长度问题，音频格式问题等。 请将输入的音频时长截取为60s以下，并核对下音频的编码，是否是16K， 16bits，单声道。
3310	用户输入错误	输入的音频文件过大	语音文件共有3种输入方式： json 里的speech 参数（base64后）； 直接post 二进制数据，及callback参数里url。 分别对应三种情况：json超过10M；直接post的语音文件超过10M；callback里回调url的音频文件超过10M
3311	用户输入错误	采样率rate参数不在选项里	目前rate参数仅提供16000，填写4000即会有此错误
3312	用户输入错误	音频格式format参数不在选项里	目前格式仅仅支持pcm，wav或amr，如填写mp3即会有此错误
错误码常见问题及具体分析
3300 错误
语音识别api使用的是HTTP POST方法， BODY里直接放置json， Content-Type头部为 application/json。 并非常见的浏览器表单请求（application/x-www-form-urlencoded或者multipart/x-www-form-urlencoded）。

必填字段：format rate channel cuid token cuid token cuid token cuid token，请勿漏填。此外 (speech, len) 及 (url, callback) 这两组参数必须二选一，如果都填，默认处理第一组。 channel cuid token，请勿漏填。此外 (speech, len) 及 (url, callback) 这两组参数必须二选一，如果都填，默认处理第一种。 channel cuid token，请勿漏填。此外 (speech, len) 及 (url, callback) 这两组参数必须二选一，如果都填，默认处理第一种。

必填字段如format rate channel cuid token，请勿漏填。此外 (speech, len) 及 (url, callback) 这两组参数必须二选一，如果都填，默认处理第一种，并确认 音频时长截取为60s以下。

3309错误
wav和amr的音频，服务端会自动转为pcm，这个过程中导致转码出错。请确认下format及rate参数与音频一致，并确认音频时长截取为60s以下。

3301 错误
识别结果实际为空。可能是音频质量过差，不清晰，或者是空白音频。 有时也可能是pcm填错采样率。如16K采样率的pcm文件，填写的rate参数为8000。

行业与场景限制 根据工信部《综合整治骚扰电话专项行动方案》、《关于推进综合整治骚扰电话专项行动的工作方案》，相关能力不得用于商业营销类、恶意骚扰类和违法犯罪类骚扰电话类场景，也不支持在贷款、理财、信用卡、股票、基金、债券、保险、售房租房、医疗机构、保健食品、人力资源服务、旅游等场景的骚扰电话营销行为。
