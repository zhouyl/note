# consul 测试集群搭建

## consul 安装

```bash
# 安装 consul
sudo mkdir -p /data/{src,consul}
sudo mkdir -p /data/consul/{data,conf,logs}
cd /data/src
sudo wget https://releases.hashicorp.com/consul/1.3.0/consul_1.3.0_linux_amd64.zip
sudo unzip consul_1.3.0_linux_amd64.zip
sudo mv ./consul /usr/local/bin/consul
sudo chmod +x /usr/local/bin/consul
```

**配置文件 `/data/consul/conf/consul.conf`**

```json
{
  "datacenter": "oms-java",
  "data_dir": "/data/consul/data/",
  "pid_file": "/data/consul/consul.pid",
  "log_file": "/data/consul/logs/",
  "log_rotate_bytes": 500000000,
  "log_level": "INFO",
  "node_name": "node1",
  "server": true,
  "bootstrap": false,
  "bootstrap_expect": 2,
  "retry_join": ["10.62.31.13","10.62.31.14"],
  "start_join": ["10.62.31.13","10.62.31.14"],
  "retry_interval": "30s",
  "rejoin_after_leave": true,
  "ui": true,
  "client_addr": "0.0.0.0",
  "ports": {
    "server": 8300,
    "http": 8500,
    "https": -1,
    "grpc": 8502,
    "dns": 8600
  }
}
```

    !!除 note1 机器之外， 其它机器需要关闭 ui, http/https

**启动文件 `/etc/systemd/system/consul.service`**

```ini
[Unit]
Description=consul agent
Requires=network-online.target
After=network-online.target

[Service]
Restart=on-failure
ExecStart=/usr/local/bin/consul agent -config-file=/data/consul/conf/consul.conf -config-format=json
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill -INT $MAINPID

[Install]
WantedBy=multi-user.target
Alias=consul.service
```

**自启动服务**

```bash
sudo systemctl enable consul
sudo systemctl start consul
```
