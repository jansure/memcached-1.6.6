# Memcached

Memcached is a high performance multithreaded event-based key/value cache
store intended to be used in a distributed system.

See: https://memcached.org/about

A fun story explaining usage: https://memcached.org/tutorial

If you're having trouble, try the wiki: https://memcached.org/wiki

If you're trying to troubleshoot odd behavior or timeouts, see:
https://memcached.org/timeouts

https://memcached.org/ is a good resource in general. Please use the mailing
list to ask questions, github issues aren't seen by everyone!

## Dependencies

* libevent, https://www.monkey.org/~provos/libevent/ (libevent-dev)
* libseccomp, (optional, experimental, linux) - enables process restrictions for
  better security. Tested only on x86-64 architectures.
* openssl, (optional) - enables TLS support. need relatively up to date
  version.

## Environment

Be warned that the -k (mlockall) option to memcached might be
dangerous when using a large cache.  Just make sure the memcached machines
don't swap.  memcached does non-blocking network I/O, but not disk.  (it
should never go to disk, or you've lost the whole point of it)

## Build status

See https://build.memcached.org/ for multi-platform regression testing status.

## Docker Images

1、在memcached/images/路径下有两个版本的Dockerfile，一个是amd64(bitnami-docker-memcached-amd64)，另外一个是arm64(bitnami-docker-memcached-arm64)，这两个路径下都有“1”这个路径，“1”代表memcached是1.x.x的版本，以后如果升级到2.x.x可以再增加“2”路径，比如/memcached/images/bitnami-docker-memcached-arm64/2/xxx/xxxx

2、memcached的镜像打包使用到了分阶段docker build过程，a、使用builder-gcc作为编译基础镜像 https://gitlab-ce.alauda.cn/devops/builder-gcc    b、使用bitnami的官方镜像minideb作为memcached执行程序的镜像：docker.io/bitnami/minideb:buster

3、memcached编译

		1. 进去memcached/libevent-2.1.11-stable,使用的是autogen.sh生成configure文件

		2.在memcached/libevent-2.1.11-stable下执行configure，生成Makefile文件

		3.使用make名称执行Makefile，然后make install 把libevent的一些列动态库安装到memcached/libevent-2.1.11-stable/.libs下
		
		4.进入memcached路径，然后执行autogen.sh，生成configure
		
		5.使用configure --with-libevent=./libevent-2.1.11-stable/.libs/ 生成Makefile，然后执行make
		
		6.把LD_LIBRARY_PATH设置为：export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH
		
		6.执行ldd -r memcached && ldd -r memcached-debug，若动态库都找到，就表示编译成功
		
		
		
		
## Bug reports

Feel free to use the issue tracker on github.

**If you are reporting a security bug** please contact a maintainer privately.
We follow responsible disclosure: we handle reports privately, prepare a
patch, allow notifications to vendor lists. Then we push a fix release and your
bug can be posted publicly with credit in our release notes and commit
history.

## Website

* https://www.memcached.org

## Contributing

See https://github.com/memcached/memcached/wiki/DevelopmentRepos
