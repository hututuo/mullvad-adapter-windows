# Development and CI

This Windows public snapshot omits the original macOS candidate workflow. Mac descriptions below are historical reference. The receiving Agent defaults to diagnosis and a returned report; see WORKBUDDY_START_HERE.md before installing build tools.

For Windows development, start with [deployment and Agent handoff](windows-handoff.md)
and [complete candidate assembly](../packaging/windows/README.md). Windows uses
Rust/Tauri/Node with a Python packaging script; the Ruby/Swift local checks and
arm64 candidate sections below describe the separate macOS implementation.
Installing a prebuilt Windows setup does not require those development tools.

## Architecture changes and specifications

Read [spec-development.md](spec-development.md) before shared-core or Windows work. The proposed [Windows change](../openspec/changes/windows-shared-core/proposal.md) contains requirements/design/tasks; no Windows binary or spec CLI installation is implied. Continue to distinguish unit checks, mock packages, real candidate installation, production handover, signing and release.

## Local checks

From the repository root:

```sh
ruby -c daemon/mullvad_adapter.rb
ruby -I . -I daemon -e 'ARGV.each { |f| require File.expand_path(f) }' daemon/test_*.rb
cd ui
swift test -c release
```

## Candidate build

The GitHub Actions workflow pins Xcode 15.4 and Ruby 3.3.11, runs `checks` first and then `candidate-build`. The latter builds an arm64 Swift release app, verifies its actual Mach-O architecture, creates a zip containing the app, daemon installer/source, and a small install helper, and writes a manifest with the exact source SHA, runner, platform/architecture, file sizes, SHA256 values, and signing status. Signing and public release are separate gates and are not performed by this workflow.

The candidate still needs a real macOS installation/startup test. See `docs/candidate-install.md` for the existing Ruby/Clash/WireGuard/launchd runtime requirements and upgrade procedure. Hosted runner success proves compilation and package assembly only; it does not prove Clash routing, launchd behavior, network egress, UI interaction, old-data upgrade, or clean-machine installation.

## Isolated UI review

After `swift build -c release` in `ui`, run `.build/release/MullvadAdapterUI --preview-cities`. The city dashboard uses clearly labelled example data and intercepts all API calls without opening sockets to the production daemon. City selection and favorite editing are local preview interactions. The preview disables external ping, report export, and notification polling. It is a design/interaction check, not live-network acceptance.

## PQ component

The checks job pins Rust 1.97.1 and checksum-verified protoc 36.2, builds and tests the GPL official config-client helper, verifies arm64, and supplies the exact checked binary to candidate assembly. The package includes its source/lock/license; build tools are not runtime dependencies. Local builds use `scripts/build-pq-helper.sh`; set `PQ_CARGO_BIN` and `RUSTC` explicitly when preserving a host toolchain configuration. See `helpers/pq-negotiator/README.md`. No real tunnel or private identity is present in CI.

## Slow canary evidence

Canary phases keep independent completion timestamps and the shared five-second
freshness gate. After a slow successful hostname check, the collector may refresh
expired IP/DNS phases once on the same owned worker. New DNS is independent
measurement, not retroactive proof of the prior hostname bootstrap. The original
phase budgets and operation cancellation still apply; there is no unbounded
refresh loop. Still-expired evidence is a local stale result, not a negative
relay observation. Active paths are remeasured without replacement; candidates
retry the same relay within their existing recovery budget. Promotion checks
freshness before storage, after its readback and after selector confirmation.
Evidence that expires during storage never reaches the selection call. Existing
ownership, network epoch, protection and final commit fences remain required.
