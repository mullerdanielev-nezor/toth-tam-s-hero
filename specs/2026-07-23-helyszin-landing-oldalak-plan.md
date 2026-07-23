# Helyszín-specifikus landing oldalak Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Létrehozni 4 helyszín-specifikus landing oldalt (Cegléd, Nagykőrös, Abony, Kecskemét), amik egyedi tartalommal és belső linkeléssel célozzák a helyi long-tail kereséseket, a `2026-07-23-helyszin-landing-oldalak-design.md` specifikáció szerint.

**Architecture:** 4 önálló statikus HTML fájl a repó gyökerében, egy közös `location.css`-sel (nem nyúlunk a meglévő `index.html`/`referenciak.html` stílusához). Minden oldal a meglévő fejlécet/láblécet használja, egyedi hero-szöveggel, szolgáltatás-blokkal és a településhez tartozó, már meglévő referencia-fotókkal. A meglévő 4 oldal lábléce kap egy új "Térségek" linkoszlopot.

**Tech Stack:** Statikus HTML/CSS, nincs build-lépés. A `docs/` mappa a GitHub Pages live build — minden fájlt oda is át kell másolni, a képútvonalakat `preview/img/` → `img/`-re cserélve, ahogy a projekt eddigi konvenciója.

**Nincs automatizált tesztkeret ebben a repóban** — a "tesztelés" itt HTTP-státusz-ellenőrzést (curl egy helyi Node szerveren) és böngészős vizuális ellenőrzést jelent, nem unit teszteket.

---

## Fájlstruktúra áttekintés

```
location.css                (új, közös stílus a 4 helyszín-oldalhoz)
cegled.html                 (új)
nagykoros.html              (új)
abony.html                  (új)
kecskemet.html              (új)
sitemap.xml                 (módosít: 4 új <url>)
llms.txt                    (módosít: 4 új sor)
index.html                  (módosít: lábléc "Térségek" oszlop)
referenciak.html            (módosít: lábléc "Térségek" oszlop)
impresszum.html             (módosít: lábléc "Térségek" oszlop)
adatkezeles.html            (módosít: lábléc "Térségek" oszlop)
docs/*                      (minden fenti fájl szinkronizált másolata)
```

---

### Task 1: `location.css` létrehozása

**Files:**
- Create: `location.css`

- [ ] **Step 1: Hozd létre a fájlt a következő tartalommal**

```css
* { box-sizing: border-box; margin:0; padding:0; }
body { font-family:'Inter', sans-serif; background:#DEE3D9; color:#1C1B19; }
img.photo { display:block; }

.logo { font-family:'Archivo Black',sans-serif; font-size:18px; letter-spacing:-0.5px; color:#1C1B19; }
.logo span { color:#42563A; }
header.site { background:#F4F2ED; display:flex; align-items:center; justify-content:space-between; padding:18px 40px; border-bottom:1px solid rgba(28,27,25,.12); position:sticky; top:0; z-index:100; }
a.logo { text-decoration:none; }
header.site nav { display:flex; align-items:center; gap:30px; font-family:'Space Grotesk',sans-serif; font-size:13.5px; font-weight:600; }
header.site nav a { color:#1C1B19; text-decoration:none; opacity:.75; }
header.site nav a:hover { opacity:1; color:#42563A; }
@media(max-width:820px){
  header.site { flex-direction:column; align-items:flex-start; gap:12px; }
  header.site nav { flex-wrap:wrap; gap:14px; }
}

.btn { padding:15px 28px; border-radius:100px; font-weight:700; font-size:14px; text-decoration:none; display:inline-flex; align-items:center; justify-content:center; gap:8px; border:none; cursor:pointer; }
.btn-primary { background:#42563A; color:#F4F2ED; }
.btn-outline { border:1.5px solid #42563A; color:#42563A; background:transparent; }

.loc-hero { background:#DAD8CF; padding:70px 40px 60px; text-align:center; }
.loc-hero .kicker { display:inline-block; padding:6px 16px; border-radius:100px; font-size:14px; font-weight:700; border:1.5px solid #42563A; color:#42563A; margin-bottom:16px; }
.loc-hero h1 { font-family:'Archivo Black',sans-serif; font-size:clamp(30px,4.6vw,52px); text-transform:uppercase; letter-spacing:-1.5px; line-height:1.05; margin-bottom:18px; max-width:820px; margin-left:auto; margin-right:auto; }
.loc-hero p { font-size:16px; line-height:1.7; opacity:.8; max-width:640px; margin:0 auto 28px; }

.loc-services { background:#DEE3D9; padding:60px 40px; }
.loc-services .wrap { max-width:1100px; margin:0 auto; display:grid; grid-template-columns:repeat(auto-fit, minmax(220px, 1fr)); gap:28px; }
.loc-services .lsitem h3 { font-family:'Space Grotesk',sans-serif; font-size:18px; color:#42563A; margin-bottom:8px; }
.loc-services .lsitem p { font-size:14px; line-height:1.6; opacity:.8; }

.loc-gallery { background:#DAD8CF; padding:60px 40px; text-align:center; }
.loc-gallery h2 { font-family:'Archivo Black',sans-serif; font-size:clamp(24px,3.4vw,36px); text-transform:uppercase; letter-spacing:-1px; margin-bottom:30px; }
.loc-gallery .wrap { max-width:1100px; margin:0 auto; }
.lg-grid { display:grid; grid-template-columns:repeat(auto-fit, minmax(220px, 1fr)); gap:16px; margin-bottom:34px; }
.lg-item { border-radius:14px; overflow:hidden; position:relative; background:#151412; aspect-ratio:4/3; }
.lg-item img.photo { width:100%; height:100%; object-fit:cover; }
.lg-item span { position:absolute; bottom:0; left:0; right:0; padding:10px 14px; background:linear-gradient(0deg, rgba(0,0,0,.8), transparent); color:#F4F2ED; font-size:12.5px; font-family:'Space Grotesk',sans-serif; text-align:left; }

.loc-cta { background:#171C15; padding:60px 40px; text-align:center; }
.loc-cta h2 { font-family:'Archivo Black',sans-serif; font-size:clamp(24px,3.4vw,36px); text-transform:uppercase; letter-spacing:-1px; color:#F4F2ED; margin-bottom:12px; }
.loc-cta p { color:#DAD8CF; opacity:.75; margin-bottom:24px; font-size:14px; }

.sitefooter { background:#171C15; padding:60px 40px 0; }
.sitefooter .fwrap { max-width:1200px; margin:0 auto; display:flex; gap:50px; flex-wrap:wrap; padding-bottom:44px; border-bottom:1px solid rgba(255,255,255,.1); }
.sitefooter .fcol { flex:1; min-width:160px; display:flex; flex-direction:column; gap:10px; }
.sitefooter .fbrand { flex:1.6; min-width:240px; }
.sitefooter .fbrand p { font-size:13px; color:#8a877e; line-height:1.6; margin-top:12px; max-width:280px; }
.sitefooter .fcol h4 { font-family:'Space Grotesk',sans-serif; font-size:12px; text-transform:uppercase; letter-spacing:.5px; color:#9CB58E; margin-bottom:6px; }
.sitefooter .fcol a, .sitefooter .fcol span { color:#cfcac2; text-decoration:none; font-size:13.5px; opacity:.85; }
.sitefooter .fcol a:hover { opacity:1; color:#9CB58E; }
.sitefooter .fbottom { max-width:1200px; margin:0 auto; padding:22px 0; font-size:12px; color:#666; text-align:center; }
```

