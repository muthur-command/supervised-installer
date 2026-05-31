> [!IMPORTANT]
> 此安装方式仅适合高级用户！
>
> Supervised Debian 安装是一种「强约束型」方案：依赖较多，并会覆盖/捆绑若干系统服务的默认配置。
>
> 普通用户请优先使用官方 Muthur Command OS 镜像（OVA / 树莓派等）。

# 安装 Muthur Command Supervised

本方式在常规 Linux 发行版（当前支持 Debian 13）上提供完整的 Muthur Command 体验：除 Muthur Command OS 外，其余组件与官方栈一致。系统会运行 Muthur Command Supervisor；Supervisor 不仅是应用，而是管理整机的「设备层」，会在配置偏离预期时清理、修复或恢复默认值。

不使用 Muthur Command OS 时，用户需自行维护 Docker、网络及依赖版本。Muthur Command Supervised 按原样提供，供社区 DIY 场景使用；我们仅接受在**全新安装、已完全更新的 Debian、无额外软件包**环境下可复现的问题报告。

此方式面向熟悉 Linux、Docker 与网络运维的高级用户。

## 安装

以 root 执行（`su -` 或 `sudo su -`）：

### 步骤 1：切换到 NetworkManager 与 systemd-resolved

```bash
apt install \
network-manager \
systemd-resolved
```

若此时无法上网，需将网络完全交给 NetworkManager。默认 DHCP 场景可参考：

```bash
systemctl restart systemd-resolved.service && \
systemctl disable --now networking.service && \
mv /etc/network/interfaces /etc/network/interfaces.disabled && \
systemctl restart NetworkManager
```

> [!NOTE]
> 切换后 IP 可能变化（NetworkManager 与 ifupdown 的 DHCP 客户端行为不同）。

### 步骤 2：安装额外依赖

```bash
apt install \
curl \
udisks2
```

### 步骤 3：安装 Docker CE

```bash
curl -fsSL get.docker.com | sh
```

### 步骤 4：安装 OS Agent

见 [os-agent 文档](https://github.com/muthur-command/os-agent/tree/mc#install--upgrade)。

### 步骤 5：安装 Supervised deb 包

```bash
curl -L -o muthur-command-supervised.deb https://github.com/muthur-command/supervised-installer/releases/latest/download/muthur-command-supervised.deb
apt install ./muthur-command-supervised.deb
```

## 支持的 machine 类型

- generic-x86-64
- odroid-c2 / odroid-c4 / odroid-n2
- qemuarm-64（任意 aarch64 机器可用此项）
- qemux86-64
- raspberrypi3-64 / raspberrypi4-64 / raspberrypi5-64
- khadas-vim3

## 配置

默认数据目录 `$DATA_SHARE` 为 `/var/lib/muthurcommand`。

安装时可覆盖：

```bash
DATA_SHARE=/my/own/muthurcommand dpkg --force-confdef --force-confold -i muthur-command-supervised.deb
```

## 故障排查

使用 `journalctl -f` 查看日志。若不熟悉 Linux 排错，建议使用 Muthur Command OS 镜像。

## 来源

- **上游：** [home-assistant/supervised-installer](https://github.com/home-assistant/supervised-installer)
- **本仓库：** Muthur Command OS 维护的 fork，行为可能与上游不同。
- **许可：** Apache-2.0，见 [`LICENSE`](./LICENSE)。
