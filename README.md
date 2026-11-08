# Power Automate → GitHub Actions: nasazení na žádost

Školní projekt do předmětu KPV/OPP (Optimalizace podnikových procesů, ZČU).
Téma: *Optimalizace procesu žádosti a schvalování nasazení aplikace pomocí
Power Automate a GitHub Actions.*

## Jak to funguje

1. Flow v Power Automate pošle přes GitHub konektor událost
   `repository_dispatch` s typem `deploy` a daty o žádosti.
2. Workflow [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml)
   zkontroluje verzi a prostředí, zapíše nasazení do
   [`deployments/log.csv`](deployments/log.csv) a publikuje složku `site/`
   na GitHub Pages.
3. Stránka ukazuje nasazenou verzi, dobu od žádosti po nasazení a historii.

## Flow v Power Automate

| Flow | Nástroje | Průběh |
|---|---|---|
| 1 – ruční nasazení | GitHub | ruční spuštění → zadání verze a prostředí → kontrola → dispatch `deploy` |
| 2 – žádost se schválením | Forms, Approvals, GitHub, Outlook | žádost ve formuláři → schválení → dispatch `deploy` → potvrzení e-mailem; při zamítnutí důvod |

## Data události (`client_payload`)

```json
{
  "version": "1.2.0",
  "environment": "test",
  "requested_by": "jmeno@students.zcu.cz",
  "requested_at": "2026-11-08T14:05:00Z",
  "approved_by": "",
  "request_id": "id běhu flow",
  "flow": "flow1-rucni"
}
```

`version` (X.Y.Z) a `environment` (`test` / `produkce`) jsou povinné,
jinak workflow skončí chybou.

## Nastavení repozitáře

- Settings → Pages → Source: **GitHub Actions**.
- Nasazovanou aplikací bude simulátor vytápění z předmětu KKY/AKS; zatím je
  ve `site/` jen stavová stránka.