- [ ] **Step 2: Commit**

```bash
git add location.css
git commit -m "Add shared location.css for city landing pages"
```

---

### Task 2: `cegled.html` létrehozása

**Files:**
- Create: `cegled.html`

- [ ] **Step 1: Hozd létre a fájlt a következő tartalommal**

```html
<!DOCTYPE html>
<html lang="hu">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Térkövezés, betonozás, alapozás Cegléden — Tóth Tamás</title>
<meta name="description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Cegléden — Tóth Tamás egyéni vállalkozó, ingyenes helyszíni felméréssel, garanciával.">
<link rel="canonical" href="https://tothtamasepito.hu/cegled.html">
<link rel="icon" href="favicon.ico" sizes="any">
<link rel="icon" type="image/svg+xml" href="favicon.svg">
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">
<link rel="icon" type="image/png" sizes="48x48" href="favicon-48x48.png">
<link rel="icon" type="image/png" sizes="96x96" href="favicon-96x96.png">
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
<link rel="apple-touch-icon" sizes="76x76" href="apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="120x120" href="apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="152x152" href="apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="167x167" href="apple-touch-icon-167x167.png">
<link rel="apple-touch-icon" sizes="60x60" href="apple-touch-icon-60x60.png">
<meta name="msapplication-TileImage" content="mstile-144x144.png">
<meta name="msapplication-TileColor" content="#42563A">
<link rel="icon" type="image/png" sizes="192x192" href="android-chrome-192x192.png">
<link rel="icon" type="image/png" sizes="512x512" href="android-chrome-512x512.png">
<link rel="manifest" href="site.webmanifest">
<meta name="theme-color" content="#42563A">
<meta property="og:type" content="website">
<meta property="og:locale" content="hu_HU">
<meta property="og:title" content="Térkövezés, betonozás, alapozás Cegléden — Tóth Tamás">
<meta property="og:description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Cegléden — ingyenes helyszíni felméréssel, garanciával.">
<meta property="og:url" content="https://tothtamasepito.hu/cegled.html">
<meta property="og:image" content="https://tothtamasepito.hu/img/terkovezes-nagy-terasz-keritessel-cegled.webp">
<meta name="twitter:card" content="summary_large_image">
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Service",
  "serviceType": "Földmunka, alapozás, betonozás, térkövezés, kertépítés",
  "provider": { "@type": "LocalBusiness", "name": "Tóth Tamás", "telephone": "+36209796081", "email": "vitotamas1999@gmail.com" },
  "areaServed": { "@type": "City", "name": "Cegléd" }
}
</script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
<link rel="stylesheet" href="location.css">
</head>
<body>

<header class="site">
  <a class="logo" href="index.html">TÓTH<span>·</span>TAMÁS</a>
  <nav id="mainNav">
    <a href="index.html#services">Szolgáltatások</a>
    <a href="referenciak.html">Munkáim</a>
    <a href="index.html#ab1">Rólam</a>
    <a href="index.html#faq">GYIK</a>
    <a href="index.html#contact">Kapcsolat</a>
  </nav>
</header>

<section class="loc-hero">
  <div class="in">
    <span class="kicker">CEGLÉD</span>
    <h1>Földmunka, betonozás és térkövezés Cegléden</h1>
    <p>Cegléden és környékén évek óta végzek térkövezést, betonozást és kerítésépítést családi házaknál és udvaroknál. A munkát elejétől a végéig magam viszem, precízen és garanciával.</p>
    <a class="btn btn-primary" href="index.html#contact">Ingyenes árajánlatot kérek</a>
  </div>
</section>

<section class="loc-services">
  <div class="wrap">
    <div class="lsitem">
      <h3>Földmunka</h3>
      <p>Alapárok kiásása, tereprendezés és drénezés Cegléden — akár egy új teraszhoz, akár egy kerítés alapozásához.</p>
    </div>
    <div class="lsitem">
      <h3>Alapozás</h3>
      <p>Zsaluzás, vasalás és pontos szintezés — ceglédi ingatlanoknál ez a legtöbb terasz- és kerítésépítés első lépése.</p>
    </div>
    <div class="lsitem">
      <h3>Betonozás</h3>
      <p>Járdák, teraszalapok és sima betonfelületek kivitelezése Cegléden, tartós, repedésmentes kivitelben.</p>
    </div>
    <div class="lsitem">
      <h3>Térkövezés</h3>
      <p>Teraszok, bejárók és lépcsős udvarok térkövezése Cegléden — ez az egyik leggyakoribb munkám ebben a városban.</p>
    </div>
    <div class="lsitem">
      <h3>Kertépítés</h3>
      <p>Kerítésoszlopok alapozása, sárga blokk és paneles kerítések építése ceglédi udvaroknál.</p>
    </div>
  </div>
</section>

<section class="loc-gallery">
  <div class="wrap">
    <h2>Munkáim Cegléden</h2>
    <div class="lg-grid">
      <div class="lg-item"><img class="photo" src="preview/img/alapozas-zsaluzas-cegled.webp" alt="Zsaluzás, alapkiásás, Cegléd"><span>Zsaluzás, alapkiásás</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/betonozas-kesz-betonlap-cegled.webp" alt="Kész betonlap, Cegléd"><span>Kész betonlap</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/terkovezes-nagy-terasz-keritessel-cegled.webp" alt="Nagy terasz, kerítéssel, Cegléd"><span>Nagy terasz, kerítéssel</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/terkovezes-terasz-lepcsovel-cegled.webp" alt="Teraszépítés lépcsővel, Cegléd"><span>Teraszépítés lépcsővel</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-sargablokk-keritesoszlopokkal-cegled.jpg" alt="Kész kerítés, sárga blokk oszlopokkal, Cegléd"><span>Kész kerítés, sárga blokk oszlopokkal</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-keritesoszlopok-alapozasa-cegled.jpg" alt="Kerítésoszlopok alapozása, Cegléd"><span>Kerítésoszlopok alapozása</span></div>
    </div>
    <a class="btn btn-outline" href="referenciak.html">Összes munka megtekintése →</a>
  </div>
</section>

<section class="loc-cta">
  <h2>Kérj ingyenes árajánlatot Cegléden</h2>
  <p>Nincs kötelezettség, ingyenes helyszíni felmérés — 24 órán belül visszahívlak.</p>
  <a class="btn btn-primary" href="index.html#contact">Árajánlatot kérek</a>
</section>

<footer class="sitefooter">
  <div class="fwrap">
    <div class="fcol fbrand">
      <div class="logo" style="color:#F4F2ED;">TÓTH<span style="color:#9CB58E;">·</span>TAMÁS</div>
      <p>Földmunka, betonozás és térkövezés — precízen, garanciával, Heves megyében és Budapesten.</p>
    </div>
    <div class="fcol">
      <h4>Oldal</h4>
      <a href="index.html#services">Szolgáltatások</a>
      <a href="referenciak.html">Munkáim</a>
      <a href="index.html#ab1">Rólam</a>
      <a href="index.html#faq">GYIK</a>
      <a href="index.html#contact">Kapcsolat</a>
    </div>
    <div class="fcol">
      <h4>Elérhetőség</h4>
      <a href="tel:+36209796081">+36 20 979 6081</a>
      <a href="mailto:vitotamas1999@gmail.com">vitotamas1999@gmail.com</a>
      <span>Heves megye és Budapest</span>
    </div>
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
  <div class="fbottom">© 2026 Tóth Tamás — Minden jog fenntartva.</div>
</footer>

</body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add cegled.html
git commit -m "Add Cegléd location landing page"
```

