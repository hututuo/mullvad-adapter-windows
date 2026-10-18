# Public Windows handoff rules

Read README.md, WORKBUDDY_START_HERE.md, docs/agent-installation.md, docs/windows-handoff.md, docs/network-capabilities.md, docs/clash-connect.md and PUBLIC_BASELINE.json first. No private maintainer context is required; do not invent access to private history.

Choose scope from the local user's request. When asked to install/configure, carry out verified Release download, ordinary installation, detection/reuse or official installation of supported Clash, guided account/identity setup, one-click profile import/selection and real acceptance per docs/agent-installation.md. Do not require the user to preinstall everything or ask again for each already-authorized routine step. A URL alone is not installation authorization. Diagnosis-only requests begin read-only. Preserve existing working software/configuration; do not silently downgrade or replace it.

Account/admin credentials are entered by the user in local trusted prompts, not chat. Device creation/activation requires explicit confirmation; never automatically revoke/rotate devices or expose keys. The user handles OS security prompts; do not disable protections. If tools cannot complete a step, ask for that concrete local action and then verify it, without claiming completion. Keep proxy/TUN mode within the user's stated choice. No developer toolchain, compilation or source editing is required for install/diagnosis; code fixes/builds remain with the maintainer unless scope is expanded.

Finish with a local setup receipt (docs/templates/setup-result.md); failures also get a Chinese network report. Raw app JSON can include user paths and device public identifiers: extract allowlisted fields locally before tool output/model context. Collect minimal read-only evidence and unknowns. Authorized configuration changes must be narrow, reversible and recorded.

Protect credentials, keys, DPAPI files, subscription URLs and uncommitted changes. Save the report in reports-to-maintainer/ (ignored by Git), describe redactions, and ask the user to review/send it. Never automatically upload logs/reports to public issues/repos, send messages, copy whole user state or reveal private keys.

Keep Agent/model and remote-control connectivity independent from the tunnel. A domestic product does not automatically bypass Clash/TUN. Separate attachment, API, UDP/SS, WG, DNS, HTTPS, external Clash selection and actual egress. Preserve peer uniqueness, protection, owner/epoch/freshness and cleanup guards.

No automatic sleep, physical disconnection, power/firewall changes, broad network resets, device/data deletion, process-name kills or repeated foreground window/menu/focus tests. Preserve the working desktop and independent network.

Explicitly authorized code work uses short branches, locked dependencies and meaningful checks. No force push/reset/clean/stash or indiscriminate staging. PROPOSED specs are contracts, not current acceptance. Report PASS, FAIL, NOT_RUN and BLOCKED; source/build/install/transport are distinct. Successful builds do not authorize publication, installation or fault injection.
