# renovate-config

Centralized [Renovate](https://docs.renovatebot.com/) configuration preset, extended by every repo under `alexander-hughes/`.

## How to extend

In each consuming repo, set the entire `.renovaterc.json5` (or `renovate.json`) to:

```json5
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>alexander-hughes/renovate-config:default.json5"]
}
```

The explicit `:default.json5` suffix is required: Renovate's shorthand `github>org/repo` resolver only looks for `default.json` at the preset root. Since this preset uses JSON5 (for inline comments), consumers must name the file explicitly.

Add repo-specific overrides below `extends` as needed. The preset always resolves against `main`, so changes here propagate to all consumers on the next Renovate run.

## What's in the preset

`default.json5` holds **only what is true for every repo** — six sections, ordered so precedence reads top to bottom:

1. Platform behaviour: PR automerge, dashboard, rate limits (5 concurrent), `timestamp-optional`, OSV + vulnerability fast path (`security` label, 0-day bake).
2. Managers every repo uses: pre-commit, `# renovate:` annotations (quoted values allowed; `.env/.sh/.yaml/.toml`), `oci://` refs.
3. Fail-safe defaults: majors and 0.x minors are human (`tier/human`); nothing automerges unless §5 says so.
4. Tooling window: mise, pre-commit, GitHub Actions and annotated tool pins open PRs only on Mondays (ET) and are never re-pushed mid-week. Until a ruleset makes `platformAutomerge` live, a tooling PR also only automerges when a Renovate run on Monday sees green CI — that is why the window is the whole day. Annotated charts and images are exempt (app tier).
5. App ladder: patch/digest/pin 3 d automerge, minor 7 d automerge at ≥1.0 — for container images, Helm charts, pypi/npm, mise CLI pins, hook revs, action pins. PRs carry `type/<updateType>` plus `tier/auto` or `tier/human` (a consumer's later rule replaces the tier). Annotated deps on `github-releases`/`github-tags` (vendor tarballs such as Technitium) are not on the ladder: human merge.
6. Commit-message and label conventions.

**Everything about one repo lives in that repo's `.renovaterc.json5`** — its rules come after the preset's and win on the keys they set. home-ops carries its Flux path patterns, the cluster-critical and HIGH-surface ladders, and its groups; hl-ansible carries the Galaxy/ansible-core ladder; tailscale-policies is `extends` only. Promote a rule here only when a second repo needs it. Example override — hold Cilium patches for a human while containers automerge:

```json5
{ extends: ["github>alexander-hughes/renovate-config:default.json5"],
  packageRules: [{ matchPackageNames: ["/cilium/"], matchUpdateTypes: ["patch"], automerge: false, minimumReleaseAge: "5 days" }] }
```

## Self-hosting future

If/when the org migrates to self-hosted git + self-hosted Renovate, consuming repos switch their `extends` from `github>` to the platform-equivalent prefix (e.g., `gitea>alexander-hughes/renovate-config` or `local>...`). The preset content itself doesn't change. GitHub copy remains as a mirror.