---

### Task 3: `nagykoros.html` létrehozása

**Files:**
- Create: `nagykoros.html`

- [ ] **Step 1: Hozd létre a fájlt a következő tartalommal**

```html
<!DOCTYPE html>
<html lang="hu">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Alapozás, betonozás, kertépítés Nagykőrösön — Tóth Tamás</title>
<meta name="description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Nagykőrösön — Tóth Tamás egyéni vállalkozó, ingyenes helyszíni felméréssel, garanciával.">
<link rel="canonical" href="https://tothtamasepito.hu/nagykoros.html">
<link rel="icon" href="favicon.ico" sizes="any">
<link rel="icon" type="image/svg+xml" href="favicon.svg">
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">
<link rel="icon" type="image/png" sizes="48x48" href="favicon-48x48.png">
<link rel="icon" type="image/png" sizes="96x96" href="favicon-96x96.png">
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
<link rel="apple-touch-icon" sizes="76x76" href="apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="120x120" href="apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="152x152" href="apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="167x167" href="apple-touch-icon-167x167.png">
<link rel="apple-touch-icon" sizes="60x60" href="apple-touch-icon-60x60.png">
<meta name="msapplication-TileImage" content="mstile-144x144.png">
<meta name="msapplication-TileColor" content="#42563A">
<link rel="icon" type="image/png" sizes="192x192" href="android-chrome-192x192.png">
<link rel="icon" type="image/png" sizes="512x512" href="android-chrome-512x512.png">
<link rel="manifest" href="site.webmanifest">
<meta name="theme-color" content="#42563A">
<meta property="og:type" content="website">
<meta property="og:locale" content="hu_HU">
<meta property="og:title" content="Alapozás, betonozás, kertépítés Nagykőrösön — Tóth Tamás">
<meta property="og:description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Nagykőrösön — ingyenes helyszíni felméréssel, garanciával.">
<meta property="og:url" content="https://tothtamasepito.hu/nagykoros.html">
<meta property="og:image" content="https://tothtamasepito.hu/img/betonozas-kesz-jarda-nagykoros.webp">
<meta name="twitter:card" content="summary_large_image">
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Service",
  "serviceType": "Földmunka, alapozás, betonozás, térkövezés, kertépítés",
  "provider": { "@type": "LocalBusiness", "name": "Tóth Tamás", "telephone": "+36209796081", "email": "vitotamas1999@gmail.com" },
  "areaServed": { "@type": "City", "name": "Nagykőrös" }
}
</script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
<link rel="stylesheet" href="location.css">
</head>
<body>

<header class="site">
  <a class="logo" href="index.html">TÓTH<span>·</span>TAMÁS</a>
  <nav id="mainNav">
    <a href="index.html#services">Szolgáltatások</a>
    <a href="referenciak.html">Munkáim</a>
    <a href="index.html#ab1">Rólam</a>
    <a href="index.html#faq">GYIK</a>
    <a href="index.html#contact">Kapcsolat</a>
  </nav>
</header>

<section class="loc-hero">
  <div class="in">
    <span class="kicker">NAGYKŐRÖS</span>
    <h1>Alapozás, betonozás és kertépítés Nagykőrösön</h1>
    <p>Nagykőrösön leggyakrabban alapozási és betonozási munkákat végzek, de a teljes palettát vállalom — a földmunkától a kész kerítésig.</p>
    <a class="btn btn-primary" href="index.html#contact">Ingyenes árajánlatot kérek</a>
  </div>
</section>

<section class="loc-services">
  <div class="wrap">
    <div class="lsitem">
      <h3>Földmunka</h3>
      <p>Alapárok kiásása és tereprendezés Nagykőrösön, új építésű és felújítási munkákhoz egyaránt.</p>
    </div>
    <div class="lsitem">
      <h3>Alapozás</h3>
      <p>Vasalt alap, vasháló betonozás előtt — ez a legtöbb nagykőrösi munkám alapja, pontos szintezéssel.</p>
    </div>
    <div class="lsitem">
      <h3>Betonozás</h3>
      <p>Sima betonfelületek és betonjárdák kivitelezése Nagykőrösön, tartós, repedésmentes technológiával.</p>
    </div>
    <div class="lsitem">
      <h3>Térkövezés</h3>
      <p>Teraszok és bejárók térkövezése Nagykőrösön, egyedi mintázattal is.</p>
    </div>
    <div class="lsitem">
      <h3>Kertépítés</h3>
      <p>Lépcsős kerítéselem-falak, sárga blokk kerítésoszlopok és kerítésváz építése nagykőrösi telkeken.</p>
    </div>
  </div>
</section>

<section class="loc-gallery">
  <div class="wrap">
    <h2>Munkáim Nagykőrösön</h2>
    <div class="lg-grid">
      <div class="lg-item"><img class="photo" src="preview/img/alapozas-vasalt-alap-nagykoros.webp" alt="Vasalt alap az udvaron, Nagykőrös"><span>Vasalt alap az udvaron</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/betonozas-kesz-jarda-nagykoros.webp" alt="Kész betonjárda, Nagykőrös"><span>Kész betonjárda</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/betonozas-sima-felulet-nagykoros.webp" alt="Kész, sima betonfelület, Nagykőrös"><span>Kész, sima betonfelület</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-lepcsos-keritesfal-nagykoros.webp" alt="Lépcsős kerítéselem-fal, Nagykőrös"><span>Lépcsős kerítéselem-fal</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-sargablokk-keritesoszlopokkal-nagykoros.jpg" alt="Kerítés, sárga blokk oszlopokkal, Nagykőrös"><span>Kerítés, sárga blokk oszlopokkal</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-keritesvaz-tujak-kozott-nagykoros.jpg" alt="Kerítésváz, tuják között, Nagykőrös"><span>Kerítésváz, tuják között</span></div>
    </div>
    <a class="btn btn-outline" href="referenciak.html">Összes munka megtekintése →</a>
  </div>
</section>

<section class="loc-cta">
  <h2>Kérj ingyenes árajánlatot Nagykőrösön</h2>
  <p>Nincs kötelezettség, ingyenes helyszíni felmérés — 24 órán belül visszahívlak.</p>
  <a class="btn btn-primary" href="index.html#contact">Árajánlatot kérek</a>
</section>

<footer class="sitefooter">
  <div class="fwrap">
    <div class="fcol fbrand">
      <div class="logo" style="color:#F4F2ED;">TÓTH<span style="color:#9CB58E;">·</span>TAMÁS</div>
      <p>Földmunka, betonozás és térkövezés — precízen, garanciával, Heves megyében és Budapesten.</p>
    </div>
    <div class="fcol">
      <h4>Oldal</h4>
      <a href="index.html#services">Szolgáltatások</a>
      <a href="referenciak.html">Munkáim</a>
      <a href="index.html#ab1">Rólam</a>
      <a href="index.html#faq">GYIK</a>
      <a href="index.html#contact">Kapcsolat</a>
    </div>
    <div class="fcol">
      <h4>Elérhetőség</h4>
      <a href="tel:+36209796081">+36 20 979 6081</a>
      <a href="mailto:vitotamas1999@gmail.com">vitotamas1999@gmail.com</a>
      <span>Heves megye és Budapest</span>
    </div>
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
  <div class="fbottom">© 2026 Tóth Tamás — Minden jog fenntartva.</div>
</footer>

</body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add nagykoros.html
git commit -m "Add Nagykőrös location landing page"
```

