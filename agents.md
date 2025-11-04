# 🧠 agents.md

## Agent: `cloudflare-angie-mcp-agent`

### 🗂️ Project Overview

This repository contains the **Cloudflare-hosted MCP (Model Context Protocol) server** and companion **WordPress plugin (PCW Angie Connector)** that integrate with the **Angie AI Assistant**. The system allows Angie to dynamically register and communicate with remote MCP servers using Cloudflare Workers for scalability and WordPress for UI and context management.

The repository is structured for Codex automation and human collaboration. Codex builds the Worker, deploys it to Cloudflare, and verifies registration via Angie using WordPress REST endpoints.

---

### ⚙️ Build and Test Commands

#### **Build Commands**

```bash
# Cloudflare Worker build
cd mcp-cloudflare
npm install
npx tsc --noEmit
npx wrangler build

# WordPress Plugin build
cd ../wp-content/plugins/pcw-angie
npm install
npx tsc --noEmit
npm run build
```

#### **Test Commands**

```bash
# Unit tests
npm test

# Type checks
npx tsc --noEmit --strict

# Lint checks
npx eslint . --ext .ts,.js

# Integration verification
curl -sS https://mcp.pcwprops.cloudflare.dev/mcp | head -n 1
```

#### **Deploy Commands**

```bash
cd mcp-cloudflare
npx wrangler deploy
```

---

### 💻 Code Style Guidelines

| Rule Type      | Description                                             |
| -------------- | ------------------------------------------------------- |
| **Language**   | TypeScript with strict mode enabled                     |
| **Formatting** | Use Prettier defaults, 2-space indentation              |
| **Comments**   | Use JSDoc for all exported functions                    |
| **Naming**     | kebab-case for filenames, camelCase for variables       |
| **Commits**    | Follow Conventional Commits (`feat:`, `fix:`, `chore:`) |
| **Secrets**    | Never print secret values; resolve them via `op read`   |

---

### 🧪 Testing Instructions

* **Unit Testing:**

  * Each MCP tool must have at least one success and one failure test.
  * Use Jest or Vitest for testing Worker logic.
* **Integration Testing:**

  * Deploy to staging using `wrangler deploy --env staging`.
  * Confirm the `/mcp` route returns `{ "status": "MCP active" }`.
* **WordPress Plugin Testing:**

  * Verify Angie detects the Cloudflare MCP automatically in the admin panel.
  * Test the `register-new-mcp` tool manually through Angie prompts.
* **CI Workflow:**

  * See `.github/workflows` for CI steps.
  * Run all checks via `pnpm turbo run test --filter <project_name>`.

---

### 🔐 Security Considerations

* **Secrets Management:**

  * All sensitive values are stored in 1Password and accessed via the `OP_SERVICE_ACCOUNT_TOKEN`.
  * Secrets are only accessible in setup scripts and should never be echoed.
* **OAuth:**

  * Cloudflare MCP authenticates using OAuth; tokens are ephemeral and never persisted to disk.
* **WordPress Security:**

  * Use `current_user_can()` to check permissions.
  * Sanitize all inputs and validate URLs with `FILTER_VALIDATE_URL`.
* **CORS and Network Access:**

  * Restrict cross-origin requests to trusted domains.
* **Error Handling:**

  * Always return user-friendly error messages while logging detailed errors server-side.

---

### 🪜 Deployment Steps

1. Checkout or create the branch `feature/cloudflare-angie-mcp`.
2. Build both Worker and plugin using commands above.
3. Deploy using Wrangler CLI:

   ```bash
   cd mcp-cloudflare
   npx wrangler deploy
   ```
4. Verify health and registration:

   ```bash
   curl https://mcp.pcwprops.cloudflare.dev/mcp
   ```
5. Confirm Angie integration in WordPress admin.
6. Run `npx wrangler deployments list` and note deployment IDs.
7. Open a PR to `develop` branch once verified.

---

### ✍️ Commit & PR Guidelines

#### **Commit Message Format (Conventional Commits)**

```bash
feat(worker): add register-new-mcp tool
fix(plugin): correct Angie SDK registration
chore(build): update wrangler.toml
```

#### **Pull Requests**

* **Title:** `[cloudflare-angie-mcp] <Title>`
* **Target Branch:** `develop`
* **Checklist:**

  * ✅ All lint & type checks pass
  * ✅ Unit tests pass
  * ✅ Deployment verified
  * ✅ CI plan successful

---

### ⚠️ Security Gotchas

* Do **not** import `AngieMcpSdk` in the Worker (no DOM support).
* Never expose secrets or tokens in logs.
* Always validate URLs and user input.
* Use nonces for WordPress REST requests.

---

### 📦 Large Dataset & Log Handling

* Do not store large logs or datasets in Workers.
* Use Cloudflare KV or D1 for lightweight metadata only.
* Redact sensitive data from logs.

---

### 💡 Dev Environment Tips

* Use `pnpm dlx turbo run where <project_name>` to jump to a package quickly.
* Run `pnpm install --filter <project_name>` to ensure IDEs can resolve dependencies.
* Spin up new packages with `pnpm create vite@latest <project_name> -- --template react-ts`.
* Confirm `name` field in `package.json` matches expected project name.

---

### 🧩 Extra Instructions for Codex Agents

* Environment: Node >= 18, wrangler CLI, and 1Password CLI preinstalled.
* Secrets: Access via `op read`, not environment variables.
* MCP configuration file located at `/workspace/.mcp/config.json`.
* Network access always enabled.
* Setup script installs all dependencies and persists environment variables in `~/.bashrc`.
* Maintenance script reauthenticates 1Password and validates MCP endpoints after container resumes.

---

### 🧰 Contact & Ownership

**Owner:** Phillip Weitlauf (PCW Integrates)
**Support:** [engineering@pcwprops.com](mailto:engineering@pcwprops.com)
**Version:** 1.0.0
**License:** MIT
