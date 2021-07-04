title: sublime text中文乱码解决
date: 2015-10-14 23:08
categories: 工作记录
tags: sublime

---

编辑器打开代码时乱码基本都是字符编码不一致所造成的。

sublime Text编辑器可以通过安装2个扩展来解决乱码问题：

【ConvertToUTF8】 和 【GBK Encoding Support】

## 安装扩展包管理工具【Package Control】

使用快捷键【control + ~】 打开控制台，参考[package control](https://packagecontrol.io/installation)，根据版本不同选择输入以下代码：

sublime Text2:

```
	import urllib2,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

sublime Text3:

```
	import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

## 安装扩展包

 扩展包控制安装好了之后就可以开始安装扩展了：

 使用【control + shift + p】 打开扩展控制窗口，输入Install Package后回车，等待扩展窗口重新打开，分别输入包名【ConvertToUTF8】 和 【GBK Encoding Support】，然后选择对应的扩展，回车就可以开始安装了,包名一次输入一个。