---

### Task 4: `abony.html` létrehozása

**Files:**
- Create: `abony.html`

- [ ] **Step 1: Hozd létre a fájlt a következő tartalommal**

```html
<!DOCTYPE html>
<html lang="hu">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Földmunka, betonozás, kerítésépítés Abonyban — Tóth Tamás</title>
<meta name="description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Abonyban — Tóth Tamás egyéni vállalkozó, ingyenes helyszíni felméréssel, garanciával.">
<link rel="canonical" href="https://tothtamasepito.hu/abony.html">
<link rel="icon" href="favicon.ico" sizes="any">
<link rel="icon" type="image/svg+xml" href="favicon.svg">
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">
<link rel="icon" type="image/png" sizes="48x48" href="favicon-48x48.png">
<link rel="icon" type="image/png" sizes="96x96" href="favicon-96x96.png">
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
<link rel="apple-touch-icon" sizes="76x76" href="apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="120x120" href="apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="152x152" href="apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="167x167" href="apple-touch-icon-167x167.png">
<link rel="apple-touch-icon" sizes="60x60" href="apple-touch-icon-60x60.png">
<meta name="msapplication-TileImage" content="mstile-144x144.png">
<meta name="msapplication-TileColor" content="#42563A">
<link rel="icon" type="image/png" sizes="192x192" href="android-chrome-192x192.png">
<link rel="icon" type="image/png" sizes="512x512" href="android-chrome-512x512.png">
<link rel="manifest" href="site.webmanifest">
<meta name="theme-color" content="#42563A">
<meta property="og:type" content="website">
<meta property="og:locale" content="hu_HU">
<meta property="og:title" content="Földmunka, betonozás, kerítésépítés Abonyban — Tóth Tamás">
<meta property="og:description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Abonyban — ingyenes helyszíni felméréssel, garanciával.">
<meta property="og:url" content="https://tothtamasepito.hu/abony.html">
<meta property="og:image" content="https://tothtamasepito.hu/img/betonozas-jarda-hosszu-szakasz-abony.webp">
<meta name="twitter:card" content="summary_large_image">
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Service",
  "serviceType": "Földmunka, alapozás, betonozás, térkövezés, kertépítés",
  "provider": { "@type": "LocalBusiness", "name": "Tóth Tamás", "telephone": "+36209796081", "email": "vitotamas1999@gmail.com" },
  "areaServed": { "@type": "City", "name": "Abony" }
}
</script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
<link rel="stylesheet" href="location.css">
</head>
<body>

<header class="site">
  <a class="logo" href="index.html">TÓTH<span>·</span>TAMÁS</a>
  <nav id="mainNav">
    <a href="index.html#services">Szolgáltatások</a>
    <a href="referenciak.html">Munkáim</a>
    <a href="index.html#ab1">Rólam</a>
    <a href="index.html#faq">GYIK</a>
    <a href="index.html#contact">Kapcsolat</a>
  </nav>
</header>

<section class="loc-hero">
  <div class="in">
    <span class="kicker">ABONY</span>
    <h1>Földmunka, betonozás és kerítésépítés Abonyban</h1>
    <p>Abonyban elsősorban földmunkát, betonozást és kerítésépítést végzek — zúzottkő terítéstől a kész, tégla szegélyes betonjárdáig.</p>
    <a class="btn btn-primary" href="index.html#contact">Ingyenes árajánlatot kérek</a>
  </div>
</section>

<section class="loc-services">
  <div class="wrap">
    <div class="lsitem">
      <h3>Földmunka</h3>
      <p>Zúzottkő terítés, tereprendezés és törmelék-elhordás Abonyban — ez az egyik legtöbbet kért munkám itt.</p>
    </div>
    <div class="lsitem">
      <h3>Alapozás</h3>
      <p>Zúzottkő ágyazat és pontos szintezés az alapozási munkák előkészítéseként.</p>
    </div>
    <div class="lsitem">
      <h3>Betonozás</h3>
      <p>Betonjárdák tégla vagy kavicságyazatos szegéllyel, hosszú szakaszokban is — Abonyban több ilyen munkát végeztem.</p>
    </div>
    <div class="lsitem">
      <h3>Térkövezés</h3>
      <p>Teraszok és bejárók térkövezése Abonyban, igény szerint egyedi mintázattal.</p>
    </div>
    <div class="lsitem">
      <h3>Kertépítés</h3>
      <p>Fekete kerítéspanelek, cserjesorral vagy oszlopokkal — kész kerítés a mezsgyén, ahogy Abonyban szokás.</p>
    </div>
  </div>
</section>

<section class="loc-gallery">
  <div class="wrap">
    <h2>Munkáim Abonyban</h2>
    <div class="lg-grid">
      <div class="lg-item"><img class="photo" src="preview/img/foldmunka-zuzottko-teritese-abony.webp" alt="Zúzottkő terítés, közelkép, Abony"><span>Zúzottkő terítés, közelkép</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/foldmunka-tereprendezes-abony.webp" alt="Tereprendezés, Abony"><span>Tereprendezés</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/betonozas-jarda-hosszu-szakasz-abony.webp" alt="Kész járda, hosszú szakasz, Abony"><span>Kész járda, hosszú szakasz</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/betonozas-jarda-teglaszegellyel-abony.webp" alt="Kész járda, tégla szegéllyel, Abony"><span>Kész járda, tégla szegéllyel</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-fekete-keritespanel-cserjesorral-abony.jpg" alt="Fekete kerítéspanel, cserjesorral, Abony"><span>Fekete kerítéspanel, cserjesorral</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-kesz-keritespanel-mezsgyen-abony.jpg" alt="Kész kerítéspanel a mezsgyén, Abony"><span>Kész kerítéspanel a mezsgyén</span></div>
    </div>
    <a class="btn btn-outline" href="referenciak.html">Összes munka megtekintése →</a>
  </div>
</section>

<section class="loc-cta">
  <h2>Kérj ingyenes árajánlatot Abonyban</h2>
  <p>Nincs kötelezettség, ingyenes helyszíni felmérés — 24 órán belül visszahívlak.</p>
  <a class="btn btn-primary" href="index.html#contact">Árajánlatot kérek</a>
</section>

<footer class="sitefooter">
  <div class="fwrap">
    <div class="fcol fbrand">
      <div class="logo" style="color:#F4F2ED;">TÓTH<span style="color:#9CB58E;">·</span>TAMÁS</div>
      <p>Földmunka, betonozás és térkövezés — precízen, garanciával, Heves megyében és Budapesten.</p>
    </div>
    <div class="fcol">
      <h4>Oldal</h4>
      <a href="index.html#services">Szolgáltatások</a>
      <a href="referenciak.html">Munkáim</a>
      <a href="index.html#ab1">Rólam</a>
      <a href="index.html#faq">GYIK</a>
      <a href="index.html#contact">Kapcsolat</a>
    </div>
    <div class="fcol">
      <h4>Elérhetőség</h4>
      <a href="tel:+36209796081">+36 20 979 6081</a>
      <a href="mailto:vitotamas1999@gmail.com">vitotamas1999@gmail.com</a>
      <span>Heves megye és Budapest</span>
    </div>
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
  <div class="fbottom">© 2026 Tóth Tamás — Minden jog fenntartva.</div>
</footer>

</body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add abony.html
git commit -m "Add Abony location landing page"
```

