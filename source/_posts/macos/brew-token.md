title: 设置HOMEBREW_GITHUB_API_TOKEN
date: 2015-10-01 20:50
categories: Mac
tags: homebrew
---

在使用brew工具时，报了以下错误,github API 访问的权限限制:

```
Error:GitHub API rate limit exceeded for [xxxx]. (But here's the good news: Authenticated requests get a higher rate limit. Check out the documentation for more details.)
Try again in 5 minutes 54 seconds, or create an personal access token:
  https://github.com/settings/tokens
and then set the token as: HOMEBREW_GITHUB_API_TOKEN
```

解决：

按照报错说明

首先打开  [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new)

登陆后，输入Homebrew，生成token

设置环境变量HOMEBREW_GITHUB_API_TOKEN：

将HOMEBREW_GITHUB_API_TOKEN变量设置为获取到的token就可正常使用brew的api了:

```
	$ export HOMEBREW_GITHUB_API_TOKEN=XXXXXXXXXX
```