# Mullvad Adapter for Windows

与 Clash Verge Rev 配合使用的本地 Mullvad 隧道适配器。本项目不是 Mullvad 或 Clash 官方客户端。

**[下载 Windows 11 x64 安装包](https://github.com/hututuo/mullvad-adapter-windows/releases/tag/v0.1.0-preview.1)** · **[WorkBuddy 接手入口](WORKBUDDY_START_HERE.md)** · **[完整技术说明](docs/windows-handoff.md)**

## 直接安装，不用编译

完整安装器约237MB，含独立后台、Mihomo和离线WebView2。不需要Rust、Node、Python、Visual Studio或源码依赖；另需Clash Verge Rev负责系统代理/TUN与分流。安装器未代码签名，是预览候选；核对Release中的candidate.json和SHA256SUMS。

1. 下载安装器并核对SHA256，正常安装。
2. 在“使用引导”检测/安装Clash，登录自己的Mullvad账号，为此电脑建立独立设备身份。不要复用另一台正在连接电脑的私钥。
3. 选常用城市并连接；就绪后“一键写入Clash”，再在Clash选用新配置，无需手动导文件。
4. AI/普通外网默认Mullvad，国内/LAN直连；外网出口也可选用户已有节点。

## 有问题：WorkBuddy排查，把文档发回

**默认不要求朋友编译或修改软件。**让WorkBuddy打开本仓库，读[接手指令](WORKBUDDY_START_HERE.md)，先在本机白名单提取诊断字段，再分析，按[模板](docs/templates/network-report.md)生成中文Markdown。原始JSON可能含用户目录和设备标识，不直接发送。朋友审阅报告后私下发给维护者，由维护者修代码、构建并给朋友新版。

读源码/日志不用开发工具。修改Rust或打包后的前端并生成新安装器才要重编；MSVC/SDK/Rust/npm的首次下载显著大于成品。普通Clash配置调整不等于重编适配器，但仍须授权并可回滚。

Agent自己应走独立可用出口，不依赖待测隧道；国产模型名字不是直连证明。禁止擅自睡眠、断网、改电源、删设备/数据、关闭安全功能或反复操作工作桌面。

## 当前范围

- 一至三个常用城市，同城恢复，先验证/重建原路径。
- WireGuard over Shadowsocks，经物理绑定UDP carrier；**SS不是禁UDP网络的TCP保底**。
- Ping是线索；IP、内层DNS和HTTPS分别验证。日志容量轮转，诊断可生成本机文件。
- 已有Windows安装/连接/Clash出口验证；朋友网络、全新系统、当前版TUN故障恢复与长期稳定尚未验收。
- Windows高级能力不能从Mac源码推定。见[能力边界](docs/network-capabilities.md)。

## 源码与成品

本仓库从审查后的快照建立独立历史，不含私人开发资料或运行状态。首次公开安装器是既有已编译0.1.0候选，内部source_sha为`a90c22123c8578871614febee1ce5ee9f4d2db89`。公开提交另加文档、许可和报告流程，没有把文档更新当重编；见[版本映射](PUBLIC_BASELINE.json)。

构建说明在[这里](packaging/windows/README.md)。`ui/`和`daemon/`为Mac参考实现，不是Windows依赖，本仓库不交付Mac新版。

## 许可与隐私

自有源码采用 **GPL-3.0-only**，见[LICENSE](LICENSE)。第三方保留自己的许可，见[THIRD_PARTY.md](THIRD_PARTY.md)。Release附Mihomo对应源码与GPL文本；Windows包不含Mac PQ helper。

不得公开账号、token/API Key、私钥、完整订阅、DPAPI密文、完整Clash配置或原始运行目录。报告先由用户审阅，私下发回，不自动上传公共Issue或Git。