---

### Task 5: `kecskemet.html` létrehozása

**Files:**
- Create: `kecskemet.html`

- [ ] **Step 1: Hozd létre a fájlt a következő tartalommal**

```html
<!DOCTYPE html>
<html lang="hu">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Alapozás, kertépítés, földmunka Kecskeméten — Tóth Tamás</title>
<meta name="description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Kecskeméten — Tóth Tamás egyéni vállalkozó, ingyenes helyszíni felméréssel, garanciával.">
<link rel="canonical" href="https://tothtamasepito.hu/kecskemet.html">
<link rel="icon" href="favicon.ico" sizes="any">
<link rel="icon" type="image/svg+xml" href="favicon.svg">
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">
<link rel="icon" type="image/png" sizes="48x48" href="favicon-48x48.png">
<link rel="icon" type="image/png" sizes="96x96" href="favicon-96x96.png">
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
<link rel="apple-touch-icon" sizes="76x76" href="apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="120x120" href="apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="152x152" href="apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="167x167" href="apple-touch-icon-167x167.png">
<link rel="apple-touch-icon" sizes="60x60" href="apple-touch-icon-60x60.png">
<meta name="msapplication-TileImage" content="mstile-144x144.png">
<meta name="msapplication-TileColor" content="#42563A">
<link rel="icon" type="image/png" sizes="192x192" href="android-chrome-192x192.png">
<link rel="icon" type="image/png" sizes="512x512" href="android-chrome-512x512.png">
<link rel="manifest" href="site.webmanifest">
<meta name="theme-color" content="#42563A">
<meta property="og:type" content="website">
<meta property="og:locale" content="hu_HU">
<meta property="og:title" content="Alapozás, kertépítés, földmunka Kecskeméten — Tóth Tamás">
<meta property="og:description" content="Földmunka, alapozás, betonozás, térkövezés és kertépítés Kecskeméten — ingyenes helyszíni felméréssel, garanciával.">
<meta property="og:url" content="https://tothtamasepito.hu/kecskemet.html">
<meta property="og:image" content="https://tothtamasepito.hu/img/alapozas-zuzottko-fedett-terasz-kecskemet.webp">
<meta name="twitter:card" content="summary_large_image">
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Service",
  "serviceType": "Földmunka, alapozás, betonozás, térkövezés, kertépítés",
  "provider": { "@type": "LocalBusiness", "name": "Tóth Tamás", "telephone": "+36209796081", "email": "vitotamas1999@gmail.com" },
  "areaServed": { "@type": "City", "name": "Kecskemét" }
}
</script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
<link rel="stylesheet" href="location.css">
</head>
<body>

<header class="site">
  <a class="logo" href="index.html">TÓTH<span>·</span>TAMÁS</a>
  <nav id="mainNav">
    <a href="index.html#services">Szolgáltatások</a>
    <a href="referenciak.html">Munkáim</a>
    <a href="index.html#ab1">Rólam</a>
    <a href="index.html#faq">GYIK</a>
    <a href="index.html#contact">Kapcsolat</a>
  </nav>
</header>

<section class="loc-hero">
  <div class="in">
    <span class="kicker">KECSKEMÉT</span>
    <h1>Alapozás, kertépítés és földmunka Kecskeméten</h1>
    <p>Kecskeméten főleg alapozási és kertépítési munkákat végzek, fedett teraszoktól a teljes kerítésépítésig.</p>
    <a class="btn btn-primary" href="index.html#contact">Ingyenes árajánlatot kérek</a>
  </div>
</section>

<section class="loc-services">
  <div class="wrap">
    <div class="lsitem">
      <h3>Földmunka</h3>
      <p>Törmelék és zúzottkő rendezése, tereprendezés Kecskeméten, új és felújítási projekteknél egyaránt.</p>
    </div>
    <div class="lsitem">
      <h3>Alapozás</h3>
      <p>Zúzottkő ágyazat háznál, szegéllyel vagy fedett terasz alá — ez a legjellemzőbb kecskeméti munkám.</p>
    </div>
    <div class="lsitem">
      <h3>Betonozás</h3>
      <p>Betonalapok és felületek kivitelezése Kecskeméten, tartós, pontos technológiával.</p>
    </div>
    <div class="lsitem">
      <h3>Térkövezés</h3>
      <p>Teraszok és bejárók térkövezése kecskeméti ingatlanoknál.</p>
    </div>
    <div class="lsitem">
      <h3>Kertépítés</h3>
      <p>Terasz-deszkázat előkészítése, barna trapézlemez kerítés építése — Kecskeméten ez a kertépítési munkáim gyakori formája.</p>
    </div>
  </div>
</section>

<section class="loc-gallery">
  <div class="wrap">
    <h2>Munkáim Kecskeméten</h2>
    <div class="lg-grid">
      <div class="lg-item"><img class="photo" src="preview/img/alapozas-zuzottko-fedett-terasz-kecskemet.webp" alt="Zúzottkő ágyazat, fedett terasz, Kecskemét"><span>Zúzottkő ágyazat, fedett terasz</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/alapozas-zuzottko-haznal-kecskemet.webp" alt="Zúzottkő ágyazat háznál, Kecskemét"><span>Zúzottkő ágyazat háznál</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/alapozas-zuzottko-szegellyel-kecskemet.webp" alt="Zúzottkő, szegéllyel, Kecskemét"><span>Zúzottkő, szegéllyel</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/foldmunka-tormelek-rendezes-kecskemet.webp" alt="Törmelék, zúzottkő rendezése, Kecskemét"><span>Törmelék, zúzottkő rendezése</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-terasz-deszkazat-elokeszitese-kecskemet.jpg" alt="Terasz deszkázat előkészítése, Kecskemét"><span>Terasz deszkázat előkészítése</span></div>
      <div class="lg-item"><img class="photo" src="preview/img/kertepites-barna-trapezlemez-keritese-kecskemet.jpg" alt="Barna trapézlemez kerítés, Kecskemét"><span>Barna trapézlemez kerítés</span></div>
    </div>
    <a class="btn btn-outline" href="referenciak.html">Összes munka megtekintése →</a>
  </div>
</section>

<section class="loc-cta">
  <h2>Kérj ingyenes árajánlatot Kecskeméten</h2>
  <p>Nincs kötelezettség, ingyenes helyszíni felmérés — 24 órán belül visszahívlak.</p>
  <a class="btn btn-primary" href="index.html#contact">Árajánlatot kérek</a>
</section>

<footer class="sitefooter">
  <div class="fwrap">
    <div class="fcol fbrand">
      <div class="logo" style="color:#F4F2ED;">TÓTH<span style="color:#9CB58E;">·</span>TAMÁS</div>
      <p>Földmunka, betonozás és térkövezés — precízen, garanciával, Heves megyében és Budapesten.</p>
    </div>
    <div class="fcol">
      <h4>Oldal</h4>
      <a href="index.html#services">Szolgáltatások</a>
      <a href="referenciak.html">Munkáim</a>
      <a href="index.html#ab1">Rólam</a>
      <a href="index.html#faq">GYIK</a>
      <a href="index.html#contact">Kapcsolat</a>
    </div>
    <div class="fcol">
      <h4>Elérhetőség</h4>
      <a href="tel:+36209796081">+36 20 979 6081</a>
      <a href="mailto:vitotamas1999@gmail.com">vitotamas1999@gmail.com</a>
      <span>Heves megye és Budapest</span>
    </div>
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
  <div class="fbottom">© 2026 Tóth Tamás — Minden jog fenntartva.</div>
</footer>

</body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add kecskemet.html
git commit -m "Add Kecskemét location landing page"
```

