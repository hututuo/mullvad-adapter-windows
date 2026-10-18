# 给朋友的 WorkBuddy：先排查，生成文档发回

默认是在本机定位问题、保存证据和提出建议，不是现场重写软件，不要求安装Rust/Node/MSVC等开发工具。

## 用户怎么做

1. 从Release下载完整安装器，按README安装。
2. 下载本仓库源码ZIP并解压，或clone，让WorkBuddy打开文件夹。读源码和日志不需开发工具。
3. 确认WorkBuddy模型请求走独立可用网络，不经待测适配器；国产模型名字不是直连证明。
4. 故障时记下本地时间，在适配器“网络诊断”生成报告，把本机文件路径交给WorkBuddy。原始JSON可能含本机用户目录、设备名称/ID与公钥，不能直接发送；先按下面的白名单摘录，不先发公开平台。
5. 把下面指令发给WorkBuddy。报告生成后检查敏感内容，再把**报告文件**私下发给维护者；只发路径，另一台电脑读不到。

## 可复制的指令

```text
请接手本机Mullvad Adapter的部署和连接排障。先读AGENTS.md、本文、README.md、
docs/windows-handoff.md、docs/network-capabilities.md、docs/clash-connect.md、
PUBLIC_BASELINE.json和docs/templates/network-report.md。

默认只读，不下载编译器/依赖、不编译、不改源码、不更换密钥或创建设备。
若需要代码修复，由维护者构建新版。若只是配置问题，先说明精确变更、影响和回滚，
本机用户同意后再改。

核对系统/安装包/实际runtime source_sha、Clash版本和选中配置。应用原始JSON可能含
logs_path、last_report_path、account.devices、公钥与设备ID。先在本机用PowerShell
白名单提取必要状态与事件，不把原始JSON整体回显到工具输出/模型上下文或附进报告。
记录故障时间，分开物理网络、账号API、UDP/SS、WireGuard、内层DNS、
HTTPS、Clash规则/真实出口。Ping通不等于隧道可用，模型超时也不能直接归因。
保护自己的独立联网路径。不要睡眠、断网、改电源/防火墙、重置网卡、删数据/撤销
设备/显示私钥，或循环开关窗口、抢焦点。

按模板生成 reports-to-maintainer/NETWORK-REPORT-<本地日期时间>.md，写实际事实、
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
