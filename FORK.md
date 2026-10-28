# iFwu standalone builds

## v18.13.3-ifwu.1

Base: upstream `v18.13.3` (`413d278a87a69b453efabf8d1fedb9940385e8c6`).
Source branch: `ifwu/v18.13.3-retry-markdown`.

- Preserve headers on Codex WebSocket and SSE errors. The existing provider-error formatter and session retry loop consume `Retry-After`; retry classification, budgets, cancellation and tool replay rules stay unchanged.
- Keep upstream Markdown fix `0032928af339013bf3edbf509e4f8b22617253e9` (#16972), which supersedes #16491. Do not apply the older code-span patch again.
- Do not include #8926: it changes fallback ordering after a retry hint is available, rather than preserving the missing stream metadata. Existing fallback settings remain authoritative.

The default `retry.maxDelayMs` is still 300000. A client that needs to honor the observed 425-second capacity window must configure a larger bound (the dotfiles configuration uses 600000). Longer provider waits fail explicitly rather than being shortened or retried early.

## Build and installation

Push a `v*-ifwu.*` tag to run `.github/workflows/ifwu-release.yml`. It runs TypeScript checks, retry and Markdown regressions, builds macOS arm64/Linux x64/Windows x64 standalone binaries, and smoke-tests each on its native OS before publishing a GitHub Release with `SHA256SUMS.txt`.

The build uses Bun 1.4.4 and unchanged official native addons from npm version 18.13.3. Only the CLI and shared runtime version are labeled `18.13.3-ifwu.1`; no npm packages or upstream update service are published. macOS binaries retain upstream entitlements with an ad-hoc signature and are not notarized.

Download from <https://github.com/iFwu/oh-my-pi/releases>, verify `SHA256SUMS.txt`, and switch the existing local entry only after candidate checks. Preserve the previous binary for rollback. Existing processes continue using their loaded version.

`omp update` still targets the official upstream distribution and can replace this fork. Use the fork GitHub Releases for these installations; retain disabled startup update checks. Config, auth, sessions and extension APIs keep their upstream paths and identities.

Remove the stream-header patch when an upstream release includes the same behavior and passes the WebSocket/SSE retry-advice regressions.