---

### Task 6: "Térségek" lábléc-oszlop hozzáadása a 4 meglévő oldalhoz

**Files:**
- Modify: `index.html`
- Modify: `referenciak.html`
- Modify: `impresszum.html`
- Modify: `adatkezeles.html`

- [ ] **Step 1: `index.html` — keresd meg a lábléc "Szolgáltatások" oszlopát és utána szúrd be az új oszlopot**

Keresendő (a jelenlegi lábléc "Szolgáltatások" `fcol`-ja és a záró `</div>` a `fwrap` előtt):

```html
    <div class="fcol">
      <h4>Szolgáltatások</h4>
      <span>Földmunka</span>
      <span>Alapozás</span>
      <span>Betonozás</span>
      <span>Térkövezés</span>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
```

Cseréld erre:

```html
    <div class="fcol">
      <h4>Szolgáltatások</h4>
      <span>Földmunka</span>
      <span>Alapozás</span>
      <span>Betonozás</span>
      <span>Térkövezés</span>
    </div>
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
```

- [ ] **Step 2: `referenciak.html` — ugyanaz a csere**

Keresd meg ugyanazt a `Szolgáltatások` / `Jogi` `fcol` párost `referenciak.html`-ben, és illeszd be közéjük ugyanazt a "Térségek" blokkot, mint fent.

