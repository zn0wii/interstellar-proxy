<div align="center">

<img src="assets/android/ic_launcher-web.png" width="110" alt="星际穿越" />

# 星际穿越 · interstellar

**星河漫漫， 穿越光年。**

Android 7.0+ · v0.5.2 · Kotlin + Jetpack Compose

</div>

Android 上的**三内核**代理客户端（sing-box / mihomo / Xray 一键切换）。UI 采用「航空航天玻璃 + 任务控制台」设计语言（参考 satelite-proxy）：深空底色、玻璃拟态卡片、环境光晕、马卡龙主题色、状态驱动 Hero、遥测仪表网格与底部玻璃 dock。

<div align="center">

<img src="assets/app.jpg" width="300" alt="应用截图" />

</div>

## 功能

### 内核（三选一，运行中可切换）

- **sing-box 1.14**：进程内 `libbox.aar`（gomobile，四 ABI），`CommandClient` 本地套接字热切换/测速/日志，无需重启
- **mihomo (Clash Meta)**：sidecar 子进程（自编译 `cmfa` 标签规避 packages.xml 权限问题），Clash REST API 热切换、组测速、活跃连接管理、流量统计
- **Xray v26**：官方安卓二进制 sidecar（arm64/x86_64），支持 ss/vmess/vless/trojan/socks/http/wireguard/**hysteria2**；切节点 = 重生成配置重启生效
- **TUN 桥接**：sidecar 内核统一走 hev-socks5-tunnel（JNI 进程内库）把 VPN TUN 桥到本地 socks 口，规避子进程 fd 继承限制（v2rayNG 同款方案）
- **防环**：节点服务器 IP + 国内 DNS 自动排除出 VPN 路由；GEOSITE/GEOIP 数据库随 APK 打包（mihomo 用 metadb，Xray 用 v2fly dat），冷启动无需联网下载
- **DNS**：sing-box typed DNS；mihomo fake-ip + sniffer + proxy-server-nameserver；Xray 劫持 53 → 内置 DNS（国外 DoH 走代理 / 国内直连），不依赖节点 UDP 转发

### 订阅

- **多格式导入**：Clash YAML / 分享链接（ss·vmess·vless·trojan·hysteria2·tuic·anytls…）/ Base64 自动嗅探
- **导入体验**：URL（伪装 clash-verge UA，解析 `subscription-userinfo`）/ 文本导入，进行中显示进度条且可取消，失败原因在对话框内直接展示并保留输入
- **流量显示**：剩余流量 / 到期信息
- **自动更新**：WorkManager 定时拉取（1/6/12/24h），运行中按内核热重载
- **Mix 多订阅合池**：手动勾选要合并的订阅，节点合并为单一池并标注来源

### 节点

- **切换模式**：手动 / 自动（urltest）/ **智能** 三种
- **智能模式**：每 30s 实测当前出口（HTTP 204 经隧道）；延迟 >300ms 触发重选——TCP Ping 全池（<200ms 入备选）→ 实测筛选（API 内核用组测速快照，Xray 逐个切换验证）→ 择优热切换；延迟缓存持久化排序驱动下轮探测顺序；备选过少 / 无达标节点 / 筛选超时告警；首页内核区上方常驻状态行，节点页智能卡实时显示当前节点与延迟
- **URL Test 测速**：进度条 + 完成统计（节点数 / 成功 / 失败原因归类 / 最低 / P50 / P95 / 最高）；等待以内核单节点超时为基准（15s + 余量），大节点池不漏测；失败节点显示「超时」而非「未测」
- **TCP Ping**：不依赖内核直连测握手延迟，失败原因归类统计；UDP 协议（hy2/tuic/wireguard）自动跳过并提示用测速
- **协议摘要**：节点卡显示协议信息，长按详情展开完整参数与凭据（不脱敏）
- **布局与排序**：网格 / 列表双布局、延迟 / 名称排序
- **分组**：地区分组吸顶折叠；可选按国家 urltest 分组

### 路由

- **路由模式**：规则 / 全局 / 直连，首页状态胶囊只读展示、设置页切换
- **路由规则**（简易）：域名 → 直连 / 走代理 / 指定节点，Dialog 编辑，三内核语义一致
- **内置大陆分流**：geosite/geoip 随 APK 打包，大陆直连无需在线下载
- **去广告** / **局域网直连**：一键开关
- **DNS 解析覆写**：域名 → 固定 IP（hosts 语义）

### 分应用代理

> **推荐用法**：手机上建议优先使用按应用分流（白名单模式：勾选需要代理的应用，其余全部直连）。原因有二：
> 1. **对系统侵入性最小**——国内应用完全不经过 VPN，网络体验与不开代理时一致，不受内核分流影响；
> 2. **规则分流无法完整覆盖所有场景**——geosite/geoip 再全也有漏判（国内流量误走代理、国外服务被漏判直连），按应用维度切流从源头规避了误判问题。

- 白名单 / 黑名单两种模式，图标 + 搜索 + 批量选择 + 常用应用预置（关键字可扩展）
- 修改即时生效：sing-box 热重载 OverrideOptions；mihomo/Xray 检测到名单变化自动重建 TUN 应用

### 界面与交互

- **底部玻璃 dock 导航**：首页 / 节点 / 订阅 / 日志 / 设置，HorizontalPager 跟手拖动
- **玻璃控制台设计语言**：深空底色、半透明玻璃卡、accent 环境光晕、磨砂分段控件、深浅色跟随
- **马卡龙主题色**：六色预设，一个 accent 换肤整套 UI
- **状态驱动 Hero**：Face ID 笑脸（眨眼/呼吸/状态表情）或经典轨道双样式
- **任务控制台首页**：状态胶囊（路由模式/应用分流/切换模式，点击直达对应设置）+ 内核切换段控（sing-box/mihomo/Xray 运行中热切换）+ 遥测仪表网格（核心版本、实时流量、出口网络探测、订阅额度）+ 底部启动/切换节点按钮
- **网络探测**：多公共 IP API 竞速，经当前内核出口，显示出口 IP/地区/延迟
- **连接监控**（sing-box/mihomo）：活跃连接实时列表，单条/全部断开
- **日志页**：内核 stdout + 应用事件日志实时流，UTF-8 中文无损
- **通知**：常驻通知栏实时显示连接速率

## 项目结构

```
app/src/main/java/com/interstellar/proxy/
├── MainActivity.kt         # dock 导航 + HorizontalPager + 子页面栈
├── InterstellarApplication.kt
├── bg/                     # 服务层（移植自 sing-box-for-android 最小集）
│   ├── BoxService.kt       # 服务编排（CoreEngines 工厂 + pendingRestart 平滑重启）
│   ├── VPNService.kt       # VpnService.Builder / sidecar TUN（排除路由 + 分应用）
│   ├── ProxyService.kt / QuickTileService.kt / ServiceNotification.kt …
├── core/                   # 三内核抽象
│   ├── ProxyCore.kt        # 引擎接口 + CoreEngines 工厂 + CoreHost 回调
│   ├── SingBoxCore.kt      # 进程内 libbox
│   ├── MihomoCore.kt       # sidecar + Clash REST API + hev TUN 桥
│   ├── XrayCore.kt         # sidecar + 就绪探测 + hev TUN 桥
│   ├── SidecarProcess.kt   # 子进程生命周期/日志轮转/看门狗
│   ├── ClashApiClient.kt / TProxyService.kt（hev JNI）/ AppLog.kt
├── data/
│   ├── model/ subscription/          # 节点模型 / 多格式解析
│   ├── config/ConfigBuilder.kt       # sing-box JSON 生成
│   ├── config/MihomoConfigBuilder.kt # Clash YAML 生成（语义对齐）
│   ├── config/XrayConfigBuilder.kt   # Xray JSON 生成（含 hy2）
│   ├── net/（SubscriptionFetcher / NetProbe）/ Settings.kt / …
├── ui/
│   ├── AppViewModel.kt     # 状态流 + 三内核分支 + SmartSwitchEngine 接线
│   ├── SmartSwitchEngine.kt# 智能模式引擎（巡检/筛选/缓存/告警）
│   ├── ConnectionsViewModel.kt / LogsViewModel.kt / PerAppProxyViewModel.kt
│   ├── components/ pages/  # 玻璃组件 / 各页面
└── utils/                  # CommandClient / CommandTarget
```

## 构建

1. **内核二进制**（`python tools/fetch_cores.py`）：
   - **libbox.aar**：sing-box 源码 gomobile 构建（本地已含于 `app/libs/`，CI 按 tag 缓存重建）
   - **libmihomo.so**：mihomo 源码自编译（`with_gvisor,cmfa` 标签，四 ABI；arm64 纯 Go，其余走 NDK clang）
   - **libhev-socks5-tunnel.so**：hev-socks5-tunnel ndk-build（四 ABI，进程内 JNI 库）
   - **libxray.so**：Xray 官方安卓 release 下载（arm64/x86_64，SHA256 校验 + 镜像回退），geodata 随包解包

2. **APK**：
   ```bash
   ./gradlew assembleDebug    # 地心游记（.debug 后缀包名，与正式版共存）
   ./gradlew assembleRelease  # 星际穿越（按 ABI 分包）
   ```
   - 签名：根目录 `signing.properties`（gitignored），缺失时 release 回退 debug 签名
   - 网络受限环境可用腾讯 gradle 镜像（已在 wrapper 配置）

3. **CI**（`.github/workflows/release-apk.yml`）：推送 `v*` tag 自动编译全部 APK 并发布 Release
   - 版本统一在根目录 `version.properties`；发版改该文件后打 `v<versionName>` tag，CI 校验一致
   - sidecar 内核按版本 pin 缓存（mihomo/hev 源码编译，Xray 官方下载）
   - 正式签名需 repo secrets（`INTERSTELLAR_KEYSTORE_B64` 等），未配置回退 debug 签名

## 技术要点

- 三内核统一抽象（`ProxyCore` / `CoreHost`），UI 与内核解耦；sidecar 经 `nativeLibraryDir` exec（W^X 合规路径）
- sidecar TUN 桥：Android 子进程继承 fd 受限，hev-socks5-tunnel 以 JNI 进程内库读 TUN 转 socks5（v2rayNG 方案）
- 运行中切内核：停止 → 重生成 → 自动重启（`pendingRestart` 吸收停止竞态）
- 已知取舍：sidecar 内核（mihomo/Xray）的 DIRECT 出站无法跨进程 `protect()`，「绕过大陆」下国内站点经代理出国（sing-box 不受影响）；Xray v26 移除 `allowInsecure`，自签证书节点报 TLS 错
- 技术栈：Kotlin + Jetpack Compose（Material3）、kotlinx-serialization、kaml、OkHttp、WorkManager

## Roadmap

- [ ] 内置规则集定期更新（随版本发布更新）
- [ ] 智能模式参数可配置（阈值 / 巡检间隔）
- [x] 三内核切换（sing-box / mihomo / Xray）
- [x] 智能切换（巡检 → Ping 筛选 → 实测择优 → 缓存排序）
- [x] 订阅自动更新（WorkManager）
- [x] 路由规则自定义（域名 → 直连 / 代理 / 指定节点）
- [x] DNS 手动解析覆写（域名 → 固定 IP）
- [x] 玻璃控制台 UI 重设计

## 致谢

- [sing-box](https://github.com/SagerNet/sing-box) — 内核与服务层参考
- [sing-box-for-android (SFA)](https://github.com/SagerNet/sing-box-for-android) — 服务层移植参考
- [mihomo](https://github.com/MetaCubeX/mihomo) / [ClashMetaForAndroid](https://github.com/MetaCubeX/ClashMetaForAndroid) — mihomo 内核与 cmfa 构建参考
- [Xray-core](https://github.com/XTLS/Xray-core) / [v2rayNG](https://github.com/2dust/v2rayNG) — Xray 内核与 hev 桥接方案参考
- [hev-socks5-tunnel](https://github.com/heiher/hev-socks5-tunnel) — TUN→socks5 桥
