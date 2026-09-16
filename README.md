# Aegis-Mesh

Offline-first, decentralized state synchronization engine for disaster
resource allocation (medical supplies, water, shelter, hazard reports)
over an ad-hoc, intermittently-connected peer-to-peer radio mesh.

Nodes hold an OR-Set CRDT of Last-Writer-Wins resource records. When two
devices come into (simulated) BLE range, they run a digest-based
anti-entropy sync — comparing Merkle-style hashes first, then exchanging
only the records that actually diverge — so replicas converge without ever
needing a central server or a full data dump between peers.

## Layout

```
src/
  types/index.ts     Shared data shapes (ResourceRecord, StateDigest, ...)
  utils/hash.ts       SHA-256 hashing + canonical (sorted-key) JSON
  utils/logger.ts     Console logging + ASCII table printer
  utils/async.ts      sleep() helper
  crdt/hlc.ts         Hybrid Logical Clock (per-record timestamps)
  crdt/vclock.ts      Vector Clock (per-replica causal history)
  crdt/orset.ts       The OR-Set/LWW CRDT itself + digest diffing
  network/node.ts     MeshNode — a single device's identity + local writes
  network/mesh.ts     VirtualBLEMeshNetwork — simulated radio + gossip sync
  index.ts            Simulation harness: partition -> sync -> converge
```

## Running it

```bash
npm install
npm start          # runs the scenario via tsx, no build step needed
```

Other scripts:

```bash
npm run typecheck  # tsc --noEmit, strict mode
npm run build       # emit compiled JS to dist/
npm run run:build   # run the compiled output with plain node
```

## What the scenario proves

Three nodes — Base Camp, a Field Medic, and a Courier who physically
relays state between them — write independently while partitioned, sync
opportunistically as they come into range, and are asserted at the end to
have converged to an identical resolved state hash with zero data loss,
despite simulated packet loss and latency jitter throughout.
