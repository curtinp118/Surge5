# Surge5 Config 自用配置库 🚀

一份经过精心优化、易于定制的 Surge 配置文件， 支持多机场✈️订阅

## ✨ 主要特性

*   **⚡️ 极致分流**：
    *   **多地区策略**：预设香港、美国、日本、台湾、韩国、新加坡等地区优选策略组 (`smart` 模式)。
    *   **应用级优化**：针对 Netflix, YouTube, Spotify, Disney+, Telegram, Google, Apple 等常用服务独立分流。
    *   **自动测速与故障转移**：核心策略组采用自动测速，确保始终连接最快节点。

*   **📦 多订阅聚合**：
    *   预设 `🔰 Sub-01` 至 `🔰 Sub-04` 四个标准订阅。
    *   通过 `HUB` 策略组自动聚合所有订阅节点，无需手动管理。

*   **🛡 隐私与去广告**：
    *   集成主流去广告规则集 (Adblock4limbo 等)。
    *   内置隐私保护规则，屏蔽跟踪器与恶意网站。
    *   DNS 防泄漏与 DoH (DNS over HTTPS) 支持。  

- **安全策略适度**  
  仅对 Google 域名启用 HTTPS 解密，避免不必要的风险和性能开销。  



## 如何使用 🛠️

### 1. 下载配置

1. 配置文件[长按复制](https://raw.githubusercontent.com/curtinp118/Surge5/refs/heads/main/surge5.conf)，打开Surge5，点击-导入-从URL下载配置。

### 2. 填入订阅信息
使用文本编辑器打开配置文件，定位到 `[Proxy Group]` 区域：

```ini
[Proxy Group]
...
🔰 Sub-01 = select, policy-path=http://example.com/api/v1/client/subscribe?token=YOUR_TOKEN&flag, ...
🔰 Sub-02 = select, policy-path=https://example.com/api/v1/client/subscribe?token=YOUR_TOKEN, ...
```

*   将 `YOUR_TOKEN` 替换为你的机场订阅 Token。
*   或者直接替换整段 `policy-path` 链接。


### 3. 配置安全认证 (可选但推荐)
定位到 `[General]` 区域，修改远程控制密码：

```ini
[General]
external-controller-access = YOUR_PASSWORD@127.0.0.1:6170
http-api = YOUR_PASSWORD@0.0.0.0:6171
```

将 `YOUR_PASSWORD` 替换为你自己的强密码。


### 4. 启用 MITM 功能
为了实现 HTTPS 解密（用于去广告、URL 重写等高级功能），你需要配置 CA 证书。

**方法 A：生成新证书（推荐新手）**
1.  导入配置到 Surge。
2.  进入 Surge 设置 -> MitM -> 配置根证书。
3.  点击“生成新的 CA 证书”，并按照提示安装并信任证书。

**方法 B：填入已有证书**
如果你已有 P12 证书，替换 `[MITM]` 区域的占位符：

```ini
[MITM]
ca-passphrase = YOUR_PASSPHRASE
ca-p12 = YOUR_P12_BASE64_DATA
```

## 📂 策略组说明

### 核心策略组

| 策略组名称 | 类型 | 说明 |
| :--- | :--- | :--- |
| **Proxy** | Select | **总出口**，所有未命中特定规则的流量默认走此策略。支持选择地区优选组或特定订阅。 |
| **HUB** | Select | **节点聚合中心** (隐藏)，自动聚合所有 `Sub-XX` 订阅节点，供地区优选组调用。 |
| **🔰 Sub-01 ~ 04** | Select | **订阅源**，预留的 4 个订阅槽位，用于填入不同机场的订阅链接。 |
| **⚙️ 手动节点** | Select | **手动筛选**，从所有订阅中筛选出的特定节点（如 CN2, IEPL 等），供手动指定使用。 |

### 地区智能优选 (Smart)

这些策略组会自动从所有节点中筛选对应地区的节点，并选择延迟最低的节点使用。

| 策略组名称 | 筛选关键词 | 说明 |
| :--- | :--- | :--- |
| **🇭🇰 香港优选** | HK, Hong, 港 | 自动选择最佳香港节点 |
| **🇺🇸 美国优选** | US, States, 美 | 自动选择最佳美国节点 |
| **🇯🇵 日本优选** | JP, Japan, 日 | 自动选择最佳日本节点 |
| **🇨🇳 台湾优选** | TW, Tai, 湾 | 自动选择最佳台湾节点 |
| **🇰🇷 韩国优选** | KR, Korea, 韩 | 自动选择最佳韩国节点 |
| **🇸🇬 狮城优选** | SG, Singapore, 狮 | 自动选择最佳新加坡节点 |

### 应用与服务分流

针对特定应用或服务的独立分流策略，确保最佳访问体验。

| 策略组名称 | 默认策略 | 说明 |
| :--- | :--- | :--- |
| **Apple** | DIRECT | 苹果服务（App Store, iCloud 等），默认直连，可切换代理。 |
| **Google** | Proxy | Google 搜索及相关服务，默认走代理总出口。 |
| **Microsoft** | Proxy | 微软服务，默认走代理总出口。 |
| **Telegram** | Proxy | Telegram 消息与媒体，支持指定地区节点。 |
| **Twitter** | X-Fallback | Twitter/X，使用自动故障转移策略。 |
| **Netflix** | Proxy | Netflix 流媒体，建议手动指定支持解锁的节点。 |
| **YouTube** | Proxy | YouTube 视频，默认走代理总出口。 |
| **Spotify** | Proxy | Spotify 音乐，默认走代理总出口。 |
| **BiliBili** | DIRECT | 哔哩哔喱，默认直连（解决地区限制问题可切换）。 |
| **PayPal** | Proxy | PayPal 支付，安全起见建议固定节点或直连。 |
| **Gamer** | Proxy | 游戏平台（Steam, Epic, PS, Xbox 等）。 |
| **GlobalMedia** | Proxy | 其他国际流媒体服务（Disney+, HBO 等）。 |
| **AI** | AI-Fallback | 人工智能服务（ChatGPT, Gemini 等），使用自动故障转移。 |
| **ADs** | REJECT | 广告拦截，默认拒绝连接。 |

### 故障转移策略 (Fallback)

| 策略组名称 | 说明 |
| :--- | :--- |
| **AI-Fallback** | 专为 AI 服务设计，自动检测并剔除不可用节点（如 Oracle 节点）。 |
| **X-Fallback** | 专为 Twitter 设计，在美国和新加坡节点间自动切换。 |

## ⚠️ 免责声明

*   本配置文件仅供技术交流与学习使用。
*   请遵守当地法律法规，切勿用于非法用途。
*   配置文件中引用的规则集归原作者所有。

## 🤝 致谢

*   规则集来源：[Blackmatrix7](https://github.com/blackmatrix7/ios_rule_script), [VirgilClyne](https://github.com/VirgilClyne), [ACL4SSR](https://github.com/ACL4SSR/ACL4SSR) 等开源项目。
*   图标来源：[Koolson](https://github.com/Koolson/Qure), [fmz200](https://github.com/fmz200/wool_scripts)。


## 贡献 

有建议或问题？请提出 issue，热切期待你的参与。

## 许可证 📜

本库使用[GNU General Public License v3.0 许可证]。在使用之前，请阅读并遵守许可条款。