- [ ] **Step 3: `impresszum.html` — "Térségek" oszlop beszúrása**

Az `impresszum.html` láblécében nincs "Szolgáltatások" oszlop, csak "Oldal", "Elérhetőség", "Jogi". Keresd meg:

```html
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
```

Cseréld erre:

```html
    <div class="fcol">
      <h4>Térségek</h4>
      <a href="cegled.html">Cegléd</a>
      <a href="nagykoros.html">Nagykőrös</a>
      <a href="abony.html">Abony</a>
      <a href="kecskemet.html">Kecskemét</a>
    </div>
    <div class="fcol">
      <h4>Jogi</h4>
      <a href="impresszum.html">Impresszum</a>
      <a href="adatkezeles.html">Adatkezelés</a>
    </div>
  </div>
```

- [ ] **Step 4: `adatkezeles.html` — ugyanaz a csere, mint az Impresszumnál**

- [ ] **Step 5: Commit**

```bash
git add index.html referenciak.html impresszum.html adatkezeles.html
git commit -m "Add Térségek footer column linking the 4 city pages"
```

---

### Task 7: `sitemap.xml` frissítése

**Files:**
- Modify: `sitemap.xml`

- [ ] **Step 1: Szúrd be a 4 új `<url>` blokkot a záró `</urlset>` elé**

A fájl jelenlegi tartalma két `<url>` blokkot tartalmaz (főoldal, referenciák). Illeszd be utánuk, a `</urlset>` elé:

```xml
  <url>
    <loc>https://tothtamasepito.hu/cegled.html</loc>
    <changefreq>monthly</changefreq>
    <priority>0.7</priority>
  </url>
  <url>
    <loc>https://tothtamasepito.hu/nagykoros.html</loc>
    <changefreq>monthly</changefreq>
    <priority>0.7</priority>
  </url>
  <url>
    <loc>https://tothtamasepito.hu/abony.html</loc>
    <changefreq>monthly</changefreq>
    <priority>0.7</priority>
  </url>
  <url>
    <loc>https://tothtamasepito.hu/kecskemet.html</loc>
    <changefreq>monthly</changefreq>
    <priority>0.7</priority>
  </url>
```

- [ ] **Step 2: Commit**

```bash
git add sitemap.xml
git commit -m "Add 4 city landing pages to sitemap.xml"
```

---

### Task 8: `llms.txt` frissítése

**Files:**
- Modify: `llms.txt`

- [ ] **Step 1: Az "Oldalak" szekció végére illeszd be**

```markdown
- [Cegléd](https://tothtamasepito.hu/cegled.html): földmunka, alapozás, betonozás, térkövezés és kertépítés Cegléden
- [Nagykőrös](https://tothtamasepito.hu/nagykoros.html): földmunka, alapozás, betonozás, térkövezés és kertépítés Nagykőrösön
- [Abony](https://tothtamasepito.hu/abony.html): földmunka, alapozás, betonozás, térkövezés és kertépítés Abonyban
- [Kecskemét](https://tothtamasepito.hu/kecskemet.html): földmunka, alapozás, betonozás, térkövezés és kertépítés Kecskeméten
```

- [ ] **Step 2: Commit**

```bash
git add llms.txt
git commit -m "Add 4 city pages to llms.txt"
```

---

### Task 9: Szinkronizálás a `docs/` build mappába és ellenőrzés

