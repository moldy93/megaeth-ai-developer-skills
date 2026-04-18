# `@moldy/mega-mpp-sdk`

Thin integration guide for a MegaETH Machine Payments Protocol SDK.

Use this file when the user:
- explicitly names `@moldy/mega-mpp-sdk`
- wants MPP-compatible paid routes on MegaETH
- needs reusable `session` payment channels, not just one-shot x402

Do not use this file for every paid API task:
- use [x402-payments.md](x402-payments.md) for raw Permit2 / x402 proxy integrations without this SDK
- use [meridian.md](meridian.md) when settlement goes through Meridian's facilitator and `/v1/settle`

## Public package entrypoints

Use only these imports:

```ts
import { Mppx, Store, megaeth } from "@moldy/mega-mpp-sdk/server";
import { Mppx as ClientMppx, megaeth as megaethClient } from "@moldy/mega-mpp-sdk/client";
import { megaethMainnet, megaethTestnet } from "@moldy/mega-mpp-sdk/chains";
```

Do not import internal files from the SDK repo.

## Choose the flow

- Default to `charge` for one payment per protected request.
- Use `session` only when the caller explicitly needs a reusable funded channel across many requests.
- For `charge`, keep `submissionMode` omitted unless the user needs `sync` or `sendAndWait`. Omitted means `realtime`.
- For server-broadcast `charge` and all `session` flows, keep the settlement wallet and `recipient` aligned.

## What must stay explicit

Always keep these values explicit:
- `chainId`
- `recipient`

Use published values instead of guessing:

| Network | Chain ID | RPC | Charge Token | Permit2 | Session Escrow |
| --- | --- | --- | --- | --- | --- |
| Mainnet | `4326` | `https://mainnet.megaeth.com/rpc` | USDm `0xFAfDdbb3FC7688494971a79cc65DCa3EF82079E7` | `0x000000000022D473030F116dDEE9F6B43aC78BA3` | bring your own |
| Testnet | `6343` | `https://carrot.megaeth.com/rpc` | USDC `0x75139a9559c9cd1ad69b7e239c216151d2c81e6f` | `0x000000000022D473030F116dDEE9F6B43aC78BA3` | `0xD83A68408539868e5f48D0E93537f56afBB9d512` |

Do not invent a mainnet session escrow address. Mainnet `session` requires a deployed or user-provided escrow contract.

## Cloudflare Worker path

Cloudflare Workers are a first-class supported runtime for this SDK.

When the integration target already lives on Cloudflare:
- keep the payment backend inside the Worker boundary
- keep replay and session state in a shared store such as Durable Objects
- do not create a fresh in-memory store per request for live paid routes
- do not add a separate non-Cloudflare backend unless the user explicitly asks

## Canonical docs

Use the SDK repo as source of truth:
- Agent guide: <https://github.com/ifavo/mega-mpp-sdk/blob/main/docs/agent-integration.md>
- Charge docs: <https://github.com/ifavo/mega-mpp-sdk/blob/main/docs/methods/charge.md>
- Session docs: <https://github.com/ifavo/mega-mpp-sdk/blob/main/docs/methods/session.md>
- Worker demo guide: <https://github.com/ifavo/mega-mpp-sdk/blob/main/demo/README.md>
- Repository overview: <https://github.com/ifavo/mega-mpp-sdk/blob/main/README.md>
