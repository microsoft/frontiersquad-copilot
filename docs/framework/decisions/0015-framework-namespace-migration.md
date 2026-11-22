# Framework Namespace Migration

* **Status**: Accepted
* **Date**: 2026-11-22

## Context

The owning engineering team renamed internally from DevSquad to FrontierSquad, and the GitHub repository renamed to `microsoft/frontiersquad-copilot`. Every product identifier in the framework previously carried the legacy team prefix, including the repository URL, GitHub Pages base path, marketplace catalog name, plugin identifier, plugin source directory, 28 agent names, hook environment variables, consumer lockfile paths, template provenance headers, documentation pages, and raster logo artwork.

In addition, the two raster logo images rendered the legacy `./devsquad` label directly in pixel artwork with no replacement graphic available yet.

The team needed a namespace migration strategy that updated all framework surfaces, URLs, and plugin distribution metadata cleanly without retaining legacy maintenance burden or broken routes.

## Priorities and Requirements (ordered)

1. **Complete brand and identifier alignment**. Every team reference, repository URL, GitHub Pages route, plugin name, agent identifier, hook script variable, lockfile path, template header, and historical documentation artifact must reflect FrontierSquad.
2. **Valid live URLs and plugin resolution**. GitHub Pages routes, VS Code installation links, CLI marketplace registration commands, and local plugin cache lookups must target `microsoft/frontiersquad-copilot` and `/frontiersquad-copilot/`.
3. **Zero legacy maintenance overhead**. Avoid carrying dual-namespace environment variable fallbacks, dual provenance header matchers, or deprecated alias shims across future releases.
4. **No stale visual branding**. Published documentation and repository headers must not display legacy `./devsquad` artwork.

## Options Considered

### Option 1: Full hard-cut migration across identifiers, URLs, and assets (selected)

Rename all framework, repository, GitHub Pages, plugin, agent, hook, consumer directory, provenance header, cache lookup, and historical documentation identifiers to `frontiersquad` and `frontiersquad-copilot` with no legacy fallback shims, and remove the legacy raster logos.

**Evaluation against priorities**:

* **Complete brand and identifier alignment**: meets. All product, repository, URL, configuration, code, and documentation identifiers use the FrontierSquad namespace.
* **Valid live URLs and plugin resolution**: meets. Astro `base: '/frontiersquad-copilot/'`, `vscode://chat-plugin/install?source=microsoft/frontiersquad-copilot`, `copilot plugin marketplace add microsoft/frontiersquad-copilot`, and plugin cache paths match the renamed repository.
* **Zero legacy maintenance overhead**: meets. Hook scripts, provenance matchers, and environment variables cut over cleanly to `FRONTIERSQUAD_*` and `frontiersquad-template:` with no legacy branches.
* **No stale visual branding**: meets. Removing both logo PNGs and their header and hero references eliminates the outdated `./devsquad` graphic immediately.

### Option 2: Dual-read compatibility bridge for consumer repositories

Rename outgoing identifiers and URLs to `frontiersquad` while keeping fallback readers in `sdd-init.sh`, `detect-tool-extensions.sh`, and `sync-tool-extensions.sh` for `DEVSQUAD_PLUGIN_NAME`, `DEVSQUAD_PLUGIN_DIR`, `.github/devsquad/`, and `devsquad-template:` provenance comments.

**Evaluation against priorities**:

* **Complete brand and identifier alignment**: partially meets because legacy `devsquad` tokens remain embedded across hook scripts and tests.
* **Valid live URLs and plugin resolution**: meets.
* **Zero legacy maintenance overhead**: fails. Every hook change must preserve and test two identifier namespaces during pre-1.0 development, even though minor releases explicitly permit breaking changes.
* **No stale visual branding**: meets when combined with logo removal.

### Option 3: Retain legacy code identifiers and rebrand prose only

Update display names, repository URLs, and documentation prose to FrontierSquad while keeping `devsquad` for plugin directories, agent filenames, lockfiles, and hook variables.

**Evaluation against priorities**:

* **Complete brand and identifier alignment**: fails. Contributors and consumers face permanent mismatch between the FrontierSquad product name and `devsquad.*` agent invocations and paths.
* **Valid live URLs and plugin resolution**: meets.
* **Zero legacy maintenance overhead**: partially meets because code avoids dual branches, yet documentation and support carry permanent cognitive friction explaining the split naming.
* **No stale visual branding**: meets when combined with logo removal.

## Decision

Adopt Option 1 (Full hard-cut migration across identifiers, URLs, and assets) in `v0.13.0`:

* Update all repository and documentation URLs to `https://github.com/microsoft/frontiersquad-copilot` and `https://microsoft.github.io/frontiersquad-copilot/`, including Astro `base: '/frontiersquad-copilot/'`, `vscode://` installation links, CLI marketplace sources, and plugin cache lookup paths.
* Rename marketplace catalog identifier to `frontiersquad-copilot`, plugin identifier to `frontiersquad`, author to `Microsoft CSU FrontierSquad`, and plugin directory to `.github/plugins/frontiersquad`.
* Rename all 28 agent files and identifiers from `devsquad` and `devsquad.<phase>` to `frontiersquad` and `frontiersquad.<phase>`.
* Cut over hook scripts, consumer state directories (`.github/frontiersquad/`), environment variables (`FRONTIERSQUAD_PLUGIN_NAME`, `FRONTIERSQUAD_PLUGIN_DIR`), and template provenance headers (`frontiersquad-template:`) without legacy fallbacks.
* Remove `docs/src/assets/logos/` raster logos and their references in `README.md`, `docs/astro.config.mjs`, and `docs/src/content/docs/index.mdx`.
* Rewrite documentation, ADRs, and changelog history to FrontierSquad.

The accepted trade-off is a one-time breaking upgrade for pre-0.13.0 consumers, who must re-register the `microsoft/frontiersquad-copilot` marketplace source, uninstall `devsquad`, install `frontiersquad`, rename `.github/devsquad/` to `.github/frontiersquad/`, and strip legacy `devsquad-template:` line-1 comments before running `sdd-init.sh`. This aligns with the project's pre-1.0 versioning policy.
