# Golden NestJS Backend Template

Clean, malware-hardened NestJS + Prisma starter. Use this — and only this — to scaffold new backend client repos.

## Stack

- NestJS 11 + TypeScript
- Prisma + PostgreSQL
- JWT auth (`register` / `login` / `me`)
- Swagger at `/docs`
- Config malware scanner (CI + pre-commit)

## Security (non-negotiable)

```bash
npm run security:scan
```

- `scripts/check-malware-configs.mjs` — signature + long-line gate
- `.github/workflows/security-config-scan.yml` — fails PRs with infected configs
- `.husky/pre-commit` — blocks bad commits locally
- Never copy `eslint.config.*` / `prettier.config.*` from old client folders without scanning first


Also blocked by the scanner: fake `public/fonts/fa-solid-400.woff2`, malicious `.vscode/tasks.json` (`folderOpen`), and worm `.bat` helpers.

Policy: **no merge to `main` unless Security Config Scan is green.**

## Quick start

```bash
cp .env.example .env
npm install
# start Postgres (docker compose up -d db) then:
npx prisma migrate dev --name init
npm run security:scan
npm run dev
```

- API: `http://localhost:3000/api`
- Health: `http://localhost:3000/api/health`
- Swagger: `http://localhost:3000/docs`

## Create a new client repo from this template

1. Copy this folder (or use GitHub "Use this template").
2. Rename `package.json` name + README.
3. Run `npm run security:scan` before first `npm install` on any machine that already has other clones.
4. Enable branch protection: require `Security Config Scan` + PR review.
5. Rotate secrets; never commit `.env`.

## Endpoints

| Method | Path | Auth |
|--------|------|------|
| GET | `/api/health` | no |
| POST | `/api/auth/register` | no |
| POST | `/api/auth/login` | no |
| GET | `/api/auth/me` | Bearer JWT |

## Lock `main` (do this after first GitHub push)

```bash
# requires: gh auth login (admin on the repo)
./scripts/enable-branch-protection.sh
# or: ./scripts/enable-branch-protection.sh owner/repo
# solo: APPROVALS=0 ./scripts/enable-branch-protection.sh owner/repo
```

This turns on a GitHub ruleset so **direct pushes to `main` are rejected**. Only pull requests (with required checks) can merge.

## Checklist before shipping a client fork

- [ ] `npm run security:scan` passes
- [ ] `.env` not committed
- [ ] JWT_SECRET rotated
- [ ] `./scripts/enable-branch-protection.sh` run (main is PR-only)
- [ ] Branch protection requires security + CI checks
- [ ] Write access limited to active collaborators
