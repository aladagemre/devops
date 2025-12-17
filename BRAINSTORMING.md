Harika — bunu iki taraftan aynı anda yapacağız:

1. **Renovate PR gürültüsünü azaltma** (daha az PR, daha anlamlı PR)
2. **CI’ı hızlandırma** (daha az koşsun, koşunca daha hızlı bitsin)

Aşağıdaki ayarlar senin “npm + Next.js + çok repo + private” senaryosuna göre.

---

## 1) Renovate PR gürültüsünü azalt

### A) PR sayısını limit + tek güne sıkıştır

`devops/renovate/base.json` içine ekle:

```json
{
  "extends": ["config:recommended"],
  "timezone": "Europe/Istanbul",
  "labels": ["deps"],
  "rangeStrategy": "bump",

  "schedule": ["every weekend"],
  "prHourlyLimit": 2,
  "prConcurrentLimit": 5,

  "dependencyDashboard": true,
  "rebaseWhen": "behind-base-branch"
}
```

Ne sağlar?

* Haftaiçi PR yağmuru olmaz, **hafta sonu batch** gelir.
* Aynı anda açık PR sayısı sınırlanır.

### B) PR’ları paket gruplarına topla

`devops/renovate/nextjs.json`’ı şöyle güncelle:

```json
{
  "extends": ["github>emre/devops//renovate/base.json"],

  "packageRules": [
    {
      "matchUpdateTypes": ["patch", "minor"],
      "automerge": true,
      "requiredStatusChecks": ["ci"],
      "groupName": "patch-minor"
    },
    {
      "matchUpdateTypes": ["major"],
      "automerge": false
    },

    { "matchPackagePatterns": ["^eslint", "^prettier"], "groupName": "linting" },
    { "matchPackagePatterns": ["^@types/"], "groupName": "types" },
    { "matchPackagePatterns": ["^@testing-library/", "^jest", "^vitest", "^playwright"], "groupName": "tests" }
  ]
}
```

Ne sağlar?

* 15 ayrı PR yerine **3–5 anlamlı PR**.

### C) “Stability days” ile en yeni versiyonun oturmasını beklet

(Çok sürpriz kırılımı azaltır)

```json
{
  "stabilityDays": 3
}
```

Bunu `base.json`a koyabilirsin.

---

## 2) CI’ı hızlandır (ve gereksiz koşmayı azalt)

### A) Workflow’u “paths-ignore” ile gereksiz tetiklemelerden kurtar

Her repo’daki `.github/workflows/ci.yml` (çağıran) dosyana şunu ekle:

```yaml
on:
  pull_request:
    paths-ignore:
      - "**/*.md"
      - "docs/**"
  push:
    branches: [main]
    paths-ignore:
      - "**/*.md"
      - "docs/**"
```

Ne sağlar?

* README/docs değişince CI boşa çalışmaz.

### B) Concurrency ile “eski CI’ı iptal et”

Çok hız kazandırır:

```yaml
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
```

Bunu çağıran workflow’a da koyabilirsin.

### C) Reusable workflow içinde cache’leri gerçekten kullan

Şu an `setup-node cache: npm` iyi ama Next.js build cache ekleyince ciddi hızlanır.

`devops/.github/workflows/nextjs-ci.yml` içine `actions/cache` ekle:

```yaml
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node_version }}
          cache: 'npm'

      - name: Cache Next.js
        uses: actions/cache@v4
        with:
          path: |
            .next/cache
          key: nextjs-${{ runner.os }}-${{ hashFiles('package-lock.json') }}-${{ hashFiles('**/*.ts', '**/*.tsx', '**/*.js', '**/*.jsx') }}
          restore-keys: |
            nextjs-${{ runner.os }}-${{ hashFiles('package-lock.json') }}-
```

Ne sağlar?

* Özellikle `next build` (ve bazen test) **bariz hızlanır**.

### D) Install’ı hızlandır: `npm ci` + küçük dokunuş

Install step:

```yaml
      - name: Install
        run: npm ci --prefer-offline --no-audit
```

Ne sağlar?

* CI’da audit yapma → hız (audit’i ayrı güvenlik katmanına bırakıyoruz)

### E) Test yoksa test step’i koşmasın

Her projede test yoksa CI gereksiz patlıyor. “Script varsa koş” mantığı:

```yaml
      - name: Test (if exists)
        run: npm run -s test --if-present
```

Aynısını typecheck için de yapabilirsin:

```yaml
      - name: Typecheck (if exists)
        run: npm run -s typecheck --if-present
```

---

## Bonus: Netlify preview build gürültüsünü kesmek (istersen)

Renovate PR’ları Netlify preview build açıyorsa, build sayısı şişer. Bunu kesmenin en temiz yolu Netlify “Ignore builds” kuralı.

Repo’daki `netlify.toml` içine:

```toml
[build]
  ignore = "bash -lc 'if [ \"$HEAD\" = \"refs/heads/renovate\" ] || echo \"$BRANCH\" | grep -q \"^renovate/\"; then exit 0; else exit 1; fi'"
```

(İstersen bunu daha güvenli/okunaklı hale getiririz; Netlify env’lerine göre ufak farklar olabiliyor.)

---

## Sana uygulanabilir “hemen şimdi” checklist

1. `devops/renovate/base.json` → schedule + PR limit + dashboard + stabilityDays
2. `devops/renovate/nextjs.json` → grouping + patch/minor automerge
3. `devops/.github/workflows/nextjs-ci.yml` → `.next/cache` + `--prefer-offline --no-audit` + `--if-present`
4. Her repo’daki çağıran `ci.yml` → `paths-ignore` + `concurrency`

İstersen, kullandığın CI check adı gerçekten `ci` mi? (Workflow name ve job name bazen farklı görünüyor.) Değilse `requiredStatusChecks`’i ona göre ayarlayalım; yoksa automerge hiç çalışmaz.

