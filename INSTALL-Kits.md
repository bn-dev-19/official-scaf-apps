# Installing a SCAF kit

A kit archive (`kits/scaf-kit-<version>.zip`) contains the **content of a project's `scaf/` folder**: `_agent-kit/` (documentation, skills, tools), `.scaf-version`, `redocly.yaml`, an empty `v0/` project skeleton and `INSTALL.md` (from 0.4.0-beta.2).

## 1. Install the archive

From the root of your project:

```bash
mkdir -p scaf
curl -L -o scaf-kit.zip https://github.com/bn-dev-19/official-scaf-apps/raw/main/kits/scaf-kit-<version>.zip
unzip scaf-kit.zip -d scaf && rm scaf-kit.zip
```

Replace `<version>` with a version listed in [README.md](README.md) (e.g. `0.4.0-beta.2`).

- **Upgrading an existing project**: only replace `scaf/_agent-kit/` (`rm -rf scaf/_agent-kit` then unzip it again). Never overwrite your own `scaf/v*/` folders or `scaf/.scaf-version`.
- The kit's own version is in `scaf/_agent-kit/.scaf-kit` (not the same thing as `scaf/.scaf-version`, which is your project's active version).

## 2. Tell your AI agent that `scaf/` exists

Claude Code, opencode, Codex… read instruction files at the project root. Add the kit's block to them:

1. Take `scaf/_agent-kit/templates/agents-block.md` (from `<!-- scaf-kit:begin` to `<!-- scaf-kit:end -->`).
2. Put it in `AGENTS.md` (opencode, Codex…) and in `CLAUDE.md` (Claude Code) at the project root. If `AGENTS.md` already holds it, `CLAUDE.md` can contain only the line `@AGENTS.md`. Create the files if they do not exist.
3. To update later, **replace** what is between the markers; never add the block twice.

Or simply ask your agent: *"Read `scaf/INSTALL.md` and do it."*

## 3. Requirements and check

- **Ruby** (standard library only) for the specification tools: audit, OpenAPI, SQL, mocks.
- **Node + pnpm** only for the app generators (`tools/nextjs`, `tools/nestjs`): `cd scaf/_agent-kit/tools && pnpm install`.

```bash
ruby scaf/_agent-kit/tools/audit.rb scaf/$(cat scaf/.scaf-version)
```

Then start with `scaf/_agent-kit/README.md`.

## 4. Updating the kit (on request)

Once the agent block is installed, you can simply ask your agent to *"update the scaf kit"*. It follows `scaf/_agent-kit/skills/scaf-update-kit.md`:

1. reads the installed `version` and the `repository` URL in `scaf/_agent-kit/.scaf-kit`;
2. lists the archives in `kits/` of this repository and picks the latest stable one (a pre-release only if you already use one or ask for it);
3. downloads it into a temporary folder, shows you the changelog and **waits for your confirmation**;
4. replaces **only** `scaf/_agent-kit/` (backup kept), refreshes the agent block in `AGENTS.md` / `CLAUDE.md`, and runs the audit.

`scaf/v*/` and `scaf/.scaf-version` are never touched. Manual alternative: download `kits/scaf-kit-<version>.zip` and replace `scaf/_agent-kit/` yourself.
