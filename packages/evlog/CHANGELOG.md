# evlog

## 2.10.0

### Minor Changes

- [#225](https://github.com/HugoRCD/evlog/pull/225) [`3d1dcd4`](https://github.com/HugoRCD/evlog/commit/3d1dcd4678da83c05e754623b7443426231565ab) Thanks [@izadoesdev](https://github.com/izadoesdev)! - Add HyperDX drain adapter (`evlog/hyperdx`) for OTLP/HTTP ingest, with defaults aligned to [HyperDX OpenTelemetry documentation](https://hyperdx.io/docs/install/opentelemetry) (`https://in-otel.hyperdx.io`, `authorization` header). Includes docs site and `review-logging-patterns` skill updates.

## 2.9.0

### Minor Changes

- [#212](https://github.com/HugoRCD/evlog/pull/212) [`96c47cd`](https://github.com/HugoRCD/evlog/commit/96c47cd3adfbaf0e6c53db9be55b45f652dfbdb8) Thanks [@MrLightful](https://github.com/MrLightful)! - Add React Router middleware integration (`evlog/react-router`) with automatic wide-event logging, drain, enrich, and tail sampling support

### Patch Changes

- [#220](https://github.com/HugoRCD/evlog/pull/220) [`b0c26d5`](https://github.com/HugoRCD/evlog/commit/b0c26d5eacb2382402a0ab99744650796ea52be7) Thanks [@HugoRCD](https://github.com/HugoRCD)! - fix(nitro): make `evlogErrorHandler` compatible with TanStack Start's `createMiddleware().server()` API

  `evlogErrorHandler` now accepts both `(next)` and `({ next })` signatures, so `createMiddleware().server(evlogErrorHandler)` works directly without a wrapper in all TanStack Start versions.

- [#215](https://github.com/HugoRCD/evlog/pull/215) [`31cb4ab`](https://github.com/HugoRCD/evlog/commit/31cb4ab903c969107a368cb5a9629eff6fe0c63b) Thanks [@HugoRCD](https://github.com/HugoRCD)! - fix(nitro): always create logger in request hook so `useLogger()` works in server middleware

  Previously, calling `useLogger(event)` inside a Nuxt server middleware would throw `"Logger not initialized"` because the Nitro plugin skipped logger creation for routes not matching `include` patterns. Since middleware runs for every request, this made it impossible to use `useLogger` there.

  The `shouldLog` filtering is now evaluated at emit time instead of creation time — the logger is always available on `event.context.log`, but events for non-matching routes are silently discarded.

- [#218](https://github.com/HugoRCD/evlog/pull/218) [`453a548`](https://github.com/HugoRCD/evlog/commit/453a5483d1a7b2db7979edbc306cd9b9584e9f40) Thanks [@benhid](https://github.com/benhid)! - fix(parseError): respect `.status` / `.statusCode` on Error instances instead of hardcoding 500

  Frameworks like NestJS attach HTTP status directly on Error subclasses (e.g. `BadRequestException` has `.status = 400`). Previously, `parseError()` ignored these properties and always returned 500 for any `Error` instance without a `data` property. Now uses `extractErrorStatus()` to extract the correct status.

- [#219](https://github.com/HugoRCD/evlog/pull/219) [`79f811d`](https://github.com/HugoRCD/evlog/commit/79f811dab02717470ed5f178b5c944a395dc4025) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Improve TanStack Start documentation with route filtering, pipeline (batching & retry), tail sampling sections, Vite plugin callout, and TanStack Router vs TanStack Start disambiguation

## 2.8.0

### Minor Changes

- [#196](https://github.com/HugoRCD/evlog/pull/196) [`abda28c`](https://github.com/HugoRCD/evlog/commit/abda28cc00b6276a59c2cf9dcfca295f4d7b878c) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add `evlog/ai` integration for AI SDK v6+ observability.
  - `createAILogger(log)` returns an `AILogger` with `wrap()` and `captureEmbed()`
  - Model middleware captures token usage, tool calls, finish reason, and streaming metrics
  - Supports `generateText`, `streamText`, `generateObject`, `streamObject`, and `ToolLoopAgent`
  - Accumulates data across multi-step agent runs (steps, models, tokens)
  - String model IDs resolved via `gateway()` with full autocompletion
  - Gateway provider parsing extracts actual provider and model name
  - Streaming metrics: `msToFirstChunk`, `msToFinish`, `tokensPerSecond`
  - Cache tokens (`cacheReadTokens`, `cacheWriteTokens`) and reasoning tokens tracked
  - Error capture from failed model calls and stream error chunks
  - `captureEmbed()` for embedding calls (`embed`, `embedMany`)
  - `ai` is an optional peer dependency

- [#189](https://github.com/HugoRCD/evlog/pull/189) [`d92fb46`](https://github.com/HugoRCD/evlog/commit/d92fb46b2d272dca0de73a0ffedda746304f57b6) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add `evlog/vite` plugin for build-time DX enhancements in any Vite-based framework.
  - Zero-config auto-initialization via Vite `define` (no `initLogger()` needed)
  - Build-time `log.debug()` stripping in production builds (default)
  - Source location injection (`__source: 'file:line'`) for object-form log calls
  - Opt-in auto-imports for `log`, `createEvlogError`, `parseError`
  - Client-side logger injection via `transformIndexHtml`
  - New `evlog/client` public entrypoint
  - Nuxt module gains `strip` and `sourceLocation` options (no breaking changes)

### Patch Changes

- [#197](https://github.com/HugoRCD/evlog/pull/197) [`3601d30`](https://github.com/HugoRCD/evlog/commit/3601d303c122509a8f665f20e8275248e6e6e7f5) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add retry with exponential backoff to all HTTP drain adapters and improve timeout error messages.
  - Transient failures (timeouts, network errors, 5xx) are retried up to 2 times with exponential backoff (200ms, 400ms)
  - `AbortError` timeout errors now display a clear message: `"Axiom request timed out after 5000ms"` instead of the cryptic `"DOMException [AbortError]: This operation was aborted"`
  - New `retries` option on all adapter configs (Axiom, OTLP, Sentry, PostHog, Better Stack)
  - 4xx client errors are never retried

## 2.7.0

### Minor Changes

- [#175](https://github.com/HugoRCD/evlog/pull/175) [`aa18840`](https://github.com/HugoRCD/evlog/commit/aa18840459b4adced2747f70ebe0fed394348195) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add file system drain adapter (`evlog/fs`) to write wide events as NDJSON files to the local file system with date-based rotation, size-based rotation, automatic cleanup, and `.gitignore` generation

- [#174](https://github.com/HugoRCD/evlog/pull/174) [`a77a69a`](https://github.com/HugoRCD/evlog/commit/a77a69a11caf350e190d0e9eae743c904a86cf4c) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add `silent` option to suppress console output while still passing events to drains, fix drain pipeline to prevent double-draining in framework integrations, and add central configuration reference page to docs

### Patch Changes

- [#178](https://github.com/HugoRCD/evlog/pull/178) [`2b26ed2`](https://github.com/HugoRCD/evlog/commit/2b26ed2682cd98b21a5a64a44e6f3337018bae3c) Thanks [@ruisaraiva19](https://github.com/ruisaraiva19)! - Use request `originalUrl` for correct path extraction in NestJS and Express integrations (`evlog/nestjs`, `evlog/express`)

- [#172](https://github.com/HugoRCD/evlog/pull/172) [`d87d1e0`](https://github.com/HugoRCD/evlog/commit/d87d1e03ae47b913338f6d73bd7ed874316e749b) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Remove `@sveltejs/kit` optional peer dependency that caused `ERESOLVE` failures in non-SvelteKit projects (e.g. Nuxt 4) due to transitive `vite@^8.0.0` requirement

## 2.6.0

### Minor Changes

- [#169](https://github.com/HugoRCD/evlog/pull/169) [`e38787f`](https://github.com/HugoRCD/evlog/commit/e38787f08ea63bbff4ba2fea10945b2f9af94ef5) Thanks [@OskarLebuda](https://github.com/OskarLebuda)! - Add `evlog/toolkit` entrypoint exposing building blocks for custom framework integrations (`createMiddlewareLogger`, `extractSafeHeaders`, `createLoggerStorage`, `extractErrorStatus`)

### Patch Changes

- [#164](https://github.com/HugoRCD/evlog/pull/164) [`d84b032`](https://github.com/HugoRCD/evlog/commit/d84b03277d20cce649e4711db2e6bedbafd3f0f4) Thanks [@oritwoen](https://github.com/oritwoen)! - Fix browser DevTools pretty printing to use CSS `%c` formatting instead of ANSI escape codes (fixes Firefox rendering), share CSS color constants between standalone and client loggers, and escape `%` in dynamic values to prevent format string injection

- [#166](https://github.com/HugoRCD/evlog/pull/166) [`5f45b3f`](https://github.com/HugoRCD/evlog/commit/5f45b3ff01d2f73dbd92de14e384608541002bd3) Thanks [@schplitt](https://github.com/schplitt)! - Fix Nitro v3 error handler registration and update to Nitro v3 beta

## 2.5.0

### Minor Changes

- [`d7b06fa`](https://github.com/HugoRCD/evlog/commit/d7b06faba5704aa97fe1b9a46628be974a1b8a37) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add default condition to subpath exports for CJS compatibility and fix OTLP batch grouping by resource identity

## 2.4.1

### Patch Changes

- [`8ade245`](https://github.com/HugoRCD/evlog/commit/8ade2455ecc8f8da37e71fe19b7302dfb1563d69) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Restore useLogger() JSDoc for IntelliSense and remove unused RequestLogger import from Fastify adapter

## 2.4.0

### Minor Changes

- [#141](https://github.com/HugoRCD/evlog/pull/141) [`91f8ceb`](https://github.com/HugoRCD/evlog/commit/91f8cebe3d00efcd1b9fc8795b2b272a17b8258d) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add NestJS integration (`evlog/nestjs`) with Express-compatible middleware, `useLogger()` via AsyncLocalStorage, and full pipeline support (drain, enrich, keep)

- [#142](https://github.com/HugoRCD/evlog/pull/142) [`866b286`](https://github.com/HugoRCD/evlog/commit/866b28687cd9cae2dfe347c5831a3c62648906ef) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add SvelteKit integration (`evlog/sveltekit`) with handle hook, error handler, `useLogger()`, and `createEvlogHooks()` for automatic wide-event logging, drain, enrich, and tail sampling support

## 2.3.0

### Minor Changes

- [#135](https://github.com/HugoRCD/evlog/pull/135) [`e3e53a2`](https://github.com/HugoRCD/evlog/commit/e3e53a2dac958e0ede9dffb70623f90ff800c0bc) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add Elysia plugin integration (`evlog/elysia`) with automatic wide-event logging, drain, enrich, and tail sampling support

## 2.2.0

### Minor Changes

- [#134](https://github.com/HugoRCD/evlog/pull/134) [`2f92513`](https://github.com/HugoRCD/evlog/commit/2f9251346384eef42cc209919ae367aee6054845) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add Express middleware integration (`evlog/express`) with automatic wide-event logging, drain, enrich, and tail sampling support

- [#132](https://github.com/HugoRCD/evlog/pull/132) [`e8d68ac`](https://github.com/HugoRCD/evlog/commit/e8d68acf7e6ef44ad4ee44aff2decc4a4885d73f) Thanks [@HugoRCD](https://github.com/HugoRCD)! - Add Hono middleware integration (`evlog/hono`) for automatic wide-event logging in Hono applications, with support for `drain`, `enrich`, and `keep` callbacks

## 2.1.0

### Minor Changes

- [`f6cba9b`](https://github.com/HugoRCD/evlog/commit/f6cba9b39a84e88ae44eef8ea167e6baa3a43e51) Thanks [@HugoRCD](https://github.com/HugoRCD)! - bump version
