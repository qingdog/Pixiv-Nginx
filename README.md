## quick start

* 修改hosts文件

  ```shell
  echo -e "\n127.0.0.1 github.com" >> c:/Windows/System32/drivers/etc/hosts
  tail -n 1 c:/Windows/System32/drivers/etc/hosts
  ```
  
  > ```
  > 127.0.0.1 www.pixiv.net #pixiv
  > 127.0.0.1 a.pixiv.org #广告
  > 127.0.0.1 i.pximg.net #图片
  > 127.0.0.1 accounts.pixiv.net #账号
  > 127.0.0.1 en.wikipedia.org #英文维基百科桌面版
  > 127.0.0.1 zh.wikipedia.org #中文维基百科桌面版
  > 127.0.0.1 ja.wikipedia.org #日文维基百科桌面版
  > 127.0.0.1 upload.wikimedia.org #wiki图片
  > 127.0.0.1 github.com
  > ```

### start

```shell
cd .\Pixiv-Nginx\nginx-1.26.1\
start .\nginx.exe
.\nginx.exe -s reload
.\nginx.exe -s stop
tail  logs/error.log
start .\nginx.exe
wget https://github.com/mashirozx/Pixiv-Nginx --no-check-certificate
#curl --insecure --location-trusted https://github.com/qingdog/cdn/archive/refs/heads/main.zip -o main.zip
```

> ```shell--2024-07-24 10:30:46--  https://github.com/mashirozx/Pixiv-Nginx
> 正在解析主机 github.com (github.com)... 127.0.0.1
> 正在连接 github.com (github.com)|127.0.0.1|:443... 已连接。
> 警告: “github.com” 的证书不可信。
> 警告: “github.com” 的证书颁发者未知。
> 证书所有者与主机名 “github.com” 不符
> 已发出 HTTP 请求，正在等待回应... 200 OK
> 长度：未指定 [text/html]
> 正在保存至: “Pixiv-Nginx”
> 
> Pixiv-Nginx                                              [ <=>                                                                                                                 ] 344.11K  1.96MB/s  用时 0.2s
> 
> 2024-07-24 10:30:48 (1.96 MB/s) - “Pixiv-Nginx” 已保存 [352364]
> ```
>
> ```shell
> PS D:\mytest\nginx-1.26.1> ipconfig -flushdns
> 
> Windows IP 配置
> 
> 已成功刷新 DNS 解析缓存。
> PS D:\mytest\nginx-1.26.1> ping github.com
> 
> 正在 Ping github.com [127.0.0.1] 具有 32 字节的数据:
> 来自 127.0.0.1 的回复: 字节=32 时间<1ms TTL=128
> 来自 127.0.0.1 的回复: 字节=32 时间<1ms TTL=128
> 来自 127.0.0.1 的回复: 字节=32 时间<1ms TTL=128
> 来自 127.0.0.1 的回复: 字节=32 时间<1ms TTL=128
> 
> 127.0.0.1 的 Ping 统计信息:
>     数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
> 往返行程的估计时间(以毫秒为单位):
>     最短 = 0ms，最长 = 0ms，平均 = 0ms
> ```

```shell
# git 不进行ssl证书认证
git config --global http.sslVerify false
# git当前仓库不认证
git config http.sslVerify false
# git执行命令时不认证
git -c http.sslVerify=false push
```

### chrome --ignore-certificate-errors

* chrome快捷方式

```shell
"C:\Program Files\Google\Chrome\Application\chrome.exe" --ignore-certificate-errors
```

* 或者bat脚本启动chrome浏览器

```shell
@echo off
start "" "C:\Program Files\Google\Chrome\Application\chrome.exe" --ignore-certificate-errors
```

* 不修改hosts文件（从chrome映射到本地再经过本地nginx的代理）

  ```shell
  "C:\Program Files\Google\Chrome\Application\chrome.exe" --host-rules="MAP *.github.com github" --host-resolver-rules="MAP github 127.0.0.1" --ignore-certificate-errors
  ```

> * 不使用该Pixiv-Nginx项目，仅使用chrome浏览器访问GitHub
>
> ```shell
> @echo off
> start "" "C:\Program Files\Google\Chrome\Application\chrome.exe" --host-rules="MAP app-api.pixiv.net appapipixiv, MAP oauth.secure.pixiv.net TainakaRitsu, MAP i.pximg.net IzumiKonata, MAP s.pximg.net HiiragiKagami, MAP s.pximg.net HiiragiKagami, MAP pixiv.net pixivnet, MAP www.pixiv.net pixivnet, MAP e-hentai.org e-hentai, MAP forums.e-hentai.org e-hentai, MAP ehgt.org Aqua, MAP xml.ehgt.org Senko, MAP exhentai.org Eris, MAP a.pixiv.org ads, MAP *.ads-pixiv.net ads, MAP *.doubleclick.net ads, MAP *.wikipedia.org wikidata.org, MAP commons.wikimedia.org wikidata.org,  MAP discord.com discord, MAP *.github.com github" --host-resolver-rules="MAP appapipixiv 210.140.131.199, MAP TainakaRitsu 210.140.131.219, MAP IzumiKonata 210.140.92.144, MAP HiiragiKagami 210.140.92.143, MAP pixivnet 210.140.131.199, MAP e-hentai 104.20.19.168, MAP Aqua 178.162.140.212, MAP Senko 172.67.0.127, MAP Eris 178.175.129.252, MAP ads 127.0.0.1, MAP upload.wikimedia.org 208.80.154.240, MAP wikidata.org 185.15.59.224,  MAP discord 162.159.138.232, MAP github 20.205.243.166" --test-type --ignore-certificate-errors
> ::https://www.diggui.com/#type=A&hostname=github.com&nameserver=public&public=114.114.114.114
> ::https://tool.chinaz.com/dns/github.com
> ```

