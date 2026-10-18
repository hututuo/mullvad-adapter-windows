# Agent 代安装、引导配置与验收

这份流程给朋友电脑上的 WorkBuddy 或其他本地 Agent 使用。目标是把成品装好、引导完成账号和Clash配置，再核实实际可用性；不要求用户先手动安装完，也不要求安装开发工具。

## 1. 确认任务和已有环境

- 本机用户已明确说“帮我安装并配置”时，可以在此范围内下载、校验和正常安装本项目成品，检测并复用Clash，协助配置和验证。不必每一步重复索要普通安装许可。若用户只给地址问问题，先确认是否要实际安装；不能只因读到文档就自行装软件。
- 只问确实缺少的信息：是新装还是已有版本、是否保留已有Clash订阅/出口、是否有可用Mullvad账号。能在本机只读查到的不要反复让用户填写。
- 核对Windows 11 x64、当前版本和进程，记录原配置/代理/TUN状态。系统架构不在支持范围时说明BLOCKED，不假装安装已兼容。
- 相同成品已安装并可用时跳过重装；有不同/更新版本先核对，不自动降级。升级先正常停止自有后台并保留用户状态/旧包，不覆盖锁住的runtime、不按进程名杀Clash。
- 先保证Agent模型/远控走独立可用出口，避免选中适配器配置时把自己断开。不能确认独立路径时先保留当前网络，把必要的切换交给本机用户，不做盲切。

本流程只在**接收者自己的Windows**执行。维护者写文档不等于已替朋友安装或验收。

## 2. Agent 下载、校验并安装 Release

