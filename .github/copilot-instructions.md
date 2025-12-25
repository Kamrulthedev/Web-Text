# Copilot instructions for this repository

Overview
- Monorepo with three parts: frontend ([src](src)), backend ([server](server)), and database config ([db](db)).
- Backend: NestJS + TypeORM (see [server/src/app.module.ts](server/src/app.module.ts#L1-L40)).
- Frontend: React + Vite (see [src/App.tsx](src/App.tsx) and [src/package.json](src/package.json#L1-L40)).

What good assistant changes look like
- Small, focused edits: add REST endpoints in `server/src/*`, add entities in `server/src/entities/`, update React pages/components in `src/`.
- When changing DB schema, update `db/init_db/*.sql` and keep TypeORM entities in sync.

Architecture notes (quick)
- The Nest app boots from [server/src/main.ts](server/src/main.ts#L1-L40) and listens on port 3001.
- TypeORM is configured inline in [server/src/app.module.ts](server/src/app.module.ts#L1-L40) with `synchronize: true` and `entities: [User]` — entities live under [server/src/entities](server/src/entities).
- Client dev server runs with Vite on port 3000 (see [src/package.json](src/package.json#L1-L40)).
- DB is provided by the Docker compose in [db/docker-compose.yml](db/docker-compose.yml#L1-L60) exposing MySQL on port 3306. Init SQL is in [db/init_db/test.sql](db/init_db/test.sql#L1-L80).

Developer workflows (commands)
- Start DB: `cd db && docker compose up -d` (uses MySQL 8 image, user `docker`/`docker`).
- Start backend (dev): `cd server && npm install && npm run start:dev` (Nest watch on port 3001).
- Start frontend (dev): `cd src && yarn install && yarn dev` (Vite on port 3000).

Project-specific conventions and patterns
- Single-module Nest app: controllers live in `server/src/app.controller.ts`, services in `server/src/app.service.ts`. Add controllers/providers there or create new modules following the same pattern.
- Entities: put TypeORM entities under `server/src/entities/` and register them in the `TypeOrmModule` import list.
- Simple API pattern: current sample routes include `/api/getTest`, `/api/postTest`, `/api/hello` — follow `/api/...` prefix for REST routes.
- DB credentials are hard-coded for local development (host `localhost`, user `docker`, password `docker`, DB `test`). Changes to credentials should be mirrored between `db/docker-compose.yml` and `server/src/app.module.ts`.

Integration points and external deps
- MySQL (mysql2 + TypeORM) — ensure Docker container is healthy before running Nest app.
- NestJS v10 + TypeORM v0.3.x — use decorators and async lifecycle as in existing files.
- Client uses `axios` and `react-router-dom` for API calls and routing.

Testing and linting
- Backend uses Jest configured in `server/package.json` (see `jest` section). Frontend has `jest` devDep but no tests included by default.

Examples (how to add a Murmur endpoint)
1. Add `Murmur` entity to `server/src/entities/murmur.entity.ts` and export it.
2. Register in `TypeOrmModule.forFeature([...])` and `forRoot({ entities: [User, Murmur] })` in [server/src/app.module.ts](server/src/app.module.ts#L1-L40).
3. Add controller route in `server/src/app.controller.ts` under `/api/murmurs`.

Notes for AI agents
- Prefer minimal, testable changes. Run `npm run start:dev` and `yarn dev` locally to validate end-to-end.
- Do not change production assumptions: this repo is a test/assignment scaffold — keep changes explicit and reversible.
- When editing code, reference the file links above in the PR description and include manual test steps (e.g., curl commands or browser URLs).

If anything here is unclear or you'd like the instructions expanded (e.g., more example tasks or conventions), tell me which sections to iterate on.