### fork change

```shell
cp .\nginx-1.26.1\ .\Pixiv-Nginx\
cp -r .\nginx-1.26.1\ .\Pixiv-Nginx\
cd .\Pixiv-Nginx\nginx-1.26.1\

sed -n '/upstream github-com-lb/,/\t\t}/p' nginx.conf
        upstream github-com-lb {
                server 20.205.243.166:443;
                server 20.27.177.113:443;
                server 140.82.112.4:443;
        }
        server {
                listen 443 ssl;
                server_name github.com;
                # 证书与密钥
                ssl_certificate ca/pixiv.net.crt;
                ssl_certificate_key ca/pixiv.net.key;
                # 代理根路径
                location / {
                        #proxy_pass https://20.205.243.166;
                        proxy_pass https://github-com-lb/;
                        proxy_set_header Host $host;
                        # 调整缓冲区大小 upstream sent too big header 502
                        proxy_buffer_size       32k; #nginx保存用户请求头缓冲区大小
                        proxy_buffers           4       32k; #4个缓冲区，网页平均在32k以下
                }
```

### 证书

* 自签证书傻瓜式批处理包/【限初次使用】一键生成根证书和子证书.bat

* 自签证书傻瓜式批处理包/config_childCA.txt

  * ```shell
    DNS.32 = github.com
    DNS.33 = *.github.com
    ```

* 自签证书傻瓜式批处理包/【修改config_childCA.txt后使用】重新签发子证书.bat

* chrome://settings/

  * chrome://settings/security
  * 管理证书 - 受信任的根证书颁发机构
  * 导入 - 下一步 - 浏览 - 自签证书傻瓜式批处理包/pixiv.net.crt - 是
  * pi
  
* ```shell
  cp 自签证书傻瓜式批处理包/pixiv.net.crt conf/ca/pixiv.net.crt
  cp 自签证书傻瓜式批处理包/pixiv.net.key conf/ca/pixiv.net.key
  ```
  
  ### 查看当前用户安装的证书
  
  ```powershell
  Get-ChildItem Cert:\CurrentUser\My | sls FuckGFW
  
  [Subject]
    CN=Pixiv.net, O=FuckGFW Foundation, C=CN
  [Issuer]
    CN=Pixiv.net, O=FuckGFW Foundation, C=CN
  [Serial Number]
    5BECEEB84BB054FE97AF3CA1E540E9AE0256360A
  [Not Before]
    2024/9/11 17:50:28
  [Not After]
    2034/9/9 17:50:28
  [Thumbprint]
    8AA9340D4DAB6404C9EF6ABF95F9AFDADD1B05F5
  [Subject]
    CN=PixivCA, O=FuckGFW Foundation, C=CN
  [Issuer]
    CN=Pixiv.net, O=FuckGFW Foundation, C=CN
  [Serial Number]
    02
  [Not Before]
    2024/9/11 17:53:09
  [Not After]
    2025/9/11 17:53:09
  [Thumbprint]
    7C03BA707D9629EB9DAEF06CF381C1926F81D755
  ```
  
  * 删除证书
  
  ```powershell
  #此命令从 My 证书存储中删除证书。
  Remove-Item Cert:\CurrentUser\My\8AA9340D4DAB6404C9EF6ABF95F9AFDADD1B05F5
  
  #它不影响私钥。你必须使用 -DeleteKey 参数来删除私钥和证书。
  Remove-Item Cert:\CurrentUser\My\7C03BA707D9629EB9DAEF06CF381C1926F81D755 -DeleteKey
  ```
  
  

# Pixiv-Nginx

[![GitHub release](https://img.shields.io/github/release/mashirozx/Pixiv-Nginx.svg?style=flat-square)](https://github.com/mashirozx/Pixiv-Nginx/releases/latest)
[![Github commits (since latest release)](https://img.shields.io/github/commits-since/mashirozx/Pixiv-Nginx/latest.svg?style=flat-square)](https://github.com/mashirozx/Pixiv-Nginx/commits/)

使用说明见 <https://2heng.xin/2017/09/19/pixiv/>

在本页点击下图所示的按键下载以获取最新版。

<img src="https://view.moezx.cc/images/2018/09/17/git.png" width="360" alt="下载方法" />

如遇本页下载速度过慢，可尝试通过镜像下载：

|[👉 镜像1](https://git.mashiro.top/mirrors/Pixiv-Nginx) |[👉 镜像2](https://git.moezx.cc/mirrors/Pixiv-Nginx)|
|---|---|
|<img src="https://view.moezx.cc/images/2023/03/15/ed9589b16436258a529172fbaa052b60.png" width="360" alt="镜像1下载方法" /> | <img src="https://view.moezx.cc/images/2021/10/23/c5a92987653ea0c6921b1a461401cfec.png" width="360" alt="镜像2下载方法" />|

### LICENSE

<a href="http://www.wtfpl.net/"><img src="https://ngx.moezx.cc/share/svg/brands/WTFPL_badge.svg" width="80" alt="WTFPL" /></a>

This is a fork of [nginx/nginx](https://github.com/nginx/nginx), so you should always follow its [2-clause BSD-like license](http://nginx.org/LICENSE).

The visualized management tool is part of [wnmp/wnmp](https://github.com/wnmp/wnmp), which is under [GPL-3.0](https://github.com/wnmp/wnmp/blob/master/LICENSE).
