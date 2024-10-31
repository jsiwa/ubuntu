要使用 `iptables` 指定出站 IP，你可以通过使用 `iptables` 的 `-o`（指定输出接口）和 `-j`（跳转到特定目标）选项来控制出站流量。以下是一些基本的示例和命令。

### 1. 设置出站流量到特定 IP

如果你想要将所有出站流量定向到特定的 IP 地址，可以使用以下命令：

```bash
# 将所有出站流量定向到特定 IP（例如：192.168.1.100）
sudo iptables -t nat -A OUTPUT -p tcp -j DNAT --to-destination 192.168.1.100
```

### 2. 允许特定出站流量

如果你只想允许某些特定的流量通过某个出口 IP，比如允许 SSH 流量（端口 22）：

```bash
# 允许出站的 SSH 流量
sudo iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
```

### 3. 拒绝特定出站流量

如果你想要拒绝特定的出站流量，比如拒绝所有出站到特定 IP 的流量：

```bash
# 拒绝所有出站到某个特定 IP（例如：192.168.1.100）的流量
sudo iptables -A OUTPUT -d 192.168.1.100 -j REJECT
```

### 4. 设置默认出站策略

如果你想要设置出站的默认策略，可以使用：

```bash
# 默认拒绝所有出站流量
sudo iptables -P OUTPUT DROP

# 允许出站到特定 IP 的流量
sudo iptables -A OUTPUT -d 192.168.1.100 -j ACCEPT
```

### 5. 保存和恢复规则

保存当前的规则以便在重启后仍然有效：

```bash
# 保存规则
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

在系统启动时加载这些规则，可以使用 `iptables-restore`：

```bash
sudo iptables-restore < /etc/iptables/rules.v4
```

### 6. 列出当前规则

查看已配置的规则：

```bash
sudo iptables -L -n -v
```

### 7. 清空所有规则

如果你想要清空所有的 `iptables` 规则，可以使用：

```bash
sudo iptables -F
```

### 8. 指定出站接口

如果你想在指定的网络接口上管理出站流量，可以结合使用 `-o` 选项：

```bash
# 指定通过 enp1s0 接口出站到特定 IP
sudo iptables -A OUTPUT -o enp1s0 -d 192.168.1.100 -j ACCEPT
```

### 总结

- 使用 `iptables` 可以灵活地管理出站流量。
- 通过设置接受、拒绝和 DNAT 规则，你可以精确控制流量的方向和目标。
