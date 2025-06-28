# Blockcast 网络

Blockcast 是一个去中心化物理基础设施网络（DePIN），旨在通过内容分发网络（CDN）利用闲置的网络带宽赚钱，底层由 Solana 提供支持。
![image](https://github.com/user-attachments/assets/f44a7020-447a-4255-bb86-75232bd636aa)


---

### 奖励详情：

一个为期 6 个月的资源证明（Proof of Resources）周期，根据节点能力进行奖励：

- 表现最优的节点将在周期结束时获得特殊的 NFT 奖励。

**奖励依据如下：**

- 网络连接质量（上行带宽）
- 节点在线率（可靠性）
- 硬件评分（内存、硬盘、CPU）
- 所在地理位置（IP 所属区域）
- **加分项：支持多播（Multicast）的 IP 可获得额外积分！**
- 即便是低配置硬件也可以参与运行（亲测1C1G vps就能运行）
- 跑节点需要独立IP，所以需要购买vps [11美金每年VPS](https://my.racknerd.com/aff.php?aff=14724&pid=912)
- 每个账号可以跑多个节点

---

## 环境要求

以下安装命令在ubuntu22.04 和 ubuntu24.04 上都可以运行

## 安装依赖项

更新系统包：

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

安装必要依赖：

```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

## 安装 Docker：
### 方式一，安装 [1panel](https://1panel.cn/) 会自动安装docker，还可以方便的管理服务器
```bash
curl -sSL https://resource.1panel.pro/quick_start.sh -o quick_start.sh && bash quick_start.sh
```
### 方式二，命令行安装Docker
```bash
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  \"$(. /etc/os-release && echo \"$VERSION_CODENAME\")\" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

测试 Docker 安装是否成功：

```bash
sudo docker run hello-world
sudo systemctl enable docker
sudo systemctl restart docker
```

---

## 注册 Dashboard 控制台

前往 [Blockcast 控制台](https://app.blockcast.network?referral-code=lwmQvP) 注册账户。

---

## 安装 Blockcast

克隆仓库：

```bash
git clone https://github.com/sam-mika333/blockcast
cd blockcast
```

---

## 启动节点服务

```bash
docker compose up -d
```

> ⚠️ 注意：请确保 8080 端口未被占用。如有冲突，请编辑 `docker-compose.yml` 文件，将 `8080:8080` 替换为 `8081:8080` 以修改端口。

---

## 查看日志状态

列出容器：

```bash
docker compose ps -a
```

示例输出：

```
NAME                                 IMAGE                             COMMAND                  SERVICE          
beacon-docker-compose-watchtower-1   containrrr/watchtower             "/watchtower"            watchtower
beacond                              blockcast/cdn_gateway_go:stable   "/usr/bin/beacond -l…"   beacond
blockcastd                           blockcast/cdn_gateway_go:stable   "/usr/bin/blockcastd…"   blockcastd
control_proxy                        blockcast/cdn_gateway_go:stable   "/usr/bin/control_pr…"   control_proxy
```

查看日志：

```bash
docker compose logs -fn 1000
```

如果所有容器都运行正常，可跳过日志查看步骤。

---

## 注册节点

获取地理位置：

```bash
curl -s https://ipinfo.io | jq '.city, .region, .country, .loc'
```

初始化并注册节点：

```bash
docker compose exec blockcastd blockcastd init
```

- 执行后会在终端中输出一个 `Registration URL`，复制后用浏览器打开。
- 页面中会自动填写 Hardware ID 和 Challenge Key，手动填写上一步获取的位置信息。
- 提交注册。

> 注册完成后，等待几分钟，节点状态会变为 “Online” ✅
