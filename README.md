# Install on Centos 6,7:

## Step 1:
```
# yum -y install wget gcc gcc-c++ flex bison make bind bind-libs bind-utils openssl openssl-devel perl quota libaio libcom_err-devel libcurl-devel tar diffutils nano dbus.x86_64 db4-devel cyrus-sasl-devel perl-ExtUtils-Embed.x86_64 cpan
```

Check OpenSSL version:
```
# openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
```

if version is below 1.1 (if is 1.1 or higher jump to *Step 2*):
```
# sudo yum install libtool perl-core zlib-devel -y
# wget https://github.com/openssl/openssl/archive/OpenSSL_1_1_0g.tar.gz
# tar -zxvf OpenSSL_1_1_0g.tar.gz
# cd openssl-OpenSSL_1_1_0g
# ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared zlib
# make
# make test
# sudo make install
```

then:
```
# nano /etc/profile.d/openssl.sh
```
insert this code and save(ctrl+x):
```
# /etc/profile.d/openssl.sh
pathmunge /usr/local/openssl/bin
```
then:
```
# nano /etc/ld.so.conf.d/openssl-1.1.0g.conf
```
insert this code and save(ctrl+x):
```
# /etc/ld.so/conf.d/openssl-1.1.0g.conf
/usr/local/openssl/lib
```

then:
```
# sudo ldconfig -v
# openssl version
OpenSSL 1.1.0h  27 Mar 2018
```

then:
```
# nano Makefile
```
replace these lines in **Makefile** file:
```
CFLAGS: -I/usr/local/openssl/include
LDFLAGS: -L /usr/local/openssl/lib
```
to:
```
CFLAGS = -I/usr/local/openssl/include -m64 -O3 -std=gnu11 -Wall -mpclmul -march=core2 -mfpmath=sse -mssse3 -fno-strict-aliasing -fno-strict-overflow -fwrapv -DAES=1 -DCOMMIT=\"${COMMIT}\" -D_GNU_SOURCE=1 -D_FILE_OFFSET_BITS=64
LDFLAGS = -L /usr/local/openssl/lib -m64 -ggdb -rdynamic -lm -lrt -lcrypto -lz -lpthread -lcrypto
```
then:
```
# make clean
# make
```

## Step 2:
```
# yum install git
# cd ~
# git clone https://github.com/wecanco/MTProxy.git
# cd MTProxy
# make
```

Your binary will be *objs/bin/mtproto-proxy*

then:
```
# curl -s https://core.telegram.org/getProxySecret -o proxy-secret
# curl -s https://core.telegram.org/getProxyConfig -o proxy-multi.conf
```

now Generate a secret to be used by users to connect to your proxy:
```
# head -c 16 /dev/urandom | xxd -ps
```


## Run mtproto-proxy
```
# screen
# cd ~
# cd MTProxy
# objs/bin/mtproto-proxy -u nobody -p 9990 -H 444 -S <secret> --aes-pwd proxy-secret proxy-multi.conf -M 1
```

where:
- nobody is the user name. mtproto-proxy calls setuid() to drop privilegies
- 444 is the port, used by clients to connect to the proxy
- 9990 is the local port. You can use it to get statistics from mtproto. Like wget localhost:8888/stats
 You can only get this stat via loopback
- <secret> is the secret generated by *head -c 16 /dev/urandom | xxd -ps*
- 1 is the number of workers. You can increase the number of workers, if you have a powerful server
- also feel free to check out other options using ```objs/bin/mtproto-proxy -help```


## Register your proxy and set promotion channel:  
1. go to [@MTProxybot](https://t.me/MTProxybot) bot and start it.
2. send */newproxy* commond.
3. send your server ip (or domain/subdomain)  and proxy port like ==> *host:port*
4. now send secret key generated by *head -c 16 /dev/urandom | xxd -ps*
5. now send */myproxies* and set promoted channel


[@WeCanGP](https://t.me/WeCanGP)
