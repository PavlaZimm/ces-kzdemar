# Cesťák ZDEMAR

Interní webová aplikace pro vyúčtování pracovních cest. Uživatel vybere řidiče,
zadá datum a celkový počet kilometrů (tam i zpět) a aplikace najde vhodnou trasu
nebo kombinaci tras a připraví hotový text vyúčtování ke zkopírování.

## Jak to funguje

Aplikace je čistě statická – jeden soubor `index.html`, který obsahuje HTML, CSS
i JavaScript včetně dat (seznam řidičů a seznam tras). Nepotřebuje žádný backend,
databázi ani API klíče a všechny výpočty běží v prohlížeči.

Vyhledávání tras probíhá ve čtyřech krocích:

1. **Jednodenní trasa** – hledá trasu, jejíž kilometry padnou do tolerance.
2. **Dvoudenní kombinace** – dvojice tras, jejichž součet kilometrů odpovídá.
3. **Greedy algoritmus** – pro vyšší nájezdy skládá delší kombinace (3+ dny).
4. **Nejbližší alternativy** – pokud nic nesedí, nabídne 5 nejbližších tras.

Tolerance se počítá jako 5 % ze zadaných kilometrů, minimálně 20 km a maximálně
150 km. Do výsledků se dostanou pouze trasy, jejichž celkový čas (tam + 30 minut
jednání + zpět) nepřesáhne 4 hodiny 59 minut.

## Lokální spuštění

Stačí otevřít `index.html` v prohlížeči. Případně přes lokální server:

```bash
python3 -m http.server 8000
# http://localhost:8000
```

Kopírování do schránky (`navigator.clipboard`) funguje jen v zabezpečeném
kontextu – tedy na `https://` nebo na `localhost`.

## Nasazení na Vercel

Projekt je připravený pro nasazení bez jakékoli konfigurace buildu:

1. Ve Vercelu zvolte **Add New → Project** a naimportujte tento repozitář.
2. **Framework Preset** nechte na `Other`, Build Command i Output Directory
   nechte prázdné – Vercel naservíruje `index.html` z kořene repozitáře.
3. **Deploy**.

Každý push do `main` pak vytvoří produkční nasazení, push do ostatních větví
náhledové (preview) nasazení.

Alternativně přes Vercel CLI:

```bash
npx vercel        # náhledové nasazení
npx vercel --prod # produkční nasazení
```

## Struktura projektu

```
.
├── index.html    # celá aplikace (HTML + CSS + JS + data)
├── vercel.json   # konfigurace nasazení a bezpečnostní hlavičky
└── README.md
```

## Úprava dat

Seznamy řidičů a tras jsou v `index.html` v poli `drivers` a `trasy` na začátku
bloku `<script>`.

```js
{ firma: "Zdemar Czech s. r. o.", jmeno: "Jméno Příjmení", vozidlo: "AUS", spz: "1AB 2345" }
{ kam: "Teplice", km: 20, casTam: "0:15", popis: "Název firmy" }
```

U tras je `km` vzdálenost tam i zpět a `casTam` čas jedné cesty ve formátu `h:mm`.

## Poznámka k datům

Aplikace obsahuje jména zaměstnanců a SPZ vozidel. Je proto označená jako
`noindex` a nasazení by mělo být chráněné – ve Vercelu doporučujeme zapnout
**Deployment Protection** (Settings → Deployment Protection), aby byl web
přístupný jen přihlášeným členům týmu.
