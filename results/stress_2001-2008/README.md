# 🧪 Stress Results (2001–2008)

Stress runs are for **illustration only** on degraded feed quality and are **excluded** from headline metrics.

We publish **only**:
- **Annual summary CSV** (minimal aggregates like PnL%, MaxDD%).
- **Annual SVG equity** (year curve).

> ⚠️ **Not published for stress:**  
> • detailed per‑trade reports (timestamps/prices),  
> • minute/bar‑level equity and drawdown series,  
> • **all extended analytics** and risk metrics (Sharpe, Sortino, MAR, etc.),  
> because the stress period is excluded from headline under the **Data Quality Policy** (see `data_quality_policy/`).

---

## 🎚️ Track scope for stress
Stress results are provided **only for the 1.5% risk track with a fixed starting capital of 100,000 USD per year**.  
This is purely illustrative and not part of headline evaluation.

---

## 🔗 Inputs binding
- Canonical: Data Hub → `INPUTS-PROVENANCE.md` (RAW/PREPARED/Calendars linkage).  
- Pin in this repo: `INPUTS-PIN.md` (which input archives are used).  
- Mirrored SHA‑256/GPG/OTS for input archives: `integrity/inputs/`.

---

## 🔐 Integrity checks
- Each year ships `artifacts_YYYY.sha256` (manifest of inputs/outputs for that year).
- How to verify SHA‑256 / GPG / OTS and how to handle **absolute paths** inside manifests — see the root `INTEGRITY.md`.

### Quick (per‑year manifest)
**Linux**
```bash
sha256sum -c results/stress/1p50/fixed_start_100k/<YYYY>/artifacts_YYYY.sha256
```

**macOS**
```bash
shasum -a 256 -c results/stress/1p50/fixed_start_100k/<YYYY>/artifacts_YYYY.sha256
```

**Windows PowerShell**
```powershell
Get-ChildItem "results\stress\1p50\fixed_start_100k\<YYYY>" -Filter "artifacts_*.sha256" | ForEach-Object {
  $m = $_.FullName
  $lines = Get-Content $m
  foreach ($line in $lines) {
    if ($line -match '^([0-9a-f]{64})\s{2}(.+)$') {
      $expected = $Matches[1].ToLower()
      $file = Join-Path (Split-Path $m) $Matches[2]
      $actual = (Get-FileHash $file -Algorithm SHA256).Hash.ToLower()
      if ($actual -ne $expected) { "MISMATCH: $file`n  manifest=$expected`n  actual  =$actual" }
    }
  }
}
```

---

## 📦 Super‑manifest of yearly manifests (optional convenience)

For stress runs we also provide a **super‑manifest** — a hash list of the per‑year manifest files — e.g.:  
`integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256`  
Each line has the format:  
```
<sha256>␠␠artifacts_YYYY.sha256
```

### Verify the super‑manifest

**Linux / macOS**
```bash
# Recompute SHA‑256 of all yearly manifests (order‑stable), then strip paths to basenames:
REPO=/path/to/repo
cd "$REPO"

# 1) Collect and hash
find results/stress_2001-2008/1p50/fixed_start_100k -type f -name 'artifacts_*.sha256' -print0   | sort -z   | xargs -0 shasum -a 256 > /tmp/recalc_full.txt

# 2) Keep "<hash>␠␠<basename>"
awk '{print $1"  "$2}' /tmp/recalc_full.txt | awk '{cmd="basename ""$2"""; cmd|getline b; close(cmd); print $1"  "b}' > /tmp/recalc_base.txt

# 3) Compare with the stored super‑manifest
diff -u integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256 /tmp/recalc_base.txt   && echo "OK: super‑manifest matches" || echo "BAD: differences found"
```

**Windows PowerShell**
```powershell
$repo = "C:\path\to\repo"
$super = Join-Path $repo "integrity\results\stress_2001-2008\fixed_start_100k_manifest\artifacts_2001-2008.sha256"
$expect = Get-Content $super

$errors = @()
Get-ChildItem "$repo\results\stress_2001-2008\1p50\fixed_start_100k" -Recurse -Filter "artifacts_*.sha256" |
  Sort-Object FullName | ForEach-Object {
    $h = (Get-FileHash $_.FullName -Algorithm SHA256).Hash.ToLower()
    $line = "$h  $($_.Name)"
    if ($expect -notcontains $line) { $errors += "MISMATCH: $($_.Name)" }
  }

if ($errors.Count -eq 0) { "OK: super-manifest matches" } else { $errors -join "`n" }
```

> If your manifests contain **absolute paths**, this does **not** affect hash correctness; the super‑manifest only hashes the `artifacts_YYYY.sha256` files themselves.

---

## 🔗 Links
- Canonical input binding (Data Hub): `INPUTS-PROVENANCE.md` / `INPUTS-PROVENANCE.ru.md`
- Integrity guide (this repo): `INTEGRITY.md` (note on absolute paths in manifests)
- Yearly manifests: `results/**/artifacts_YYYY.sha256` (+ `.asc`, `.ots` for headline tracks when applicable)

---

## 📝 Notes
- This **Stress** section is **demonstrative** and does not inform headline evaluation.
- The **INPUTS-PIN.md** bridges this repo with the Data Hub; canonical input proofs live in the Data Hub, mirrored here for convenience.
- Yearly manifests already enumerate the exact input files (prepared minutes, calendars) used by each run.