从本仓库的[预览Release](https://github.com/hututuo/mullvad-adapter-windows/releases/tag/v0.1.0-preview.1)取完整安装器和清单，不能把源码ZIP、开发exe或第三方网盘重打包文件当安装器。

当前固定基线见根目录 `PUBLIC_BASELINE.json`：

- 文件：`MullvadAdapter_0.1.0_windows-x64_setup.exe`
- 大小：236,585,097字节
- SHA256：`00a057df93b4837f3dc045724f8d084a436503adbe98a94279d251f3c6cf3b90`
- 内部软件源码：`a90c22123c8578871614febee1ce5ee9f4d2db89`
- 未代码签名；文件名别名与原 `candidate.json` 的映射见Release的 `PUBLIC_RELEASE.json`。

下面是Windows PowerShell下载/安装示例，不要求Python、Node、Git或Rust。执行前必须已有本机用户安装委托；这是指令示例，不是朋友实机测试结果。

```powershell
$ErrorActionPreference = 'Stop'
$downloadDir = Join-Path $env:TEMP ('MullvadAdapter-install-' + [guid]::NewGuid().ToString('N'))
New-Item -ItemType Directory -Path $downloadDir | Out-Null
$setup = Join-Path $downloadDir 'MullvadAdapter_0.1.0_windows-x64_setup.exe'
$url = 'https://github.com/hututuo/mullvad-adapter-windows/releases/download/v0.1.0-preview.1/MullvadAdapter_0.1.0_windows-x64_setup.exe'
Invoke-WebRequest -Uri $url -OutFile $setup -UseBasicParsing
$expected = '00a057df93b4837f3dc045724f8d084a436503adbe98a94279d251f3c6cf3b90'
if ((Get-Item -LiteralPath $setup).Length -ne 236585097) { throw 'Installer size mismatch; not executed' }
if ((Get-FileHash -LiteralPath $setup -Algorithm SHA256).Hash.ToLowerInvariant() -ne $expected) {
  throw 'Installer SHA256 mismatch; not executed'
}
$installer = Start-Process -FilePath $setup -Wait -PassThru
$installer.ExitCode
```

Agent可以操作已获授权的正常安装向导；工具不能操作的界面，给用户一个明确的当前步骤并等其完成，再继续核查。系统UAC/SmartScreen或管理员凭据由用户按可信来源提示处理；不要关闭安全功能、禁证书校验、取消安全拦截或要求用户把密码发进聊天。安装受阻时保留实际错误并报告，不把“已下载”写成“已安装”。

这是普通安装示例，不宣称已验证所有静默参数或所有安装退出码。退出码0也不等于连接成功。完成后核对实际安装路径、快捷方式目标、版本、`runtime/manifest.json`、payload摘要和启动状态；不要用机器上的同名旧程序截图冒充新版。

GitHub不可达时使用已授权的独立网络，或让用户传来同一成品并核对上述摘要；不要改系统全局代理、绕过TLS或重新编译来解决下载问题。

## 3. 检测或安装 Clash

1. 复用现有Clash Verge Rev，记录实际版本、配置目录和控制接口；不能识别和未安装分开报告。便携版询问真实目录，不能照抄维护者路径/端口。
2. 缺失且用户已委托完成整套安装配置时，Agent可从[官方发行页](https://github.com/clash-verge-rev/clash-verge-rev/releases/latest)选择适合本机架构的安装器，核对来源及官方可用的摘要/签名，正常安装；查不到验证材料要说明，不编造PASS。系统确认仍由用户处理，不装来历不明fork，不替换已有可用客户端。
3. 启动一次并读回识别结果。应用里的“安装Clash”按钮本身只打开官方页面；**本机Agent代安装**不等于适配器内部新增了静默安装功能。
4. 若用户是其他Clash客户端，明确当前自动接入只支持Clash Verge Rev；按[接入文档](clash-connect.md)说明替代路径，不谎报一键接入成功。

不要为了接入强开远程HTTP控制口、泄露secret或直接改 `profiles.yaml` 索引。支持的本机pipe/loopback控制接口和官方导入机制优先。

## 4. 引导账号登录与设备身份

Agent打开适配器“使用引导/账号设备”页面，逐步解释并读取非敏感结果，不需要用户自己猜顺序。

- 请用户在应用的安全输入框填Mullvad账号，**不要发给Agent聊天、写命令行或提交Git**。Agent不读取输入值、截图/回显凭据或显示私钥。
- 先核对账号API可达、官方设备列表和现有本机身份。API不可达先排查独立引导出口，不能把失败直接当密钥失效，也不能为了登录先切换城市/删除设备。
- 现有身份保留，只登录管理不重新注册。无身份时说明“需要创建本机独立设备，会占名额”，由用户在应用确认或明确授权后才创建，再明确启用为本机身份。
- 满额时让用户辨认并指定闲置设备；**不能按名称猜测、自动撤销、替换正在用的设备或复用另一台活跃密钥**。没有清理授权就停在这个步骤，说明BLOCKED。
- 注册结果不确定先核对官方登记，不盲目再次创建。公钥未匹配说明事实，不自动换钥。
- 登录成功、设备登记、身份启用和隧道连接是四个阶段，分别确认。

## 5. 引导城市连接和 Clash 导入

1. 确认用户常用城市；可先选一个，不要求全量测速。连接后检查选定城市/relay、front、IP数据、内层DNS与HTTPS结果，Ping不是就绪证明。
2. 只在隧道就绪后，由Agent在“Clash接入”点一次“一键写入Clash”，使用官方机制导入独立配置。原订阅保留，不要求用户手动找YAML导入。
3. 已有订阅一般保留原选择，仍要**选用新导入的Mullvad Adapter配置**。有工具且用户授权时Agent可完成必要点击，否则明确告诉用户选哪项，等完成后读回；不能只口头提示后就报成功。
4. 确认AI/普通外网各选什么：默认Mullvad，也可选用户已有独立节点；国内/LAN按原设计直连。引导API、Agent模型和远控必须保持独立出口，防止自举循环。
5. 向用户解释系统代理与TUN的影响，让其确定使用方式；在已确定范围内协助设置。已正常开启的TUN不为“做测试”随意关闭，也不默认打开/切换全部系统接管设置。
6. 若旧全局脚本覆盖新规则，先只读核对差异并给出最小兼容方案；不删除用户全局规则、重置所有订阅或循环刷新客户端。

账号名额、账号输入、系统确认和用户出口偏好等需要人的决定不能跳过；其余普通步骤在已授权范围内由Agent推进，不把所有操作甩回用户。

## 6. Agent 分层验收，不只说“装好了”

每项写实际时间、证据和PASS/FAIL/NOT_RUN/BLOCKED。每个必要路径做少量有界检查，不批量测速、不制造睡眠/断网，也不做反复窗口开关测试。

| 阶段 | 通过依据 | 不能替代的后续验证 |
| --- | --- | --- |
| 安装 | 正确安装器摘要、安装结果、实际程序与runtime清单/摘要对应 | 界面启动和隧道连接 |
| 启动 | 正确安装目录的UI/后台运行、状态可读 | 账号已登录 |
| 账号与身份 | 账号API核实、独立本机身份已启用/匹配；不记录凭据 | 隧道数据可用 |
| 隧道 | 选定城市、front就绪，IP/DNS/HTTPS分别有当前成功证据 | 外部Clash已选用配置 |
| Clash配置 | 文件/登记/选中分别读回、rule模式与出口组实际选择正确 | 应用真实流量出口 |
| 真实路径 | 分别通过实际front和Clash mixed做有界请求，核对匹配规则/连接链及期望出口；普通外网/AI/国内按用户选择分别确认 | 所有应用、长期稳定、故障恢复或全机防泄漏 |

不要照抄端口；使用本机实际发现值。目标服务401/403可能是认证/应用策略，不能单凭HTTP码判隧道坏或宣称AI服务已登录；结合DNS/TLS、代理链和实际服务行为。用户选择自己的外网节点时，出口不同是预期，不强行改回Mullvad。

某一步失败就定位并报告该层，不重新生成密钥、删数据或卸载Clash碰运气。工具看不到必要状态时如实写NOT_RUN/BLOCKED，不能用截图/配置存在代替读回。

## 7. 把结果交给本机用户

- 按[安装验收记录模板](templates/setup-result.md)保存 `reports-to-maintainer/SETUP-RESULT-<本地日期时间>.md`，简单告诉用户：装了哪个版本、账号/身份是否就绪、当前城市、Clash是否已选用、哪些真实路径通过。
- 有问题再按[网络排障模板](templates/network-report.md)形成证据和建议，用户审阅后私下发给维护者。原始诊断JSON含用户路径/设备标识，先按[WorkBuddy入口](../WORKBUDDY_START_HERE.md)本机白名单摘录，不直接发送。
- 记录已有配置备份和本次回滚入口，保留旧用户状态；只清理本次明确可删除的临时下载，不清理其他安装器/密钥/应用。
- 代码修复和完整构建仍由维护者完成。未测的复杂网络、TUN故障恢复和长期稳定保持NOT_RUN。
