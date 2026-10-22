# ZXV Developer Kit

**Drop-in integration for the paraconsistent settlement layer on NEAR mainnet.**

The ZXV world computer is 18 registered subsystems (13 originals + the
consolidated `zxv-core` covering verdict-guard, treasury, rails, and
receipts) — all discoverable through one registry call. This kit gives
you the four-valued gate, the rail card networks, the dispute-tolerant
oracle, the work-based treasury, and the receipt chain — typed, verified,
one import.

```
┌─────────────────────────────────────────────────────────────┐
│  YOUR APP / AGENT / PROTOCOL                                 │
│        │                                                     │
│        ▼                                                     │
│  ┌───────────────────────────────────────────────────────┐   │
│  │  zxv-sdk (Rust)  ·  @zxv/developer-kit (TypeScript)   │   │
│  └───────────────────────────────────────────────────────┘   │
│        │                                                     │
│        ▼                                                     │
│  ┌───────────────────────────────────────────────────────┐   │
│  │  NEAR mainnet — 18 registered subsystems              │   │
│  │  gate · ledger · registry · oracle · bus · shield ·   │   │
│  │  micro_infer · model_registry · consumer ·            │   │
│  │  paraconsistent_oracle · settlement_router ·          │   │
│  │  yhv_wrapper · ai_verdict · zxv-core                  │   │
│  │  (verdict_guard · treasury · yhv_redeemer ·           │   │
│  │   rails_registry · rails_receipts)                    │   │
│  └───────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Why ZXV

The paraconsistent settlement invariant: **only `TRUE` settles.**
Contradictions (`TRUE + FALSE = BOTH`) are recorded, never coerced, never
a panic. If your system has multiple evidence sources — oracle feeds, AI
models, agents — ZXV makes disagreement *safe* instead of catastrophic.

## Quick start

### Rust

```toml
[dependencies]
zxv-sdk = "0.1"
```

```rust
use zxv_sdk::{Gate, LogicState, RailNetwork, rail_number_valid, settle_or_hold};

// the settlement invariant
let verdict = Gate::conjoin(LogicState::True, LogicState::False);
assert!(!verdict.is_settleable());

// a rail card number
assert!(rail_number_valid("88090016741821469134", RailNetwork::Dragon));

// evidence fold — Err = held, never a panic
let r = settle_or_hold(&[LogicState::True, LogicState::False]);
assert_eq!(r, Err(LogicState::False));
```

### TypeScript

```bash
npm install @zxv/developer-kit
```

```ts
import { Zxv, LogicState } from "@zxv/developer-kit";

const zxv = new Zxv();
const caps = await zxv.discover();                    // 18 subsystems
const net  = await zxv.verifyCard("88090016741821469134"); // "dragon"
const price = await zxv.oraclePrice("NEAR");          // dispute-tolerant
const ok  = await zxv.isSettleable(LogicState.BOTH);  // false
```

## The 18 subsystems

| Capability | Account | Role |
|---|---|---|
| gate / ledger | `6ca890f7…` | the kernel: the gate + the $YHVH ledger |
| registry | `7eaa2c6f…` | the boot sector: `vm_capabilities()` |
| oracle | `6a57f581…` | the original oracle gateway |
| bus | `3c70e9c6…` | the IPC bus |
| shield | `789457e6…` | the execution shield |
| micro_infer | `86f80143…` | deterministic on-chain AI (64/64 KAT) |
| model_registry | `77361b15…` | content-addressed model manifests |
| consumer | `8947dcb7…` | the integration proof |
| paraconsistent_oracle | `ea85f538…` | dispute-tolerant price harmonizer |
| settlement_router | `8bf17701…` | the receipt rail (settle on TRUE) |
| yhv_wrapper | `1a051ac2…` | wYHVH liquidity adapter |
| ai_verdict | `781fc03d…` | settleable on-chain AI |
| **zxv-core** | `f18889ea…` | the consolidated core (below) |

**`zxv-core`** hosts five capability names in one lean contract:

| Capability | Functions |
|---|---|
| verdict_guard | `conjoin`, `is_settleable` (the safe evidence gate) |
| treasury | `capture_toll`, `quote_toll`, `register_worker` (work-based economy) |
| yhv_redeemer | the wYHVH → YHVH redeem path |
| rails_registry | `verify_number` (Dragon/Phoenix/Thunderbird cards) |
| rails_receipts | `append`, `head`, `verify_chain` (the hash-chained journal) |

## The settlement invariant (why it's safe)

| Evidence | Verdict | Settlement |
|---|---|---|
| all sources agree | `TRUE` | ✅ allowed |
| sources diverge | `BOTH` | ❌ held — recorded, never coerced |
| no evidence yet | `NEITHER` | ❌ waits |
| explicit refutation | `FALSE` | ❌ refused |

## Documentation

- [The white paper](https://github.com/sykomiked/ZXV-NEP-paraconsistent/blob/main/docs/WHITEPAPER.md)
- [The full deployment record](https://github.com/sykomiked/ZXV-NEP-paraconsistent/blob/main/docs/ledger/FORENSIC_LEDGER.md)
- [The oracle-divergence case study](https://github.com/sykomiked/ZXV-NEP-paraconsistent/blob/main/docs/CASE_STUDY_ORACLE_DIVERGENCE.md)

## Repository layout

```
crates/zxv-sdk/        the Rust crate (no_std, zero-dep core)
typescript/            the TypeScript SDK (@zxv/developer-kit)
docs/                  this documentation
examples/              working examples
```

## License

Apache-2.0 — the same license as the ZXV kernel.