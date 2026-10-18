# Windows desktop workbench

The Tauri 2 / React / TypeScript UI talks to the independent Windows Rust host through authorized, versioned local IPC. It reads real city/catalogue, settings, device, account and diagnostic snapshots. A browser preview has no native host and displays that limitation; it does not manufacture an online connection.

From this directory, `npm ci --ignore-scripts && npm run build` checks TypeScript and builds the bundled frontend. Windows native builds require Rust 1.90 or newer (CI pins 1.97.1), the MSVC build tools and WebView2 for development. These are developer requirements, not instructions for an eventual installed user.

Build the agent with `ADAPTER_SOURCE_SHA` set to the exact source commit. Stage its pinned runtime with `packaging/windows/stage_runtime.py`; compile the UI with the same source SHA and `ADAPTER_RUNTIME_MANIFEST_SHA256` set to that exact runtime manifest digest. The UI expects the five-file `runtime/` directory beside its executable. Source/version mismatch is rejected before any request is sent to an already running host. Do not mix separately built executables without reassembling and verifying their manifest.

Closing the window hides it; the tray can reopen it or quit the UI while the backend remains independent. The diagnostics page creates a public report and copies the current report path with the native Windows clipboard. Clipboard contention keeps the file and exposes a retry. No renderer command accepts arbitrary clipboard text, shell commands or filesystem paths. No remote UI is loaded.

Connect and full testing are still explicitly blocked until physical-egress and connection transaction acceptance. Quick ICMP maintenance is implemented and does not start a WireGuard worker. Required multihop/PQ remain unsupported rather than silently falling back. See [prototype boundaries](../docs/windows-prototype.md).

`bundle.active` remains false until the installer gate is implemented. The NSIS configuration specifies current-user installation and bundled offline WebView2, but configuration alone is not an installer or clean-machine acceptance. No Tauri shell, arbitrary filesystem, process or network plugin is enabled. No remote UI is loaded.

Record Windows interaction, independent backend/tray lifecycle, clean installation, absent WebView2, upgrades, rollback and actual routing separately from compilation. Signing and release remain separate gates.
