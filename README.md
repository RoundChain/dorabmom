# dorabmon

Peer-to-peer electronic cash. Started from Bitcoin v0.3.19, the last release Satoshi ran.

---

New genesis. Separate network. Same 21M cap, same halving, same 10-minute blocks.

---

## Specifications

| Parameter | Value |
|-----------|-------|
| Proof-of-work | Yespower 1.0 (N=2048, r=32, "dorabmon") |
| Block time | 10 minutes |
| Block reward | 50 dorabmon, halving every 210,000 blocks |
| Max supply | 21,000,000 dorabmon |
| Coinbase maturity | 100 blocks |
| P2P port | 9759 |
| RPC port | 19759 |
| Address prefix | 1 (same encoding as Bitcoin) |
| Genesis hash | `0x0290400ea28d3fe79d102ca6b7cd11cee5eba9f17f2046c303d92f65d6ed2617` |
| Script exec active | block 18,000 |
| Time warp protection | block 16,000 |

---

## Script Engine

All original opcodes run. No exceptions.

| Limit | Value |
|-------|-------|
| Max script size | 10,000 bytes |
| Max stack depth | 1,000 items |
| Max element size | 520 bytes |
| Max opcodes per script | 201 |
| Max sigops per block | 20,000 |
| Max multisig keys | 20 |

What works:

- **OP_CAT** -- concatenation, covenant patterns
- **OP_MUL, OP_DIV, OP_MOD** -- on-chain arithmetic
- **OP_LSHIFT, OP_RSHIFT** -- bit shifts (up to 31)
- **OP_AND, OP_OR, OP_XOR, OP_INVERT** -- bitwise logic
- **OP_SUBSTR, OP_LEFT, OP_RIGHT** -- string slicing
- **OP_CHECKMULTISIG** -- bare m-of-n, up to 20 keys
- **All sighash types** -- ALL, NONE, SINGLE, each with ANYONECANPAY
- **OP_RETURN** -- provably unspendable (original was bugged, fixed)
- Any well-formed script up to 10KB is standard and relays

What is not in dorabmon:

- No P2SH
- No SegWit, no witness data
- No CLTV, no CSV (use nLockTime)
- No Taproot, no Schnorr
- No Schnorr multisig

Signatures are strict DER with low-S. Scripts use separated evaluation. Minimal push encoding. NULLDUMMY enforcement on CHECKMULTISIG.

---

## Fee Policy

```
priority = sum(input_value * confirmations) / tx_size
```

- Threshold: 57,600,000 (1 dorabmon confirmed 1 day in 250-byte tx)
- First 27KB of each block: reserved for high-priority free transactions
- Rest of block: sorted by fee-per-byte
- When fee required: 0.01 dorabmon per KB
- Dust threshold: 0.01 dorabmon (outputs below this always need a fee)

The wallet handles fee calculation automatically.

---

## Address Indexer

Optional per-address UTXO and history index for exchanges, dApps, block explorers.

```ini
indexer=1
```

Auto-syncs on startup. RPC: `getaddressbalance`, `getaddressutxos`, `getaddresstxids`, `getindexerinfo`.

---

## Configuration

| OS | Data directory |
|----|----------------|
| Linux | `~/.dorabmond/` |
| macOS | `~/Library/Application Support/dorabmon/` |
| Windows | `%APPDATA%\dorabmon\` |

`dorabmon.conf` example:

```ini
server=1
rpcuser=user
rpcpassword=pass
gen=0
```

---

## Security

Included from launch:

- Value overflow protection
- Blockchain checkpoints (0)
- Connection and message size rate limits

Added:

- Time warp protection (soft-fork, block 16,000)
- DNS peer discovery with hardcoded fallback
- External IP learned from peers, no HTTP calls
- Anchor connections for eclipse resistance
- Peer group diversity for sybil resistance
- Strict DER + low-S signatures
- SIGHASH_SINGLE out-of-range fix
- CHECKMULTISIG NULLDUMMY
- OP_RETURN hard fail
- Separated script evaluation
- Bounded script execution

---

## Documentation

| Document | Description |
|----------|-------------|
| [docs/RPC_API.md](docs/RPC_API.md) | Full JSON-RPC reference, all 61 commands |
| [docs/RAW_TRANSACTIONS.md](docs/RAW_TRANSACTIONS.md) | Raw transaction construction, signing, multisig workflows |
| [docs/SCRIPT_DEV.md](docs/SCRIPT_DEV.md) | Script developer guide: opcodes, limits, contract patterns, examples |
| [docs/SCRIPT_EXEC.md](docs/SCRIPT_EXEC.md) | Script engine implementation: what changed and why |
| [docs/FEES.md](docs/FEES.md) | Priority-based fee policy details |
| [docs/SPV_CLIENT.md](docs/SPV_CLIENT.md) | SPV protocol specification |
| [docs/SECURITY_FIXES.md](docs/SECURITY_FIXES.md) | Security hardening details |
| [docs/dorabmon.md](docs/dorabmon.md) | dorabmon proof-of-work algorithm |
| [docs/SOLO_MINING.md](docs/SOLO_MINING.md) | Solo mining |
| [docs/POOL_INTEGRATION.md](docs/POOL_INTEGRATION.md) | Mining pool integration, Stratum, NOMP |
| [docs/MINING_OPTIMIZATIONS.md](docs/MINING_OPTIMIZATIONS.md) | Mining performance tuning |
| [docs/BUILD_UNIX.md](docs/BUILD_UNIX.md) | Linux/BSD build |
| [docs/BUILD_MACOS.md](docs/BUILD_MACOS.md) | macOS build |
| [docs/BUILD_WINDOWS.md](docs/BUILD_WINDOWS.md) | Windows build overview |
| [docs/BUILD_WINDOWS_MINGW.md](docs/BUILD_WINDOWS_MINGW.md) | Windows MinGW/MSYS2 |
| [docs/BUILD_WINDOWS_VC.md](docs/BUILD_WINDOWS_VC.md) | Windows Visual Studio |
| [docs/dorabmon_DEVELOPMENT.md](docs/dorabmon_DEVELOPMENT.md) | Design notes and development philosophy |
| [docs/GENESIS.md](docs/GENESIS.md) | Genesis block parameters |

---

## License

MIT. See [license.txt](license.txt).
