# Github配置DNS

映射文件存放于（Windons系统）: `C:\Windows\System32\drivers\etc\hosts`

<img src="F:\笔记\Deeplearning\assets\1673005832507.png" style="zoom:50%">

以管理员身份运行命令行（目的是为了修改hosts文件中的内容），在命令行中依次输入以下代码。

```
cd drivers\etc
notepad hosts
```

将下列配置信息黏贴到hosts文件的最后面。

```
# GitHub related website
	140.82.112.4		github.com
	199.232.69.194 		github.global.ssl.fastly.net
	140.82.113.4 		gist.github.com
	185.199.108.154		help.github.com
	185.199.108.154		docs.github.com
	185.199.109.153		desktop.github.com
	34.201.80.84		vscode-auth.github.com
	140.82.113.21		education.github.com
	140.82.114.17		status.github.com
# GitHub related website
```

<img src="F:\笔记\Deeplearning\assets\1673006170443.png" style="zoom:40%">

完成上述步骤后，Ctrl+S保存。输入 ipconfig /flushdns，刷新DNS解析缓存。

```
ipconfig /flushdns
```

<img src="F:\笔记\Deeplearning\assets\1673006320479.png" style="zoom:50%">

输入ping github.com，丢包率较低，在浏览器中访问github网址，速度明显提升。

```
ping github.com
```

<img src="F:\笔记\Deeplearning\assets\1673006387351.png" style="zoom:50%">