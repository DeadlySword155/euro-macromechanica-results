# 🛡️ Strategy Proofs & Public‑Safe Mechanics  
*(this folder contains integrity proofs for a proprietary archive; the strategy itself is not published)*

---

## What this folder includes
- **Purpose:** proofs of existence and integrity for a closed strategy archive.  
- **Files:** `*.sha256` (hash manifests), `*.asc` (GPG signatures), `*.ots` (OpenTimestamps).  
- **Important:** the strategy code/parameters are **not** published here.

### Keys
- **Public key:** `keys/emm_pub_key.asc` (ASCII‑armored)  
- **GPG fingerprint:** `4E53 DA03 D1A1 644E 6479  3C47 EEEC 0AFA 4D8A F0A7`

---

## ✅ Quick check

### Linux / macOS (bash/zsh)
```bash

# 2) Verify the manifest signature
gpg --verify artifacts.sha256.asc artifacts.sha256

# 3) Verify all files listed in the manifest
# (run from the folder that contains the listed files)
sha256sum -c artifacts.sha256      # macOS: shasum -a 256 -c artifacts.sha256

# 4) Verify the time anchor (Bitcoin)
ots verify artifacts.sha256.ots artifacts.sha256
# optionally upgrade the .ots to a confirmed Bitcoin block:
# ots upgrade artifacts.sha256.ots
```

### Windows PowerShell
```powershell
gpg --import keys\emm_pub_key.asc
gpg --verify artifacts.sha256.asc artifacts.sha256

# Verify a single file against a known SHA-256
$h="<64-hex>".ToLower(); $f="file.ext"
$act=(Get-FileHash $f -Algorithm SHA256).Hash.ToLower()
if ($act -eq $h) {"OK"} else {"MISMATCH: $f`n  expected=$h`n  actual  =$act"}
```

> **Manifest format.** Lines look like `HEX␠␠filename` (exactly **two spaces**).  
> **No paths:** manifests may list **filenames only** — just run verification from the correct folder.  
> **Absolute paths:** manifests with absolute paths are also fine; hashes **remain valid**. See `INTEGRITY.md` for path‑aware verification.

---

## ⚙️ Entry/Exit Logic (M5 with M1 micro‑confirmation) — public summary

**Idea.** For each M5 bar the engine deterministically computes direction‑specific **service levels**:
- **Trigger** — initiates entry;  
- **Guard** — protective stop;  
- **Objective** — target.

**Entries are micro‑confirmed** on M1 within the current M5 bar’s window.

**Per‑bar sequence**
1) **Window:** all M1 minutes inside the current M5 bar.  
2) **Pre‑filters:** session blocks; news pre‑blocks and event‑hour rules; data‑hygiene heuristics (thin bars, post‑gap warm‑up); temporary post‑anomaly blocks.  
   - **Data‑quality caveat:** when a year exhibits **many 5–10‑minute gaps**, the reliability of **Trigger/Guard/Objective** computations degrades; such years are **excluded from headline** per the Data Quality Policy. **Longer gaps** (>10 min) also matter, but are **far less material overall** for an M5 engine than frequent 5–10‑minute gaps.  
3) **Readiness:** both **Guard** and **Objective** must be present; otherwise no entry.  
4) **Entry trigger (via M1):** a touch of **Trigger** activates the entry; if multiple triggers are eligible, pick the most stringent per the internal order.  
5) **Rate limiters:** optional one‑trade‑per‑bar; cooldown; **reset** rule — if the baseline reference is reclaimed within the bar, no same‑direction re‑entry until the bar closes.  
6) **Position management:**  
   **SL:** fixed at **Guard** (no trailing);  
   **TP:** at **Objective**, recomputed each new M5 bar with a small execution allowance. Fills are checked on M1; if SL and TP collide within the same minute, **SL takes precedence**.  
7) **Force exit:** **only at the very end of the dataset** — if data ends and a position is still open, it is closed on the **last available minute**. No other forced exits are used.

### ⛓️ Gaps & exits (concise)
- **Positions are not auto‑closed across gaps.** Over weekends/discontinuities the trade remains open and continues on the next available bar.  
- **Exit evaluation on M1** inside the next M5 bar:  
  - **LONG:** TP at `high ≥ Objective`, SL at `low ≤ Guard`;  
  - **SHORT:** mirror (TP at `low ≤ Objective`, SL at `high ≥ Guard`).  
- **Fill price** is the **exact computed level** (Objective/Guard, with allowance); the **timestamp** is the first M1 minute where reachability is met. M1 high/low are used for **detection**, not as the trade price.  
- **Same‑minute conflict:** if both target and stop conditions are met, **SL takes precedence**.  
- The **“anomalous‑gap” detector** only **blocks new entries** for a configured period; it **does not** close an open position.

### 💼 Sizing, accounting & public cost model
- **Position size** = a fraction of equity at entry; risk derives from the distance to **Guard**.  
- Trades store `pnl_abs` and `pnl_pct`; equity is built at **exit** timestamps.  
- **Cost model:** the **1‑pip early exit** is an **allowance for spread/slippage/commission**; reporting may present it as a symmetric **“0.5/0.5 split”** across entry/exit.

> This is a public summary. **Numeric thresholds, exact formulas, and time windows are intentionally omitted.** Full parameterization, test cases, and a parity protocol are provided under the **NDA Audit Kit**.  
> Headline/stress inclusion is governed by the **Data Quality Policy** (primary gate: frequency of **5–10‑minute** gaps).

---

## 📜 Licensing & access
- **This folder:** *CC BY‑NC 4.0* (integrity artifacts & documentation).  
- **Per‑trade CSVs / equity dynamics with trades / execution diagnostics:** **Not public** — available on request. See `COMMERCIAL.md` for terms (NDA Audit Kit, Research/Commercial License). 
- **Strategy code:** Proprietary / All Rights Reserved. Not included.
See [`LICENSE-STRATEGY-CODE.txt`](https://github.com/rleydev/euro-macromechanica-results/tree/main/LICENSE-STRATEGY-CODE.txt) and [`COMMERCIAL.md`](https://github.com/rleydev/euro-macromechanica-results/tree/main/commercial.md).

**Contact:** GitHub **@rleydev (thelaziestcat)** · email **thelaziestcat@proton.me** / **thelazyazzcat@gmail.com**
