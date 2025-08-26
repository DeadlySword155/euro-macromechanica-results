# 🧪 Stress Results (2001–2008)

Стресс-прогоны предназначены **только** для иллюстрации поведения на деградированном фиде и **не входят** в headline-метрики.  
**Выполнены только для трека 1p50 (risk 1.5% на сделку) с фиксированным стартовым капиталом _100 000 USD на каждый год_.**

Публично выкладываются **только**:
- **годовые summary CSV** (минимальные агрегаты: PnL%, MaxDD% и т.п.);
- **SVG equity** (годовая кривая).
- **artifacts_YYYY.sha256** (годовой манифест)

> ⚠️ **Не публикуется для stress:**  
> • детальные per-trade отчёты (метки времени/цены),  
> • поминутные/побарные ряды equity и просадки,  
> • **весь расширенный анализ** и риск-метрики (Sharpe, Sortino, MAR и пр.),  
> поскольку stress-период исключён из headline согласно **политике качества данных** (см. `data_quality_policy/`).

---

## 🔗 Привязка входов (inputs binding)
- Канон: Data Hub → `INPUTS-PROVENANCE.md` (связка RAW/PREPARED/Calendars). Ру перевод –  `INPUTS-PROVENANCE.ru.md`
- Пин в этом репо: `INPUTS-PIN.ru.md` (какие архивы входов использованы).  
- Зеркала SHA-256/GPG/OTS входных архивов: `integrity/inputs/`.

---

## 🔐 Интегрит-проверки

- У каждого года лежит `artifacts_YYYY.sha256` (манифест входов/выходов).
- Дополнительно есть **манифест «списка манифестов»** (roll-up) для 2001–2008:  
  `integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256`  
  В нём перечислены пары `SHA256  путь/к/годовому/artifacts_YYYY.sha256`.  
  Вы можете сверить каждый годовой манифест с этим списком.
- Как проверять SHA-256 / GPG / OTS и как работать с **абсолютными путями** в манифестах — см. корневой `INTEGRITY.md`.

### Быстро (Linux)
```bash
# 1) Проверить годовой манифест
sha256sum -c results/stress/1p50/fixed_start_100k/YYYY/artifacts_YYYY.sha256

# 2) Сверить «манифест манифестов» (roll-up) со стоящими на диске файлами
cut -d' ' -f3 integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256  | xargs -I{} sha256sum "{}"  | awk '{print $1"  "$2}' | sort -k2 > /tmp/recalc_rollup.txt

sort -k2 integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256 > /tmp/rollup_ref.txt

diff -u /tmp/rollup_ref.txt /tmp/recalc_rollup.txt && echo "OK: все годовые манифесты совпали" || echo "BAD: есть расхождения"
```

### Быстро (macOS)
```bash
# 1) Проверить годовой манифест
shasum -a 256 -c results/stress/1p50/fixed_start_100k/YYYY/artifacts_YYYY.sha256

# 2) Сверить roll-up файл со стоящими на диске годовыми манифестами
awk '{print $2}' integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256  | xargs -I{} shasum -a 256 "{}"  | awk '{print $1"  "$2}' | sort -k2 > /tmp/recalc_rollup.txt

sort -k2 integrity/results/stress_2001-2008/fixed_start_100k_manifest/artifacts_2001-2008.sha256 > /tmp/rollup_ref.txt

diff -u /tmp/rollup_ref.txt /tmp/recalc_rollup.txt && echo "OK: все годовые манифесты совпали" || echo "BAD: есть расхождения"
```

### Быстро (Windows PowerShell)
```powershell
# 1) Проверить годовой манифест: пересчитать хэши всех файлов из artifacts_YYYY.sha256 и сравнить
$manifest = Get-Content "results\stress\1p50\fixed_start_100k\YYYY\artifacts_YYYY.sha256"
$issues = @()
foreach ($line in $manifest) {
  if ($line -match '^\s*$') { continue }
  $parts = $line -split '\s{2}',2
  $ref = $parts[0].ToLower()
  $path = $parts[1]
  if (-not (Test-Path $path)) { $issues += "MISSING: $path"; continue }
  $act = (Get-FileHash $path -Algorithm SHA256).Hash.ToLower()
  if ($act -ne $ref) { $issues += "MISMATCH: $path`n  manifest=$ref`n  actual  =$act" }
}
if ($issues.Count -eq 0) { "OK: годовой манифест совпал" } else { $issues -join "`n" }

# 2) Сверить roll-up: artifacts_2001-2008.sha256 перечисляет хэши файлов artifacts_YYYY.sha256
$roll = Get-Content "integrity\results\stress_2001-2008\fixed_start_100k_manifest\artifacts_2001-2008.sha256"
$issues = @()
foreach ($line in $roll) {
  if ($line -match '^\s*$') { continue }
  $parts = $line -split '\s{2}',2
  $ref = $parts[0].ToLower()
  $path = $parts[1]
  if (-not (Test-Path $path)) { $issues += "MISSING: $path"; continue }
  $act = (Get-FileHash $path -Algorithm SHA256).Hash.ToLower()
  if ($act -ne $ref) { $issues += "MISMATCH: $path`n  rollup =$ref`n  actual =$act" }
}
if ($issues.Count -eq 0) { "OK: roll-up совпал" } else { $issues -join "`n" }
```

> Примечание: «манифест манифестов» (roll-up) — это **обычный список** строк вида  
> `SHA256␠␠relative/path/to/artifacts_YYYY.sha256`. Его **подписывают GPG** и **якорят OTS** как первичный список.

---
