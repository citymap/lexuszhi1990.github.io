http://my.oschina.net/u/943306/blog/345923

客户机一般都会设置zh_CN.UTF-8语言，用来显示中文，而远端的vps一般就只有en_US.UTF-8
zh_CN.UTF-8一旦带过去就会报找不到的错误，文章开头已经说的很清楚了

网上还有些解决方法，并不是很靠谱，虽然从表面来看像解决问题了，但其实是把问题影藏了

比如在远程主机上的/etc/ssh/sshd_config文件里，将AcceptEnv LANG LC_*这行注释掉
然后重启远程的sshd，然后退出远程后，重新ssh上来。
这时，远程主机不会把客户机的语言环境（zh_CN.UTF-8）带过来
当然就不会再有报错，可惜的是，远程主机是无法正确显示中文的，问题还在，只是被影藏了。