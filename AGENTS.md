# Public Windows handoff rules

Read README.md, WORKBUDDY_START_HERE.md, docs/windows-handoff.md, docs/network-capabilities.md, docs/clash-connect.md and PUBLIC_BASELINE.json first. No private maintainer context is required; do not invent access to private history.

Default assignment: diagnose locally and return a Chinese Markdown report to the maintainer. Do not install compilers/dependencies, build, modify source or create/revoke/rotate devices just to diagnose. Raw app JSON may include user paths and device identifiers/public keys. First extract allowlisted fields locally; never echo the whole raw JSON into tool output/model context. Collect minimal relevant read-only evidence with times, versions, actual routing and unknowns. User-authorized configuration changes must be narrow, reversible and recorded. Code fixes/builds remain with the maintainer unless the receiving user expands scope.

Protect credentials, keys, DPAPI files, subscription URLs and uncommitted changes. Save the report in reports-to-maintainer/ (ignored by Git), describe redactions, and ask the user to review/send it. Never automatically upload logs/reports to public issues/repos, send messages, copy whole user state or reveal private keys.

Keep Agent/model and remote-control connectivity independent from the tunnel. A domestic product does not automatically bypass Clash/TUN. Separate attachment, API, UDP/SS, WG, DNS, HTTPS, external Clash selection and actual egress. Preserve peer uniqueness, protection, owner/epoch/freshness and cleanup guards.

No automatic sleep, physical disconnection, power/firewall changes, broad network resets, device/data deletion, process-name kills or repeated foreground window/menu/focus tests. Preserve the working desktop and independent network.

Explicitly authorized code work uses short branches, locked dependencies and meaningful checks. No force push/reset/clean/stash or indiscriminate staging. PROPOSED specs are contracts, not current acceptance. Report PASS, FAIL, NOT_RUN and BLOCKED; source/build/install/transport are distinct. Successful builds do not authorize publication, installation or fault injection.
