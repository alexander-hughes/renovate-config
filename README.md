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

`default.json5` carries the cross-cutting rules: package-update cadence, branch / commit message conventions, `packageRules` for ecosystems shared across repos, dashboard / labels / scheduling defaults. Repo-specific rules (e.g., per-repo path filters, per-repo schedules) belong in the consumer.

Originally extracted verbatim from `home-ops/.renovaterc.json5` on 2026-05-29. Future structural changes (e.g., splitting into tier-specific presets `cluster.json5` / `ansible.json5`) will be made here, not in consumers.

## Self-hosting future

If/when the org migrates to self-hosted git + self-hosted Renovate, consuming repos switch their `extends` from `github>` to the platform-equivalent prefix (e.g., `gitea>alexander-hughes/renovate-config` or `local>...`). The preset content itself doesn't change. GitHub copy remains as a mirror.
