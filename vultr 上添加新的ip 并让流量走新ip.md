要在运行命令时指定 IP 出口，可以通过多种方法实现，具体取决于你的网络配置和脚本的实现。以下是一些常见的方法来指定 IP 出口：

### 方法 1：使用 `ip` 命令

你可以临时修改路由表，以确保特定的 IP 地址用于出站流量。首先，使用 `ip route` 命令添加一个路由，确保指定的 IP 地址作为出站接口，然后运行你的命令。
⚠️ vultr 添加IPv4 后需要 Server Restart 才能生效

例如：

```bash
# 添加一个路由，确保使用特定 IP
sudo ip route add default via <gateway-ip> dev <interface-name> src <your-ip>

# 运行你的命令
bun run src/aws/task.ts --unfinished=true --check=true --noproxy=true --checkonline=true

# 记得在命令执行后恢复路由（如果需要）
sudo ip route del default via <gateway-ip> dev <interface-name> src <your-ip>

## 真实示例
sudo ip route add default via 45.32.88.1 dev enp1s0 proto dhcp src 144.202.122.7

## 通过下面的命令列出所有的网络接口
ip link show

```

### 测试ip地址
```bash
curl https://api.myip.com
```

### 方法 2：使用 `env` 设置

如果你的脚本或命令支持环境变量，你可以在执行命令之前设置 `http_proxy` 或 `https_proxy` 来指定出口 IP。

```bash
# 例如使用 HTTP 代理
export http_proxy=http://<your-ip>:<port>
export https_proxy=http://<your-ip>:<port>

# 运行你的命令
bun run src/aws/task.ts --unfinished=true --check=true --noproxy=true --checkonline=true
```

### 方法 3：使用 `netcat` 和 `socat`

如果没有直接的方法，可以使用 `netcat` 或 `socat` 工具来通过指定的 IP 地址进行请求。例如：

```bash
echo "GET /" | socat - TCP:<target-host>:<port>,bind=<your-ip>
```

这将通过指定的 IP 地址连接到目标主机。

### 小结

如果无效，需要禁用下IPv6 
```bash
vim /etc/sysctl.conf

## 添加
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1

## 应用更改
sysctl -p
```

