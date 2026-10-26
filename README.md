# Kurník Profi — redesign webu a konfigurátoru (2026)

Prémiový frontendový redesign webu [kurnik-profi.cz](https://kurnik-profi.cz/) v čistém **HTML5, CSS3 a Vanilla JS** — bez frameworku a bez build systému.

> **Důležité:** Jde o vizuální redesign připravený k napojení, **ne o náhradu běžícího systému**. Původní web je vlastní aplikace **ASP.NET Blazor Server** (cena konfigurátoru, účty, členství, recenze a platby Comgate běží na serveru). Tento projekt nic z toho nemaže ani nepřepisuje a nic se nenasazuje do produkce.

---

## 1. Spuštění

Stačí otevřít `index.html` v prohlížeči, nebo spustit jednoduchý server (doporučeno kvůli kopírování odkazů a `fetch`):

```bash
cd kurnik-profi-redesign
python3 -m http.server 8080      # pak http://localhost:8080
```

Nasazení na běžný hosting: nahrát celou složku. Vše je statické; `404.html` nastavte jako chybovou stránku (např. `ErrorDocument 404 /404.html`).

Fotky se zatím načítají **přímo z kurnik-profi.cz** (z mého prostředí nešly stáhnout). Pro lokální kopii spusťte `bash scripts/stahni-obrazky.sh` — stáhne je do `assets/img/` a přepíše odkazy. Použijte jen se svolením provozovatele.

## 2. Struktura

```
index.html                  úvod
cenik.html                  ceník (velikosti × výbava, doplňky, kolem kurníku)
kurnik-pro-6|15|25-slepic.html  detaily velikostí (galerie, volba výbavy, parametry)
konfigurator.html           5krokový konfigurátor s živou cenou
chytry-kurnik.html          chytrý kurník, ukázka aplikace, členství, FAQ
reference.html              galerie realizací s filtry + recenze
o-nas.html                  o firmě, hodnoty, roadmapa, partneři
kontakt.html                poptávkový formulář, kontakty, dílna
prihlaseni.html / registrace.html  vzhled účtových stránek (nenapojené)
404.html
assets/css/tokens.css       design tokeny (barvy, typografie, rozestupy, stíny, tmavý režim)
assets/css/main.css         základ, layout, komponenty
assets/css/pages.css        sekce jednotlivých stránek
assets/css/configurator.css konfigurátor
assets/js/data.js           OVĚŘENÁ data (ceny, výbava, doplňky, pravidla) + KP_CONFIG
assets/js/pricing.js        výpočet ceny (server, jinak zrcadlo ceníku)
assets/js/configurator.js   konfigurátor
assets/js/catalog.js        srovnání úrovní, tabulky, galerie, detail produktu, ukázka aplikace
assets/js/main.js           navigace, mobilní menu, tmavý režim, lightbox, formuláře
scripts/stahni-obrazky.sh   lokalizace fotek
tests/price.test.js         testy výpočtu ceny (node)
tests/browser_qa.py         prohlížečová QA (playwright)
```

## 3. Designový systém

- **Barvy:** lesní zelená z identity značky (`#2B5C26` → tmavší `#14261A` pro hloubku), medový akcent podle zlaté slepičky v logu (`#E4A539`) **jen** pro hlavní CTA a aktivní volby, teplá krémová, dřevěné tóny, grafit pro text. Tmavý režim (původní web má přepínač ☀/☾).
- **Typografie:** Fraunces (titulky, řemeslný serif) + Manrope (text, UI). Obě s plnou češtinou.
- **Princip:** málo rámečků, hierarchie přes typografii a prostor, skutečné fotky, čísla tabulárně. Animace jen jemné, `prefers-reduced-motion` respektován.

## 4. Audit původního webu (26. 10. 2026)

| Oblast | Zjištění |
|---|---|
| Platforma | ASP.NET Blazor Server (SignalR spojení, „Obnovuji spojení se serverem…“), autoři Michal Kolařík / Bc. Jan Krejčí |
| Nabídka | 3 velikosti (6 / 15 / 25 slepic) × 3 úrovně (Basic / Top / Extra Profi), 14 doplňků, popeliště, sklad na krmivo, podsvícení; kurníky na míru |
| Nákup | **Žádný košík ani objednávka.** Konfigurátor končí „Nezávazně poptat“ a „Uložit konfiguraci“; online platby (Comgate) jen pro členství VIP / Super VIP |
| Účty | Přihlášení, registrace, zapomenuté heslo, aplikace (hejno, snáška, hry, členství, recenze) |
| Konfigurátor | Kroky A velikost, B výbava, C doplňky, E rozmístění prvků na 4 stěnách (vlevo/uprostřed/vpravo, panty dvířek, spád střechy), 360° náhled, živá cena |
| Neověřeno | stránky `/kurniky-pro-6-slepic`, `/kurniky-pro-25-slepic`, `/zateplene-kurniky`, `/kurniky-na-miru`, `/poradna`, právní stránky — nebyly z mého prostředí dostupné; data pro 6 a 25 jsou z ceníku |

## 5. Skutečně zachované funkce a logika

- **Všechny ceny a pravidla ceníku** 1:1 (`data.js`): 9 základních cen, doplňky, u Extra se lazura/okap/solár/popeliště/tubus neúčtují, zapojení elektroinstalace 1 950 Kč jednou k ⚡ doplňkům (u Extra v ceně), 2 světlíky + 1 ventilace v ceně, další kus 1 000 Kč.
- **Konfigurátor:** stejné volby a pořadí jako originál (výchozí 15 slepic / Basic), 4 stěny, pozice, panty, spád střechy, povinná ventilace uprostřed přední stěny. Konfigurace se ukládá do odkazu (`#c=…`) a do prohlížeče; `?size=&level=` z detailů a ceníku.
- **Poptávka:** stejná pole jako původní formulář. Bez napojeného backendu se **otevře e-mail** na info@kurnik-profi.cz s celou konfigurací — nic se netváří jako odeslané.
- Kontakty, provozovatel, IČO, partneři, sociální sítě, roadmapa, recenze (citovány doslova), členství a jeho ceny, FAQ chytrého kurníku (zkrácené), odkazy na právní stránky a aplikaci.

## 6. Integrační požadavky (co čeká na napojení)

Vše se zapíná v `assets/js/data.js → KP_CONFIG`:

| Klíč | Účel | Očekávaný kontrakt |
|---|---|---|
| `priceEndpoint` | Server je autoritou ceny | `POST` JSON konfigurace `{size, level, addons[], roof, layout[{el, wall, pos, hinge?}]}` → `{ total: number, lines: [{label, price, detail?, included?}] }`. Při chybě se zobrazí lokální výpočet s upozorněním. |
| `inquiryEndpoint` | Odeslání poptávky (konfigurátor i kontakt) | `POST` JSON `{name, phone, email, message, smart, config, quote}` → 2xx |
| `saveEndpoint` | Uložení konfigurace k účtu | `POST` konfigurace; 401 → přesměrování na přihlášení |
| `loginUrl`, `registerUrl` | Účty | zatím odkazy na současný systém |

Doporučený postup: převést šablony do Razor komponent Blazoru (CSS/JS lze převzít beze změny), nebo vystavit 3 výše uvedené endpointy a statický web nasadit na staging. Ověřit a doplnit: přesné výchozí pozice prvků na stěnách, případná zakázaná kombinace pozic, počet snáškových boxů u 6 a 25, 360° vizualizace, plná galerie (151 fotek) a velké náhledy, mapa OSM, vícejazyčnost (přepínač CS).

## 7. Výsledky testů

Spuštěno 26. 10. 2026 v headless Chromium 153:

- `node tests/price.test.js` — **22/22 OK** (všech 9 základních cen, balíčky u všech velikostí vč. kontrolního příkladu z ceníku 47 040 vs. 46 040 Kč, jednorázový elektro poplatek, světlíky/ventilace navíc, odmítnutí neplatných dat).
- `python3 tests/browser_qa.py` — 13 stránek × 7 šířek (320, 375, 430, 768, 1024, 1440, 1920 px): **0 chyb v konzoli, 0 horizontálních přetečení, 0 rozbitých interních odkazů**; 11/11 průchodů konfigurátorem a detailem produktu (ceny po každém kroku, obnova z odkazu, parametry z URL, validace formuláře).
- Vizuálně zkontrolováno na screenshotech (desktop 1440, mobil 375).

**Netestováno:** reálné načtení fotek a fontů (v testovacím prostředí blokované, nahrazené zástupci), Safari/Firefox, skutečné čtečky obrazovky, odeslání do backendu (neexistuje), rychlost na produkčním hostingu.

## 8. Nevyřešené body

1. Napojení ceny, poptávky, uložení a účtů na Blazor backend (viz 6).
2. Fotky jsou hotlinkované v 480 px; pro hero a galerii chybí větší verze — doplnit z úložiště originálu.
3. Neověřené podstránky (viz audit) nejsou redesignované; odkazy vedou na originál.
4. Pravidla rozmístění na stěnách: kolize se jen upozorňují, nezakazují.
5. Ukázka aplikace používá jen data ze samotných ukázek na původním webu (Anežka, Pepina).
6. Přepínač jazyka a 360° náhled konfigurátoru nejsou součástí.
