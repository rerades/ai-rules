# AI Rules

AI rules used in conjunction with [ai-rules-cli](https://github.com/rerades/ai-rules-cli) to bootstrap new projects with specific rules and guidelines.

## Metamodel Definition

- CLI-guided composition (question tree → rule selection)
- Tag governance (avoiding taxonomic jungle)
- Compatibility/conflicts and requirements between rules
- Audit trail (provenance, "last reviewed", owner)
- Controlled evolution (SemVer + maturity states)

## Taxonomy

Fixed facets with closed values. For extra granularity, use tags with controlled prefixes (topic:_, lint:_, etc.)

### Core Facets (Enums)

**Category:**

- `code` | `foundation` | `language` | `framework` | `tooling` | `qa` | `security` | `accessibility` | `performance` | `architecture` | `documentation` | `templates` | `testing`

**Scope:**

- `global` | `repo` | `package` | `workspace` | `app` | `component` | `page` | `route` | `api` | `ci` | `cd`

**Language:**

- `python` | `java` | `go` | `rust` | `html` | `css` | `shell` | `none` | `javascript` | `node` | `typescript` | `markdown`

**Frameworks:**

- Any string matching pattern `^[a-zA-Z0-9@/._-]+$` (e.g., `react`, `astro`, `vue`, `angular`, `next`, `remix`, `node`, `express`, `fastify`)

**Tooling:**

- Any string matching pattern `^[a-zA-Z0-9@/._-]+$` (e.g., `eslint`, `prettier`, `vitest`, `jest`, `playwright`, `cypress`, `lighthouse`, `axe`, `husky`, `lint-staged`, `turbo`, `vite`, `webpack`, `rollup`, `parcel`, `pnpm`, `npm`, `yarn`)

**Lifecycle:**

- `advisory` | `recommended` | `enforced` (how strongly it's enforced)

**Maturity:**

- `draft` | `beta` | `stable` | `deprecated`

**Stability:**

- `experimental` | `evolving` | `locked`

**Audience:**

- `frontend` | `backend` | `fullstack` | `qa` | `a11y` | `sec` | `devops` | `technical-writer` | `performance-engineer` | `ux` | `architect` | `tech-lead`

**Severity:**

- `info` | `low` | `medium` | `high` | `critical` (impact if violated)

### Relational Fields

- **requires[]**: List of ruleId that must be present
- **conflicts[]**: List of incompatible ruleId
- **supersedes[]**: List of ruleId that this rule replaces (helps with migrations)
- **bundles[]**: Group suggestions (e.g., `baseline/web`, `baseline/react`)

### Operational Fields

- **files/globs**: Paths where the rule applies (e.g., `["src/**/*.tsx"]`)
- **enforcement**:
  - `lint`: `off` | `warn` | `error`
  - `ci`: `allow` | `block` | `required`
  - `scaffold`: `none` | `add` | `update` | `required` | `suggest` (if it generates/edits files)
- **order** (int): Composition priority (lower = enters first)
- **inputs**: For parameterizable rules: `{ name → type/enum/default }`

### Tagged Fields

**Tags with prefix (string[]):**

- `topic:*` (e.g., `topic:core-web-vitals`, `topic:wcag22`)
- `lint:*` (e.g., `lint:eslint-config`, `lint:typescript-eslint`)
- `test:*` (e.g., `test:playwright`, `test:axe`)
- `perf:*` (e.g., `perf:lhci`, `perf:tti`)
- `a11y:*` (e.g., `a11y:aria`, `a11y:keyboard`)
- `sec:*` (e.g., `sec:headers`, `sec:deps`)

**Rule**: Only approved prefixes and max 5 tags.

### Governance

- Vocabulary changes → PR in central catalog with "Owners" review
- Any new enum value → JSON Schema update and CHANGELOG

## Metadata Schema (JSON Schema)

The schema follows the [JSON Schema](https://json-schema.org/draft/2020-12/schema) standard and is defined in `mdc.schema.json`.

## ID Convention

Format: `domain.subdomain.slug` (minimum 2 segments, snake_case prohibited, only `[a-z0-9-]`).

**Examples:**

- `foundation.baseline.web`
- `language.typescript.strict`
- `framework.react.hooks`
- `performance.core-web-vitals`
- `accessibility.wcag22.keyboard`
- `qa.testing.playwright`
- `security.headers.csp`

**Advantages**: Prefix search, tree selection, and logical order when composing.

## Real Example of Front-matter for .mdx

```yaml
---
id: typescript.conventions.guidelines
version: 1.0.0
title: "TypeScript Conventions Guidelines"
description: "Import/export conventions, naming standards, and code style guidelines for TypeScript projects."
category: language
scope: ["global", "repo", "package", "workspace", "app", "component"]
language: "typescript"
frameworks: ["typescript", "react", "next", "astro"]
tooling: ["typescript", "eslint", "prettier"]
lifecycle: "enforced"
maturity: "stable"
stability: "locked"
audience: ["frontend", "backend", "fullstack"]
severity: "low"
alwaysApply: false
globs: ["**/*.ts", "**/*.tsx", "**/*.astro"]
requires: []
conflicts: []
supersedes: []
bundles: ["typescript/conventions", "typescript/style"]
files: ["**/*.ts", "**/*.tsx", "**/*.astro"]
enforcement: { lint: "warn", ci: "allow", scaffold: "none" }
order: 30
inputs:
  enforceImportType:
    {
      type: "boolean",
      default: true,
      description: "Enforce import type for type-only imports",
    }
tags: ["topic:typescript", "topic:conventions", "lint:typescript-eslint"]
owner: "typescript-team@tu-org.com"
review: { lastReviewed: "2025-01-20", reviewCycleDays: 90 }
license: "MIT"
links:
  - {
      rel: "docs",
      href: "https://typescript-eslint.io/",
      title: "TypeScript ESLint",
    }
---
```

## How the CLI Uses This for Conditional Wizard

The CLI uses the metadata to:

1. **Filter rules** by category, language, frameworks, and tooling
2. **Resolve dependencies** using `requires` and `conflicts` fields
3. **Generate bundles** based on `bundles` field
4. **Apply enforcement** based on `enforcement` settings
5. **Order rules** using the `order` field for proper composition
