# Helyszín-specifikus landing oldalak — Design

## Cél

A domain friss (nincs Google-indexelés), és az oldal jelenleg egy egyoldalas
(one-pager) site kevés egyedi futószöveggel. Cél: 4 új, helyszín-specifikus
landing oldal, ami a long-tail helyi kereséseket célozza (pl. "térkövezés
Cegléd", "földmunka Nagykőrös"), belső linkeléssel és egyedi tartalommal,
anélkül hogy folyamatosan karbantartandó blogot kellene indítani.

## Érintett helyszínek

A meglévő referencia-galéria (`referenciak.html`) fotói alapján a 4 aktív
munkaterület: **Cegléd, Nagykőrös, Abony, Kecskemét**. Minden helyszínhez
tartozik legalább egy fotó minden fő szolgáltatás-kategóriából (földmunka,
alapozás, betonozás, térkövezés, kertépítés), kivéve ahol jelölöm.

## Fájlstruktúra

Új fájlok a repó gyökerében (ugyanúgy, ahogy `index.html`/`referenciak.html`
is ott vannak), és szinkronizálva a `docs/` build mappába — a meglévő
munkafolyamat szerint:

```
cegled.html
nagykoros.html
abony.html
kecskemet.html
location.css        (közös stílus az új oldalakhoz)
```

A `docs/` alá ugyanezek bekerülnek, a képösvények `preview/img/` → `img/`
cserével, ahogy az `index.html`/`referenciak.html` szinkronnál eddig is.

**Fontos:** a `specs/` mappa (ez a dokumentum) NEM kerül a `docs/` build alá —
az egy belső tervezési artifact, nem publikus tartalom.

## location.css — közös stílus

A 4 új oldal saját `<style>` blokk helyett egy közös `location.css`-t használ
(ugyanaz a minta, mint a `legal.css` a jogi oldalaknál), hogy ne kelljen a
teljes ~300 soros CSS-t 4x duplikálni. A meglévő `index.html`/`referenciak.html`
nem változik, azok megtartják a saját inline stílusukat.

A `location.css` a meglévő design-tokeneket (színek, fontok) újrahasznosítja:
- Háttérszínek: `#DEE3D9` / `#DAD8CF` / `#171C15` (footer)
- Fő zöld: `#42563A`, világos zöld: `#9CB58E`
- Fontok: Archivo Black (címek), Space Grotesk (kiemelések), Inter (törzsszöveg)

## Oldal-sablon (mind a 4 oldalra érvényes)

1. **Fejléc** — azonos nav-val, mint most (Szolgáltatások, Munkáim, Rólam,
   GYIK, Kapcsolat) — nincs dropdown, a helyszín-oldalak nem kerülnek a
   fő navigációba.
2. **Mini-hero** — `<h1>`: "Földmunka, betonozás és térkövezés Cegléden"
   (városnevenként), 1 bekezdés egyedi bevezető szöveg.
3. **Szolgáltatás-blokk** — mind az 5 szolgáltatás felsorolva, 2-3 mondat
   szolgáltatásonként, helyi utalással (pl. "cegléd-i családi házaknál
   leggyakrabban...").
4. **Szűrt galéria** — csak az adott településhez tartozó meglévő fotók
   (a `referenciak.html`-ből ismert kép+alt+címke adatok újrafelhasználva,
   statikus rács, szűrő-gombok NÉLKÜL — egy helyszínen úgyis kevés kép van,
   nem kell interaktív szűrő).
5. **CTA sáv** — "Kérj ingyenes árajánlatot Cegléden" gomb →
   `index.html#contact` (nincs önálló űrlap az oldalon, ahogy döntöttük).
6. **Lábléc** — azonos a többi oldaléval, plusz egy új **"Térségek"** oszlop,
   ami mind a 4 helyszín-oldalt linkeli (belső linkelés SEO-hatásért), ez az
   oszlop bekerül `index.html`, `referenciak.html`, `impresszum.html`,
   `adatkezeles.html` láblécébe is.

## SEO-réteg (oldalanként egyedi)

- `<title>`: "Térkövezés, betonozás, földmunka Cegléden — Tóth Tamás"
  (városnevenként)
- `<meta description>`: egyedi, város + szolgáltatás-lista
- `<link rel="canonical">`: `https://tothtamasepito.hu/cegled.html`
- Teljes favicon-linkkészlet (ugyanaz, mint a többi oldalon, copy-paste)
- OG/Twitter tagek, város-specifikus képpel
- JSON-LD: **`Service`** típus (nem duplikált `LocalBusiness`), ami a fő
  `LocalBusiness`-re hivatkozik `provider` mezőn keresztül, `areaServed` az
  adott városra állítva:

```json
{
  "@context": "https://schema.org",
  "@type": "Service",
  "serviceType": "Földmunka, betonozás, térkövezés",
  "provider": { "@type": "LocalBusiness", "name": "Tóth Tamás", "telephone": "+36209796081" },
  "areaServed": { "@type": "City", "name": "Cegléd" }
}
```

## Egyéb frissítendő fájlok

- **`sitemap.xml`** — 4 új `<url>` bejegyzés
- **`llms.txt`** — 4 új sor az "Oldalak" listában
- **`index.html`, `referenciak.html`, `impresszum.html`, `adatkezeles.html`**
  láblécei — új "Térségek" oszlop

## Tartalom-vázlat városonként (a meglévő fotók alapján)

| Város | Kiemelt kategóriák (legtöbb fotó) |
|---|---|
| Cegléd | Térkövezés, kertépítés, alapozás, betonozás |
| Nagykőrös | Alapozás, kertépítés, betonozás |
| Abony | Földmunka, betonozás, kertépítés |
| Kecskemét | Alapozás, kertépítés, földmunka |

Minden oldalon mind az 5 szolgáltatás szerepel a szövegben (SEO-lefedettség
miatt), de a galéria csak azt mutatja, amihez ténylegesen van fotó az adott
településről — nem állítunk be nem létező referenciát.

## Hibakezelés / szélső esetek

- Ha egy településhez nincs fotó egy adott kategóriában (pl. Cegléd —
  földmunka), a szöveges blokk akkor is szerepel (mert azt is vállalja ott),
  csak a galériában nem lesz hozzá kép.
- A CTA gomb `index.html#contact`-ra mutat minden helyszín-oldalról — nincs
  szükség a query-string alapú "honnan jött a lead" követésre ebben a
  körben (YAGNI, ez egy külön funkció lenne analitikával).

## Tesztelés

- Helyi statikus szerverrel (Node.js, ahogy eddig is) minden új oldal
  betöltése, kép-404 ellenőrzés, favicon-linkek 200-as válasza.
- Böngészős vizuális ellenőrzés mind a 4 oldalon (asztali nézet legalább).
- `sitemap.xml`/`llms.txt` frissítés ellenőrzése.
- Manuális linkellenőrzés: minden láblécből minden helyszín-oldal elérhető,
  és minden helyszín-oldalról vissza a főoldalra/kapcsolatra.

## Terjedelem

Ez a kör csak ezt a 4 oldalt fedi le. NEM tartalmazza: blog, query-string
lead-tracking, dropdown navigáció, önálló űrlapok helyszínenként, meglévő
`index.html`/`referenciak.html` vizuális átalakítása.
