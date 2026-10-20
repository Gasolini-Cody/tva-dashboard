# TVA — MISS MINUTES INVESTMENT AGENT
## 3-års autonom investeringsagent (2026 → utgangen av 2029)

Time Variance Authority-stil: agenten **Miss Minutes** overvåker "Sacred Timeline"
(markedet), identifiserer DCA-vinduer og varsel-phaser, og presenterer alt i et
TVA-dashboard. Fallout-GUI er fjernet.

> **VIKTIG:** Kjører i **PAPER-mode**. Ingen ekte penger berøres.

### Arkitektur

```
data.py      -> priser fra CoinGecko (BTC, ETH, SOL, SUI + NFT-aktiver IMX, APE)
signals.py   -> syklusanalyse, risiko, svindelfilter per asset
executor.py  -> regelbasert: DCA, rebalansering, gevinstrealisering (papir)
notify.py    -> Miss Minutes-varsler til framnes@proton.me (outbox/SMTP)
backtest.py  -> strategi vs "DCA kun BTC" og "hold BTC" på historiske data
dashboard.py -> TVA/Miss Minutes-dashboard (kun localhost:8777)
               - /history.json: 1000 dager pris-historikk per asset (Binance)
               - Log-skala chart med av/på per asset + tidsområde (3M/6M/1Å/MAX)
pipeline.py  -> full syklus: hent -> analyser -> varsle -> utfør -> logg
```

### Bruk

```bash
cd vault-agent
python3 pipeline.py     # én full syklus (cron: f.eks. ukentlig)
python3 backtest.py     # 36-måneders backtest med benchmarks
python3 dashboard.py    # -> http://127.0.0.1:8777
```

### Varsler (Miss Minutes)

Genereres automatisk i pipeline når:
- **DCA-signal**: marked i akkumulerings-/nedgangsfase med score ≤ 60 → gunstig DCA-vindu
- **Euforifase**: vurder gevinstrealisering

Uten SMTP-oppsett lagres varslene som `.eml` i `outbox/`. For ekte e-post til
framnes@proton.me via Proton Mail Bridge:

```bash
export VAULT_SMTP_HOST=127.0.0.1
export VAULT_SMTP_PORT=587
export VAULT_SMTP_USER='din_proton_bruker'
export VAULT_SMTP_PASSWORD='din_bridge_kode'   # aldri i config/kode
python3 pipeline.py
```

### Portefølje (config.json)

| Asset | Vekt | Kategori |
|---|---|---|
| Bitcoin | 35 % | store-of-value |
| Ethereum | 25 % | smart-contract |
| Solana | 15 % | smart-contract |
| **Sui** | 15 % | smart-contract |
| **Immutable (IMX)** | 5 % | NFT |
| **ApeCoin (APE)** | 5 % | NFT |

### Backtest-Notater

- Reeltresultat (live CoinGecko-data, 1080 daglige punkter 2023-11 → 2026-10):
  strategi **+1,2 %** | DCA kun BTC **−18,9 %** | hold BTC **+3,3 %**
- Strategien slo DCA-BTC klart; hold-BTC var jevnbygd med diversifiseringen.
- Gratis-API-et er ratebegrenset (≈1 kall/15 s); modulen venter automatisk og
  faller tilbake til syntetisk data hvis grensen nås (merkes med `source`).

### Ansvarsfraskrivelse

TVA garanterer ikke avkastning — "for all time, always" gjelder bare disiplinen.
Aldri invester mer enn du har råd til å tape.
