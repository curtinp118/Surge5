     1|# Surge5 Config 自用配置库
     2|
     3|一份面向日常使用的 Surge 配置仓库，提供稳定版成品配置和 Beta 模板，支持多机场订阅、应用分流、基础去广告与局域网受限共享的安全默认值。
     4|## 📱 预览
     5|
     6|| 首页预览  | 首页预览  |
     7|| :---: | :---: |
     8|| <img src="./images/home1.jpeg" width="300" /> | <img src="./images/home2.jpeg" width="300" /> |
     9|
    10|
    11|## ✨ 主要特性
    12|
    13|- `surge5.conf` 为稳定版，开箱即可用，`[General]` 已与 Beta 模板保持一致，适合日常长期使用。
    14|- `SurgePro_Beta.template.conf` 为占位符模板，适合从零开始自定义或对照参考。
    15|- 预设香港、美国、日本、台湾、韩国、新加坡等地区优选策略组，支持自动测速和故障转移。
    16|- 预设 `🔰 Sub-01` 至 `🔰 Sub-04` 四个订阅入口，并通过 `HUB` 聚合统一调用。
    17|- 针对 Google、Apple、Telegram、Netflix、YouTube、AI 等服务提供独立分流。
    18|- MITM 默认只处理 Google 重定向相关域名，兼顾功能与风险控制。
    19|
    20|## 如何使用
    21|
    22|### 配置文件说明
    23|
    24|| 文件 | 定位 | 适合场景 |
    25|| :--- | :--- | :--- |
    26|| `surge5.conf` | 稳定版主配置 | 日常直接使用 |
    27|
    28|### 1. 下载配置
    29|
    30|1. 下载稳定版配置：[surge5.conf](https://raw.githubusercontent.com/curtinp118/Surge5/main/surge5.conf)。
    31|2. 打开 Surge，选择“导入” -> “从 URL 下载配置”。
    32|3. 如果你准备自己从头改配置，也可以同时参考 [SurgePro_Beta.template.conf](https://raw.githubusercontent.com/curtinp118/Surge5/main/SurgePro_Beta.template.conf)。
    33|
    34|### 2. 填入订阅信息
    35|
    36|使用文本编辑器打开 `surge5.conf`，定位到 `[Proxy Group]` 区域：
    37|
    38|```ini
    39|[Proxy Group]
    40|...
    41|🔰 Sub-01 = select, policy-path=http://example.com/api/v1/client/subscribe?token=YOUR_TOKEN&flag, ...
    42|🔰 Sub-02 = select, policy-path=https://example.com/api/v1/client/subscribe?token=YOUR_TOKEN, ...
    43|🔰 Sub-03 = select, policy-path=https://example.com/api/v1/client/subscribe?token=YOUR_TOKEN, ...
    44|🔰 Sub-04 = select, policy-path=https://example.com/share/sub/backup?token=YOUR_TOKEN, ...
    45|```
    46|
    47|- 将 `YOUR_TOKEN` 替换为你的机场订阅 Token。
    48|- 或者直接替换整条 `policy-path` 为机场提供的 Surge 订阅链接。
    49|- 不想用满四个订阅时，保留未使用项也可以，不影响其余策略组使用。
    50|
    51|
    52|### 3. 配置控制器密码
    53|
    54|定位到 `[General]` 区域，修改远程控制相关密码：
    55|
    56|```ini
    57|[General]
    58|external-controller-access = YOUR_CONTROLLER_PASSWORD@127.0.0.1:6170
    59|http-api = YOUR_HTTP_API_PASSWORD@127.0.0.1:6171
    60|```
    61|
    62|- 将 `YOUR_CONTROLLER_PASSWORD` 和 `YOUR_HTTP_API_PASSWORD` 替换为你自己的强密码。
    63|- 外部控制器与 Web 仪表盘仍默认绑定 `127.0.0.1`，不直接暴露到局域网。
    64|
    65|### 4. 局域网共享默认值
    66|
    67|```ini
    68|allow-wifi-access = true
    69|allow-hotspot-access = true
    70|proxy-restricted-to-lan = true
    71|gateway-restricted-to-lan = true
    72|http-listen = 0.0.0.0
    73|socks5-listen = 0.0.0.0
    74|```
    75|
    76|- 代理端口默认可供局域网设备访问。
    77|- `proxy-restricted-to-lan = true` 与 `gateway-restricted-to-lan = true` 会把访问范围限制在当前局域网。
    78|- `external-controller-access` 与 `http-api` 仍保持本机回环地址，避免控制面板被局域网横向访问。
    79|- 建议保留强密码，并按需配合路由器或系统防火墙进一步限制来源。
    80|
    81|### 5. 启用 MITM 功能
    82|
    83|为了实现 HTTPS 解密（用于去广告、URL 重写等功能），你需要配置 CA 证书。
    84|
    85|**方法 A：生成新证书（推荐新手）**
    86|1.  导入配置到 Surge。
    87|2.  进入 Surge 设置 -> MitM -> 配置根证书。
    88|3.  点击“生成新的 CA 证书”，并按照提示安装并信任证书。
    89|
    90|**方法 B：填入已有证书**
    91|如果你已有 P12 证书，替换 `[MITM]` 区域的占位符：
    92|
    93|```ini
    94|[MITM]
    95|ca-passphrase = YOUR_PASSPHRASE
    96|ca-p12 = YOUR_P12_BASE64_DATA
    97|```
    98|
    99|## 📂 策略组说明
   100|
   101|### 核心策略组
   102|
   103|| 策略组名称 | 类型 | 说明 |
   104|| :--- | :--- | :--- |
   105|| **Proxy** | Select | **总出口**，所有未命中特定规则的流量默认走此策略。支持选择地区优选组或特定订阅。 |
   106|| **HUB** | Select | **节点聚合中心** (隐藏)，自动聚合所有 `Sub-XX` 订阅节点，供地区优选组调用。 |
   107|| **🔰 Sub-01 ~ 04** | Select | **订阅源**，预留的 4 个订阅槽位，用于填入不同机场的订阅链接。 |
   108|| **⚙️ 手动节点** | Select | **手动筛选**，从所有订阅中筛选出的特定节点（如 CN2, IEPL 等），供手动指定使用。 |
   109|
   110|### 地区智能优选 (Smart)
   111|
   112|这些策略组会自动从所有节点中筛选对应地区的节点，并选择延迟最低的节点使用。
   113|
   114|| 策略组名称 | 筛选关键词 | 说明 |
   115|| :--- | :--- | :--- |
   116|| **🇭🇰 香港优选** | HK, Hong, 港 | 自动选择最佳香港节点 |
   117|| **🇺🇸 美国优选** | US, States, 美 | 自动选择最佳美国节点 |
   118|| **🇯🇵 日本优选** | JP, Japan, 日 | 自动选择最佳日本节点 |
   119|| **🇨🇳 台湾优选** | TW, Tai, 湾 | 自动选择最佳台湾节点 |
   120|| **🇰🇷 韩国优选** | KR, Korea, 韩 | 自动选择最佳韩国节点 |
   121|| **🇸🇬 狮城优选** | SG, Singapore, 狮 | 自动选择最佳新加坡节点 |
   122|
   123|### 应用与服务分流
   124|
   125|针对特定应用或服务的独立分流策略，确保最佳访问体验。
   126|
   127|| 策略组名称 | 默认策略 | 说明 |
   128|| :--- | :--- | :--- |
   129|| **Apple** | DIRECT | 苹果服务（App Store, iCloud 等），默认直连，可切换代理。 |
   130|| **Google** | Proxy | Google 搜索及相关服务，默认走代理总出口。 |
   131|| **Microsoft** | Proxy | 微软服务，默认走代理总出口。 |
   132|| **Telegram** | Proxy | Telegram 消息与媒体，支持指定地区节点。 |
   133|| **Twitter** | X-Fallback | Twitter/X，使用自动故障转移策略。 |
   134|| **Netflix** | Proxy | Netflix 流媒体，建议手动指定支持解锁的节点。 |
   135|| **YouTube** | Proxy | YouTube 视频，默认走代理总出口。 |
   136|| **Spotify** | Proxy | Spotify 音乐，默认走代理总出口。 |
   137|| **BiliBili** | DIRECT | 哔哩哔喱，默认直连（解决地区限制问题可切换）。 |
   138|| **PayPal** | Proxy | PayPal 支付，安全起见建议固定节点或直连。 |
   139|| **Gamer** | Proxy | 游戏平台（Steam, Epic, PS, Xbox 等）。 |
   140|| **GlobalMedia** | Proxy | 其他国际流媒体服务（Disney+, HBO 等）。 |
   141|| **AI** | AI-Fallback | 人工智能服务（ChatGPT, Gemini 等），使用自动故障转移。 |
   142|| **ADs** | REJECT | 广告拦截，默认拒绝连接。 |
   143|
   144|### 故障转移策略 (Fallback)
   145|
   146|| 策略组名称 | 说明 |
   147|| :--- | :--- |
   148|| **AI-Fallback** | 专为 AI 服务设计，自动检测并剔除不可用节点（如 Oracle 节点）。 |
   149|| **X-Fallback** | 专为 Twitter 设计，在美国和新加坡节点间自动切换。 |
   150|
   151|## ⚠️ 免责声明
   152|
   153|*   本配置文件仅供技术交流与学习使用。
   154|*   请遵守当地法律法规，切勿用于非法用途。
   155|*   配置文件中引用的规则集归原作者所有。
   156|
   157|## 🤝 致谢
   158|
   159|*   规则集来源：[Blackmatrix7](https://github.com/blackmatrix7/ios_rule_script), [VirgilClyne](https://github.com/VirgilClyne), [ACL4SSR](https://github.com/ACL4SSR/ACL4SSR) 等开源项目。
   160|*   图标来源：[Koolson](https://github.com/Koolson/Qure), [fmz200](https://github.com/fmz200/wool_scripts)。
   161|
   162|
   163|## 贡献 
   164|
   165|有建议或问题？请提出 issue，热切期待你的参与。
   166|
   167|## 许可证 📜
   168|
   169|本库使用[GNU General Public License v3.0 许可证]。在使用之前，请阅读并遵守许可条款。
   170|