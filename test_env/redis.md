# redis 环境搭建(单机)

## redis

```bash
cd /data/src
sudo wget http://download.redis.io/releases/redis-5.0.0.tar.gz
sudo tar xvzf redis-5.0.0.tar.gz
cd redis-5.0.0
sudo make
cd ..
sudo mv /data/src/redis-5.0.0 /usr/local/redis
sudo vim ~/.bash_profile
    export REDIS_PATH=/usr/local/redis/src
    export PATH=$REDIS_PATH/bin:$PATH:$HOME/.local/bin:$HOME/bin

sudo mkdir /etc/redis
sudo ln -s /usr/local/redis/redis.conf /etc/redis/redis.conf
sudo vim /etc/redis/redis.conf
    bind 0.0.0.0
    daemonize yes

# echo never > /sys/kernel/mm/transparent_hugepage/enabled
sudo vim /etc/sysctl.d/sysctl.conf
    vm.overcommit_memory=1
    net.core.somaxconn=1024

sudo sysctl -p
```

**启动文件 `/etc/systemd/system/redis.service`**

```ini
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/redis/src/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/redis/src/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
Alias=redis
```

**自启动服务**
```bash
sudo systemctl enable redis
sudo systemctl start redis
```
