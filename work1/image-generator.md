### URL

[onrender](https://agi-image-checkout-demo-front-end.onrender.com/generate)

### Demo 流程

1. 输入提示词，选择 art style，点击 generate image
2. 预览会显示在右侧，点击 Checkout 发起支付流程
3. 测试支付：卡号输入 4242 4242 4242 4242，Expiry date 输入任意未来的 MM/YY，cvc 输入任意三位数字
4. 完成支付后跳转回 Success page，可以下载图片

### Requirements

- 完整的前端网站和后端 API
- 不包含第三方平台（如 API 调用、hosting、图片存储、数据库存储的费用）

### 【页面】

- 首页，generate 页面，登录/注册，个人账户管理，付款成功，付款失败，已购买（方便重新下载）
- 其他网站基础页面 Privacy policies, terms and conditions, about us, FAQ, contact us

### 【功能】可以根据业务需要调整）

- 用户的注册与登录，仅登录用户
- 预览图片水印（目前 demo 里没有加上）：支持后端返回预览时自动添加水印，用户付款完成后提供无水印的下载链接
- 图片存储在 AWS s3 上，支持  signed URL，可以短时间内过期，避免滥用下载链接
- generate 页面可以根据您 API 参数提供更多设置选项
- 可支持订阅模式。如用户支付月费，在每月享受一定量的生成和下载额度

### 一个云服务器，国外IP的

`43.135.142.221`:  root/dong1@3$

### runpod

1. runpod上第一步测试只是用别人的template，后面我们要部署自己的镜像，这块是后续需要着重研究的。2. 如果没有api调用，整体测试相当于没有完成。


### 关于网站

1. 支付需要和paypal，虚拟货币这些集成。 
2.注册只需要邮箱，gmail就可以。 
3.后台我们都已经开发好了。 
只需要前端


### seaart.ai

另外一个是正式的项目：https://www.seaart.ai/zhCN  客户想做一个极简版的类似平台；只包含注册，付款，发布流程应用几个功能，界面也简洁就客户。你估算一个工期；



| api | notes |
| --- | --- |
| /api/generate | api/generate/route.js |
| /api/checkout | |
| /api/download/id | |
| /api/profile | |
| /api/gallery | |
| /api/auth | ? |
| /api/ | ?? |
| | |


### Cryptocurrency