**Files:**
- Create: `docs/location.css`, `docs/cegled.html`, `docs/nagykoros.html`, `docs/abony.html`, `docs/kecskemet.html`
- Modify: `docs/index.html`, `docs/referenciak.html`, `docs/impresszum.html`, `docs/adatkezeles.html`, `docs/sitemap.xml`, `docs/llms.txt`

- [ ] **Step 1: Másold át a fájlokat, és cseréld a képútvonalakat**

```bash
cp location.css cegled.html nagykoros.html abony.html kecskemet.html docs/
cp index.html referenciak.html impresszum.html adatkezeles.html sitemap.xml llms.txt docs/
sed -i 's/preview\/img\//img\//g' docs/cegled.html docs/nagykoros.html docs/abony.html docs/kecskemet.html docs/index.html docs/referenciak.html
```

- [ ] **Step 2: Ellenőrizd, hogy nem maradt `preview/img/` hivatkozás a `docs/`-ban**

```bash
grep -c "preview/img" docs/cegled.html docs/nagykoros.html docs/abony.html docs/kecskemet.html
```

Expected: minden fájlnál `0`.

- [ ] **Step 3: Indíts egy helyi statikus szervert és ellenőrizd HTTP-státusszal az új oldalakat**

```bash
node -e "
const http=require('http'),fs=require('fs'),path=require('path');
const types={'.html':'text/html','.webp':'image/webp','.jpg':'image/jpeg','.svg':'image/svg+xml','.xml':'application/xml','.txt':'text/plain','.png':'image/png','.ico':'image/x-icon','.webmanifest':'application/manifest+json','.css':'text/css'};
http.createServer((req,res)=>{
  let p = path.join(__dirname, decodeURIComponent(req.url.split('?')[0]));
  if(p.endsWith('/')) p+='index.html';
  fs.readFile(p,(err,data)=>{
    if(err){res.writeHead(404);res.end('not found');return;}
    res.writeHead(200,{'Content-Type':types[path.extname(p)]||'application/octet-stream'});
    res.end(data);
  });
}).listen(8090,()=>console.log('up'));
" &
sleep 1
for f in cegled.html nagykoros.html abony.html kecskemet.html location.css; do
  echo -n "$f: "; curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8090/$f
done
```

Expected: minden sor `200`-at ad.

- [ ] **Step 4: Ellenőrizd a galéria-képek elérhetőségét is (mind a 4 oldal 6-6 képe)**

```bash
for f in alapozas-zsaluzas-cegled.webp betonozas-kesz-betonlap-cegled.webp terkovezes-nagy-terasz-keritessel-cegled.webp terkovezes-terasz-lepcsovel-cegled.webp kertepites-sargablokk-keritesoszlopokkal-cegled.jpg kertepites-keritesoszlopok-alapozasa-cegled.jpg alapozas-vasalt-alap-nagykoros.webp betonozas-kesz-jarda-nagykoros.webp betonozas-sima-felulet-nagykoros.webp kertepites-lepcsos-keritesfal-nagykoros.webp kertepites-sargablokk-keritesoszlopokkal-nagykoros.jpg kertepites-keritesvaz-tujak-kozott-nagykoros.jpg foldmunka-zuzottko-teritese-abony.webp foldmunka-tereprendezes-abony.webp betonozas-jarda-hosszu-szakasz-abony.webp betonozas-jarda-teglaszegellyel-abony.webp kertepites-fekete-keritespanel-cserjesorral-abony.jpg kertepites-kesz-keritespanel-mezsgyen-abony.jpg alapozas-zuzottko-fedett-terasz-kecskemet.webp alapozas-zuzottko-haznal-kecskemet.webp alapozas-zuzottko-szegellyel-kecskemet.webp foldmunka-tormelek-rendezes-kecskemet.webp kertepites-terasz-deszkazat-elokeszitese-kecskemet.jpg kertepites-barna-trapezlemez-keritese-kecskemet.jpg; do
  code=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:8090/img/$f)
  if [ "$code" != "200" ]; then echo "HIBA: $f -> $code"; fi
done
echo "kész, minden hiányzó kép fentebb listázva (üres kimenet = minden kép megvan)"
```

- [ ] **Step 5: Böngészős vizuális ellenőrzés**

Nyisd meg böngészőben mind a 4 oldalt (`http://localhost:8090/cegled.html` stb.), és ellenőrizd vizuálisan:
- A hero cím és szöveg a megfelelő várost említi
- Az 5 szolgáltatás-blokk megjelenik
- A galéria 6 képe betöltődik, alt-szöveggel
- A lábléc "Térségek" oszlopa mind a 4 városra mutat
- A CTA gomb az `index.html#contact`-ra navigál

- [ ] **Step 6: Állítsd le a helyi szervert**

```bash
kill %1 2>/dev/null
```

- [ ] **Step 7: Commit**

```bash
git add docs/
git commit -m "Sync city landing pages to docs/ build"
```

---

### Task 10: Push

- [ ] **Step 1: Ellenőrizd a teljes `git log` és `git status` állapotot**

```bash
git status --short
git log --oneline -10
```

Expected: `git status --short` üres (minden commitolva), a log az összes fenti commitot mutatja.

- [ ] **Step 2: Push**

```bash
git push origin main
```

Expected: sikeres push, nincs reject/403.

---

## Spec lefedettség — önellenőrzés

- Fájlstruktúra (location.css + 4 HTML fájl) → Task 1–5 ✅
- Oldal-sablon minden eleme (fejléc, hero, szolgáltatás-blokk, szűrt galéria, CTA, lábléc "Térségek" oszlop) → Task 2–5 ✅
- Meglévő 4 oldal láblécének frissítése → Task 6 ✅
- SEO-réteg (title/description/canonical/favicon/OG/JSON-LD Service) → Task 2–5 ✅
- `sitemap.xml`, `llms.txt` → Task 7–8 ✅
- `docs/` szinkron + tesztelés (HTTP-státusz, kép-ellenőrzés, vizuális check) → Task 9 ✅
- Terjedelem-korlátok betartva: nincs blog, nincs query-string tracking, nincs dropdown nav, nincs önálló űrlap oldalanként, `index.html`/`referenciak.html` vizuális stílusa nem változik ✅
