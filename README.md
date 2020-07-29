# IBMYes

本项目包括3部分

1. IBM Cloud Fonudray搭建V2Ray ws
2. 利用Github的Actions 每周重启 IBM Cloud Fonudray
3. Cloudflare 高速节点中转

# 利用Github的Actions 每周重新构建 IBM Cloud Fonudray 应用

IBM Cloud 10天不操作就会关机，所以我们需要 十天内对其重启一次，避免关机。
### 可能需要重新构建才行，使用之前构建相同的参数

首先登录IBM Cloud

点击又上角的命令行

在这一步我们主要是记录4个值

 ```
IBM_ACCOUNT // IBM Cloud的登录邮箱和密码
IBM_APP_NAME // 应用的名称
REGION_NUM // 区域编码
RESOURSE_ID // 资源组ID
UUID // /etc/v2ray/config.json中的uuid
WS_PATH // /etc/v2ray/config.json中的websocket path
 ```

具体后面会一步一步完成

![image-20200615175949804](img/README/image-20200615175949804.png)

进入命令行先执行

```shell
ibmcloud login
```

输入邮箱和密码。

之后记录下区域(Region)

![image-20200615180817619](img/README/image-20200615180817619.png)

```
#1. au-syd
#2. in-che
#3. jp-tok
#4. kr-seo
#5. eu-de
#6. eu-gb
#7. us-south
#8. us-east
```

这里需要记下和区域对应的编号也就是`REGION_NUM`，比如我这里是us-south,那么我的区域编号是`7`

接下来获取资源组id`RESOURSE_ID`

```shell
ibmcloud resource groups
```

![image-20200615183425453](img/README/image-20200615183425453.png)

图中所指向便是`RESOURSE_ID`

现在返回github，到本项目

```
https://github.com/CCChieh/IBMYes
```

![image-20200615184239713](img/README/image-20200615184239713.png)

右上角fork到自己的github下，然后进入setting

![image-20200615184327329](img/README/image-20200615184327329.png)

选择Secrets

![image-20200615184426979](img/README/image-20200615184426979.png)

New secret

分别建立四个secret

```
IBM_ACCOUNT // IBM Cloud的登录邮箱和密码
IBM_APP_NAME // 应用的名称
REGION_NUM // 区域编码
RESOURSE_ID // 资源组ID
UUID // /etc/v2ray/config.json中的uuid
WS_PATH // /etc/v2ray/config.json中的websocket path
```

以`IBM_ACCOUNT`为例![image-20200615184703280](img/README/image-20200615184703280.png)

第一行为邮箱，第二行为密码。

这里需要邮箱和密码所以中间换行 ，其他的不需要换行 。

把四个secret补充完成

![image-20200615185015130](img/README/image-20200615185015130.png)

之后点击上方Actions，在这里你就会看到有个IBM Cloud Auto Restart在执行。

![image-20200615185614978](img/README/image-20200615185614978.png)

如果没有看见Action的话到自己仓库的`/.github/workflows/ibm.yml`

![image-20200615235426917](img/README/image-20200615235426917.png)

编辑下，随意增添个空行然后commit下

![image-20200615235540567](img/README/image-20200615235540567.png)

就可以看见有action了

第一次可能因为secret没添加导致workflow执行失败，只需要点下

![image-20200615191100959](img/README/image-20200615191100959.png)

进去后按照下图

![image-20200615191035212](img/README/image-20200615191035212.png)

找到 `Re-run jobs`重新执行一次即可，至此自动重启已经ok了。

> 感谢药油@[My Flavor](https://yaohuo.me/bbs/userinfo.aspx?touserid=24109)，原本打算弄bash在自己服务器定期执行脚本，现在看了他的帖子，发现用Actions是一个更好的选择。



在客户端把地址换成ip，伪装域名换成我们cloudflare的workers的域名即可

![image-20200615215820188](img/README/image-20200615215820188.png)

如果不方便用电脑优选ip也可以把地址设为`cloudflare.com`或`icook.tw`,这两个一个cloudflare官网，自然也是使用自家cdn，另外一家是台湾省的一个网站，域名指向的ip一般也是比较好的线路。

![image-20200615220201165](img/README/image-20200615220201165.png)

这里稍微提下原理吧，主要涉及CDN和请求头部，CDN识别流量是访问哪个网站的是根据请求头的Host来识别，所以这里要么host用我们的域名 ，要么我们伪装成我们的域名，这样都可以达到回源我们网站的请求。如果自己有域名也可以换自己的域名，域名也可以从第三方接入商cname，有兴趣的同学可以自己研究下。
