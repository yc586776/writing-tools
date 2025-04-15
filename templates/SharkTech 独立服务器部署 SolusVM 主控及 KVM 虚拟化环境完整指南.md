# SharkTech 独立服务器部署 SolusVM 主控及 KVM 虚拟化环境完整指南

SolusVM 作为 OnApp 旗下的虚拟化管理平台，虽然 2.0 版本开发停滞多年，但其 1.x 版本仍是当前主流 VPS 服务商的首选控制面板。相比 Proxmox 复杂的 WHMCS 对接流程，SolusVM 以其成熟的财务系统集成和丰富的文档支持，成为行业广泛采用的解决方案。

## 核心概念与系统要求

SolusVM 采用主控（Master）/被控（Slave）分离架构：
- **主控程序**：10美元/月（含30天试用）
- **被控程序**：2.5美元/月/终端

👉 [【点击查看】2025年最新 Sharktech 优惠码及特价云服务器方案汇总](https://bit.ly/Sharktech)

### 环境准备要点
1. **服务器配置**：
   - 主控端：普通 VPS（建议 2GB+ 内存）
   - 被控端：独立服务器（推荐 SharkTech 机型，需配备 IPMI 控制台）

2. **系统要求**：
   - CentOS 6 Minimal 版本（CentOS 7 存在兼容性问题）
   - 纯净系统环境（禁用 SELinux，无 cPanel/宝塔等面板）

3. **网络配置**：
   - 主控端需公网 IP
   - 被控端需配置多个 IP 地址段（建议 /27 或更大子网）

## 分步安装教程

### 一、基础环境配置
bash
yum -y update
yum -y install wget unzip screen lrzsz

### 二、主控端安装流程
1. 执行安装脚本：
bash
wget https://files.soluslabs.com/install.sh
sh install.sh

2. SSL 证书配置（以 Let's Encrypt 为例）：
bash
curl https://get.acme.sh | sh
acme.sh --issue -d cp.example.com -w /usr/local/solusvm/www/.verification

### 三、KVM 被控端部署
1. 选择安装类型：
bash
wget https://files.soluslabs.com/install.sh
sh install.sh  # 选择选项4安装KVM组件

2. 关键网络配置：
- 外网 Bridge 示例（ifcfg-br0）：

DEVICE=br0
TYPE=Bridge
ONBOOT=yes
IPADDR=56.56.56.34
NETMASK=255.255.255.252

### 四、虚拟机管理实战
1. **模板同步**：
   - 通过 Media Sync 功能分发系统镜像
   - 建议启用 host-passthrough CPU 模式提升性能

2. **IP 地址分配**：
   - 外网 IP 块需配置网关地址
   - 内网 IP 范围建议使用 10.0.0.0/24 段

## 常见问题解决方案
1. **网络故障排查**：
   - 使用 `brctl show` 检查网桥状态
   - 确认 `/etc/sysctl.conf` 已启用 IP 转发

2. **虚拟机启动异常**：
   - 检查 KVM 节点存储空间
   - 验证 libvirtd 服务状态

## 性能优化建议
1. 为生产环境配置专用内网 Bridge
2. 启用虚拟机内存气球驱动（ballooning）
3. 定期清理过期系统模板

> 提示：SharkTech 机房采用严格的防欺诈系统，注册时请使用真实信息并避免代理访问。

通过本教程，您已掌握 SolusVM 在独立服务器环境的核心部署技能。实际运营时还需结合业务需求进行深度定制，建议持续关注官方文档更新获取最新功能支持。