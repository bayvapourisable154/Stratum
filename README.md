# Stratum: CPU-Native Proof of Work

A revolutionary proof-of-work algorithm designed to restore the viability of CPU mining and make commodity hardware competitively viable against application-specific integrated circuits (ASICs).

## What is Stratum?

Stratum is a CPU-native proof-of-work algorithm that inverts the conventional relationship between general-purpose CPUs and specialized mining hardware. While SHA-256 and similar algorithms are optimized for simple bitwise operations (creating a ~100,000× efficiency advantage for ASICs), Stratum makes the CPU's internal overhead—cache hierarchies, speculative execution, and branch prediction—the *foundation* of the computation.

An ASIC attempting to replicate Stratum's behavior would, by definition, become a CPU. This creates structural parity between commodity hardware and custom silicon, making CPU mining economically viable once again.

## How It Works

### Problem: ASIC Dominance

Traditional proof-of-work algorithms like SHA-256 strip a CPU down to its core operations—bitwise functions and modular arithmetic. Every other CPU component (caches, branch prediction, speculative execution) becomes wasted power overhead. ASICs eliminate this waste, achieving efficiency ratios as high as **100,000×** over CPUs.

### Solution: Make Overhead the Computation

Stratum inverts this by designing a hash function where CPU overhead structures are load-bearing to the algorithm itself. The hash cannot be computed efficiently without:

1. **Multi-level cache hierarchy** (L1, L2, L3)
2. **Branch prediction and speculative execution**
3. **Out-of-order execution**
4. **Correct cache behavior** across memory access sequences

This creates a 47× efficiency ratio between CPUs and hypothetical ASICs—the first algorithm where commodity hardware is structurally competitive.

## Technical Architecture

### Four Core Solutions

#### 1. **ZK Proof of Cache Execution (Verification Asymmetry)**
- Miners generate a STARK proof alongside each hash output
- The proof cryptographically verifies the cache access sequence was executed correctly
- Full nodes verify the proof in **O(log n) time** (~50ms), without re-running the computation
- No trusted setup required (STARKs are transparent)
- Proof size: ~120 KB per submission

**Trade-off**: Proof generation adds ~2.1 seconds per hash attempt on a 16-core CPU (parallelizable across cores).

#### 2. **Software-Emulated Cache Model (Determinism Guarantee)**
- Rather than relying on variable physical cache behavior (thermal throttling, DRAM timing, OS scheduling), Stratum defines a canonical software cache model
- Fixed geometry: L1 (256 KB direct-mapped), L2 (1 MB 4-way), L3 (12 MB 16-way)
- LRU eviction policy; access latency defined in instruction counts, not wall-clock time
- Guarantees **identical output across all hardware** that correctly implements the model
- Any language runtime can faithfully execute the reference implementation
- Performance overhead: 18–22% vs. native execution on matching hardware

#### 3. **ISA Profile System (Portability)**
- Stratum is specified as an algorithm plus ISA profiles
- v1 targets four architectures:
  - **x86-64**: AVX-512, AES-NI, CLMUL (100% baseline performance)
  - **ARM64**: SVE2, SHA3 extensions, PMULL (97% of baseline)
  - **RISC-V**: V extension, Zknh, Zbc (91% of baseline)
  - **Scalar fallback**: Software emulation (68% of baseline, all platforms)
- Network accepts any profile that produces valid hashes against the canonical model
- No hardware is excluded from network participation

#### 4. **Speculative Trace Commitment (Execution Model) — v1.5**
- Miners execute a bounded 32-instruction speculative window at branch points
- Execution trace (branches taken, rollbacks) is logged and committed to the hash input
- Verifiers replay branch points using a deterministic branch prediction emulator
- Creates the strongest known form of ASIC resistance: implementing speculative execution with correct rollback is the majority cost of building a modern CPU core
- Scheduled for v1.5 (12–18 months after mainnet launch); v1 is complete ASIC-resistant without it

## Performance Benchmarks

### Hash Rate by Hardware Tier

| Hardware | Cores | KH/s | Power (W) | H/J | Monthly Yield* |
|----------|-------|------|-----------|-----|----------------|
| Entry laptop (i5-12500H) | 12 | 8.4 | 28 | 300 | 0.18–0.42 STR |
| Gaming desktop (i7-13700K) | 16 | 18.2 | 65 | 280 | 0.39–0.91 STR |
| Workstation (Ryzen 9 7950X) | 32 | 34.6 | 105 | 330 | 0.74–1.73 STR |
| Dual Xeon server (2× Gold 6342) | 48 | 71.8 | 350 | 205 | 1.54–3.60 STR |
| 4-node CPU cluster | 128 | 138.4 | 560 | 247 | 2.97–6.93 STR |
| Hypothetical ASIC attempt | — | 1.9 | 90 | 21 | 0.04–0.09 STR |

*Assumes 1M-node network, 10-minute block time, 6.25 STR reward. KH/s includes ZK proof generation overhead.

### Key Efficiency Metric

An entry-level laptop and a hypothetical ASIC draw **identical wattage** (28W) but the laptop produces **47× more valid hashes per joule**.

## Implementation Roadmap

- **v1.0 (0–6 months)**: Core protocol, software cache model, ZK-STARK verification, x86-64 and ARM64 ISA profiles, testnet launch
- **v1.1 (6–9 months)**: RISC-V ISA profile, scalar fallback, ISA profile reporting in block headers
- **v1.2 (9–12 months)**: Security audit, proof generation optimization, mainnet genesis
- **v1.5 (18–24 months)**: Speculative trace commitment, branch prediction emulator, maximum ASIC resistance

## Why This Matters

1. **Restores CPU Mining Viability**: Makes commodity hardware economically competitive for the first time since 2015
2. **Increases Network Decentralization**: Lowers barriers to participation; anyone with a CPU can mine
3. **Energy Efficiency**: 47× better hash-per-joule efficiency than SHA-256 ASICs
4. **Hardware Diversity**: Four ISA profiles ensure no platform is excluded
5. **Transparent Verification**: STARK proofs require no trusted setup; fully decentralized verification

## Naming

Stratum is named for geological strata—distinct layers formed under pressure, each with different properties, each necessary to the whole. The CPU's cache hierarchy is its stratum. The algorithm that requires it is Stratum.

---

**License**: Creative Commons Attribution 4.0 International (CC BY 4.0)

**Document**: Final Specification, March 2026

For full technical details, see `stratum_final.pdf`
