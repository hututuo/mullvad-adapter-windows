# 给朋友的 WorkBuddy：代安装、引导配置、验收与报告

用户委托安装配置时，Agent负责下载/校验Release、普通安装、引导登录和Clash接入，并检查实际结果；不用让用户先自己装好。用户只要求排障时仍先只读。两种模式都不要求安装Rust/Node/MSVC等开发工具，也不默认现场改代码。

**安装配置先读：[Agent代安装与验收流程](docs/agent-installation.md)。**账号凭据、系统提示、设备创建/撤销等需要人的决定由Agent明确引导，不能跳过。

## 用户怎么做

1. 把仓库地址交给WorkBuddy，说“请帮我安装Release、引导完成账号和Clash配置，并检查是否成功”。有本机安装工具的Agent可代为执行，不要求先人工安装；缺工具时只请用户完成当前必要步骤，随后继续核查。
2. 如需本地资料，Agent下载仓库源码ZIP并解压，或clone；读源码和日志不需编译工具。
3. 确认WorkBuddy模型请求走独立可用网络，不经待测适配器；国产模型名字不是直连证明。
4. Agent需要账号输入、系统提示或设备操作确认时，会说明当前步骤；用户在应用本机填写/确认，不把凭据发到聊天。其余已授权的普通步骤让Agent继续推进。
5. 完成后由Agent给出[安装验收记录](docs/templates/setup-result.md)。失败时再生成网络排障报告，用户检查敏感内容后把**文件**私下发回；只发路径另一台电脑读不到。原始JSON先按下面白名单提取，不直接发送。

## 可复制的指令

```text
请帮我安装并配置本机Mullvad Adapter，已有安装则先核对版本/状态，不重复安装。
先读AGENTS.md、本文、README.md、docs/agent-installation.md、
docs/windows-handoff.md、docs/network-capabilities.md、docs/clash-connect.md、
PUBLIC_BASELINE.json和docs/templates/network-report.md。

在本次安装配置委托范围内，代为下载并校验Release、正常安装、检测/复用Clash，
缺少受支持Clash时从官方来源正常安装；引导我在应用里填写账号、确认必要设备操作。
账号登录成功后选城市连接，再一键写入Clash，协助选用配置并核对实际生效。
不要让我手动找YAML导入。账号/管理员密码不发聊天，不读取私钥；创建本机设备要我
明确确认，满额不能自动删设备。系统提示由我处理，你说明步骤后继续核查。

不下载开发工具、不编译、不改源码，代码缺陷仍发回维护者构建。
已有可用配置保留；范围外/会替换它的变更先说明影响与回滚。若用户此次只问排障，
先只读检查，不自行安装。缺少本机工具时请我完成一个具体步骤后继续，不假装已操作。

核对系统/安装包/实际runtime source_sha、Clash版本和选中配置。应用原始JSON可能含
logs_path、last_report_path、account.devices、公钥与设备ID。先在本机用PowerShell
白名单提取必要状态与事件，不把原始JSON整体回显到工具输出/模型上下文或附进报告。
记录故障时间，分开物理网络、账号API、UDP/SS、WireGuard、内层DNS、
HTTPS、Clash规则/真实出口。Ping通不等于隧道可用，模型超时也不能直接归因。
保护自己的独立联网路径。不要睡眠、断网、改电源/防火墙、重置网卡、删数据/撤销
设备/显示私钥，或循环开关窗口、抢焦点。

安装后分别验收版本/后台、账号身份、IP/DNS/HTTPS、Clash登记/选中、真实规则和出口，
生成 reports-to-maintainer/SETUP-RESULT-<本地日期时间>.md。失败时按网络模板再生成
reports-to-maintainer/NETWORK-REPORT-<本地日期时间>.md，写实际事实、
证据编号、复现步骤、对照结果、原因/置信度、缺失证据、最小修复建议
（仓库相对路径/符号）、配置方案/回滚和后续验收。不能确认写未知，没执行写NOT_RUN。
不含账号、token/API Key、私钥、完整订阅和用户目录；原始材料留本机，对外只给
脱敏摘录。区分配置、代码缺陷和环境限制，不把临时换城市当根因修复。

最后告诉用户报告保存位置、删了哪些敏感字段和PASS/FAIL/NOT_RUN/BLOCKED。
让用户审阅并私下把文档发回维护者。不要自动上传公共GitHub、发消息或提交报告。
```

## 本机白名单摘录示例（无需安装工具）

先由用户或本地工具设置实际路径变量 `$reportPath`，不要把原始JSON直接输出。下面只输出所列字段；需要其他字段时逐项检查用途和敏感性，不能改成输出整个status/account/connection对象。

```powershell
$localReport = Get-Content -LiteralPath $reportPath -Raw | ConvertFrom-Json
[pscustomobject]@{
  schema = $localReport.schema
  generated_at = $localReport.generated_at
  source_sha = $localReport.status.source_sha
  status = $localReport.status.status
  physical_attachment = $localReport.status.physical_attachment
  front_ready = $localReport.status.front_ready
  readiness = $localReport.status.readiness
  active_relay = $localReport.status.active_relay
  phase = $localReport.status.connection.phase
  ip_probe = $localReport.status.connection.probes.ip
  dns_probe = $localReport.status.connection.probes.dns
  https_probe = $localReport.status.connection.probes.https
  logging_degraded = $localReport.status.logging_degraded
  events = @($localReport.events | Select-Object time, layer, stage, code)
} | ConvertTo-Json -Depth 5
```

这是排障起点，不保证足以定位每次故障；缺少UDP/WG阶段细节时在本机逐项提取非敏感字段，注明缺口。不要因为报告结构有字段白名单就把它称为完整匿名化报告。

## 维护者修改和复验

[报告模板](docs/templates/network-report.md)至少包含版本、故障时间、现象、分层证据、判断依据/反证、复现方法、建议修复位置和验收步骤。未定位也交出事实和未知，不编造原因。

维护者收到文档后复核、改源码，在自己的Windows环境或可用CI构建完整安装器，核对SHA256后发新版；朋友在同场景复验。建议diff不等于可安装程序。

朋友不必先下载开发工具链。完全断网时云模型也可能不可用，保留日志，恢复独立网络后分析，不为排障先清空配置。
