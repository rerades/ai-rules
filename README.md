# ai-rules

ai rules used in conjunction with ai-rules-cli to startup new projects with specific rules

## Definición del metamodelo

- Composición guiada por el CLI (árbol de preguntas → selección de reglas).
- Gobernanza de tags (evitar jungla taxonómica).
- Compatibilidad/conflictos y requisitos entre reglas.
- Auditoría (provenance, “last reviewed”, owner).
- Evolución controlada (SemVer + estados de madurez).

### taxonomía

facetas fijas y valores cerrados. Si necesitas granularidad extra, usa tags con prefijos controlados (topic:_, lint:_, etc.)
Facetas duras (enums):

- category:
  foundation | language | framework | tooling | qa | security | accessibility | performance | architecture | docs

- scope:
  global | repo | package | workspace | app | component | page | route | api | ci | cd

- language:
  js | ts | python | java | go | rust | html | css | shell | none

- frameworks:
  react | astro | svelte | vue | angular | lit | next | remix | node | express | fastify | none

- tooling:
  eslint | prettier | vitest | jest | playwright | wdio | cypress | lighthouse | axe | pa11y | msw | pact | snyk | osv | husky | lint-staged | turbo | vite | webpack | rollup | parcel | pnpm | npm | yarn

- lifecycle:
  advisory | recommended | enforced (qué tan fuerte se exige)

- maturity:
  draft | beta | stable | deprecated

- stability:
  experimental | evolving | locked

- audience:
  frontend | backend | fullstack | qa | a11y | sec | devops | docs

- severity:
  info | low | medium | high | critical (impacto si se viola)

Campos relacionales:

- requires[]: lista de ruleId que deben estar presentes.
- conflicts[]: lista de ruleId incompatibles.
- supersedes[]: lista de ruleId a los que sustituye (ayuda en migraciones).
- bundles[]: sugerencias de grupos (p.ej. baseline/web, baseline/react).

Campos operativos:

- files (globs): sobre qué rutas aplica (["src/**/*.tsx"]).
- enforcement:
- lint:off|warn|error
- ci:allow|block
- scaffold:none|add|update (si genera/edita archivos)
- order (int): prioridad de composición (menor = entra antes).
- inputs (para reglas parametrizables): { nombre → tipo/enum/default }.

Tags con prefijo (string[]):

- topic:\* (ej. topic:core-web-vitals, topic:wcag22)
- lint:\* (ej. lint:eslint-config, lint:typescript-eslint)
- test:\* (ej. test:playwright, test:axe)
- perf:\* (ej. perf:lhci, perf:tti)
- a11y:\* (ej. a11y:aria, a11y:keyboard)
- sec:\* (ej. sec:headers, sec:deps)
- Regla: solo prefijos aprobados y máx. 5 tags.

Gobernanza:

- Cambios de vocabulario → PR en el catálogo central con revisión de “Owners”.
- Cualquier nuevo valor de enum → actualización del JSON Schema y CHANGELOG.

### esquema de metadatos (JSON Schema)

El esquema sigue el standard de [json-schema](https://json-schema.org/draft/2020-12/schema)

### convención de IDs

Formato: dominio.subdominio.slug (mínimo 2 segmentos, snake prohibido, solo [a-z0-9-]).

Ejemplos:

- foundation.baseline.web
- language.ts.strict
- framework.react.hooks
- performance.core-web-vitals
- accessibility.wcag22.keyboard
- qa.testing.playwright
- security.headers.csp

Ventajas: búsqueda por prefijo, selección por árbol y orden lógico al componer.

### ejemplo real de front-matter para un .mdc

### cómo lo usa el CLI para un wizard condicional.
