# haproxy

#sudo yum -y install virt-what curl wget gcc pcre-static pcre-devel make perl pcre-devel zlib-devel openssl openssl-devel readline-devel systemd-devel


#sudo yum -y install centos-release-scl; sudo yum -y install devtoolset-10

#scl enable devtoolset-10 bash


#cd /home/$user
#wget http://www.haproxy.org/download/2.6/src/haproxy-2.6.6.tar.gz


#tar xzf haproxy-2.6.6.tar.gz

# Build HAProxy 2.4.x from sources under CentOS 7 -------------> scroll

$ sudo yum -y install virt-what curl wget gcc pcre-static pcre-devel make \
perl pcre-devel zlib-devel openssl openssl-devel readline-devel systemd-devel
# grab haproxy sources and cd into dir.
$ _haproxy_v="2.6.6"; mkdir -p ~/src; cd ~/src; wget \
http://www.haproxy.org/download/${_haproxy_v:0:3}/src/haproxy-${_haproxy_v}.tar.gz \
&& tar xzf haproxy-${_haproxy_v}.tar.gz -C ~/src && cd ~/src/haproxy-$_haproxy_v
# install newer gcc without messing the system stock gcc
$ sudo yum -y install centos-release-scl; sudo yum -y install devtoolset-10
# the following scl command will open a new bash to use gcc10
# If you want to script it, use the this "source" command instead: source scl_source enable devtoolset-10
$ scl enable devtoolset-10 bash
# compile haproxy using gcc 10 now
$ _MY_HAPROXY_CPU_NATIVE=; _VMTEST=$(sudo virt-what); \
if [[ -z $_VMTEST ]] ;then _MY_HAPROXY_CPU_NATIVE="CPU=native"; \
echo "This seems a Bare-metal server, using CPU=native"; else \
echo "This seems virtualized: $_VMTEST"; fi; \
make -j $(nproc) TARGET=linux-glibc $_MY_HAPROXY_CPU_NATIVE \
USE_PCRE=1 USE_PCRE_JIT=1 USE_OPENSSL=1 USE_TFO=1 USE_LIBCRYPT=1 USE_THREAD=1 USE_SYSTEMD=1
$ sudo make install
# here is where you could just restart haproxy and exit if this is an "update only" task
# the following is only needed if it is a first install
$ sudo ln -s /usr/local/sbin/haproxy /usr/sbin/haproxy
$ cd admin/systemd && make
$ sudo cp haproxy.service /usr/lib/systemd/system
$ sudo systemctl daemon-reload
$ sudo mkdir -p /etc/haproxy; sudo mkdir -p /run/haproxy
$ sudo mkdir -p /var/lib/haproxy; sudo touch /var/lib/haproxy/stats
$ sudo useradd -r haproxy

# manually create config, new file: /etc/haproxy/haproxy.cfg

# setup systemd service
$ sudo systemctl start haproxy
$ sudo systemctl status haproxy
# enable haproxy on boot
$ sudo systemctl enable haproxy
