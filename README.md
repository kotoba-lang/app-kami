# app-kami

**KAMI アプリケーションシェル。** `kami.etzhayyim.com` / `worlds.etzhayyim.com` 系の
KAMI ワークベンチとゲーム appview を収める **application 面の repo** であって、
エンジン本体ではない。エンジンは `kotoba-lang/kami-engine` にある
（境界の宣言は `README.edn` の `:boundary`）。

`app-` は role 面の prefix（`manifest/repository-rules.edn` の `:plane-order`）。
この repo が所有するのは **UI シェル・appview 配信物・シーン定義・E2E** で、
形状 / scene graph / animation / render の正本は持たない。

## 由来

etzhayyim monorepo の `60-apps/etzhayyim-project-kami` から抽出した移行スナップショット。
出所と抽出元 revision は `migration.edn`（`:source-revision` / `:source-path`）が持つ。
ライセンスは Apache-2.0 + etzhayyim Charter Compliance Rider v3.1（`NOTICE`）。

## この repo に実際に入っているもの

| path | 中身 |
|---|---|
| `appview/etzhayyim-wasm-kami-*/` | 10 appview パッケージ（Svelte + TS、うち 3 つが `wrangler.jsonc` を持つ Worker 配信物、静的 `kami_web_bg.wasm` を同梱するものがある） |
| `scenes/*.jsonld` | 8 シーン定義（JSON-LD。`IslandScene` 系） |
| `kotoba/` | `@etzhayyim/kami-kotoba` — KAMI catalog の TS 参照実装（`@etzhayyim/sdk` 依存、`vitest`） |
| `e2e/` | Playwright E2E（`tests/*.spec.ts` 5 本 + `screenshots/`） |
| `docs/` | 設計文書 4 本（kami-engine / workbench / umu-godot-wasm / umu 納品） |
| `games/` | Godot Web Export 納品物の配置規約（`games/README.md`） |
| `AGENTS.md` | 抽出**前**のシステム全体の記述。`40-engine/kami-engine/` 等、この repo に無い path を指す箇所がある — 現在地の正本として読まないこと |

## 既知のギャップ（このまま読むと誤解する点）

- **`AGENTS.md` はこの repo の現在地を述べていない。** 抽出前の monorepo 全体
  （Rust エンジン crate、WIT パッケージ、ランタイム）を記述しており、そのほとんどは
  ここに存在しない。エンジンの現在地は `kotoba-lang/kami-engine` を見る。
- **appview は Svelte + TypeScript のまま。** workspace 規則
  （ADR-2608260900、2026-08-26 オーナー指示）では新規 UI は cljs + reagent + re-frame +
  `jp-go-dds` で書き、`.svelte` / `.tsx` は移行対象。この repo の appview 群は
  **未移行の既存資産**であって、新しい UI の手本ではない。
- **`kotoba/` と `e2e/` の依存は未 install の状態で checkout される。** 走らせるには
  それぞれのディレクトリで install が要る（`kotoba/` は `vitest run`、`e2e/` は
  `playwright test`）。この README は**それらが今 green であることを主張しない** ——
  未測定である。
- appview の依存 `@etzhayyim/sdk` / `@etzhayyim/sdk-mock` は git URL 固定 pin。
  未 merge branch 上の commit を pin にしない規則（AGENTS.md）はここにも当たる。

## 変更するとき

west 管理下の共有 checkout（`orgs/kotoba-lang/app-kami`）を直接編集しない。
worktree を切って作業し、push → `gh api repos/kotoba-lang/app-kami/merges` の
サーバ側マージで着地させ、そのあと west pin を前進させる
（`kbb --backend sci --classpath ".:scripts/nbb_compat" scripts/west-pin-put.cljs app-kami HEAD`）。
rebase / force-push はしない。
