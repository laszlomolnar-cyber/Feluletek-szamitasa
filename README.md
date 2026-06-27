# Felületek felmérése

Egyszerű, böngészőben futó alkalmazás ingatlanok **terület- és felületfelméréséhez**, amely a végén egy igényes **Word (.docx) jegyzőkönyvet** készít.

## Mit tud?

- Helyiségenként rögzíti a méreteket (szélesség, hosszúság, belmagasság).
- Kiszámolja az **alapterületet** és a **festendő felületet** (falak + mennyezet).
- Igény szerint **levonja az ajtók és ablakok felületét**.
- Helyiségenként megjegyzéseket lehet fűzni a felméréshez.
- A felmérésből letölthető **Word-dokumentumot** generál táblázattal és összesítéssel.
- QR-kóddal megosztható, és telefonra **alkalmazásként telepíthető** (PWA), offline is működik.

## Használat

Nincs telepítés és nincs build. Csak nyisd meg az `index.html` fájlt a böngészőben (vagy tedd ki egy webszerverre), és kezdődhet a felmérés:

**Mód kiválasztása → ingatlan adatai → helyiségek felvétele → összesítés és Word-letöltés.**

## Felépítés

- `index.html` – maga az alkalmazás (felület, logika, Word-export egyben).
- `docx.js` – a Word-fájlok készítéséhez használt könyvtár (offline működéshez beépítve).
- `qrcode.js` – a megosztáshoz használt QR-kód könyvtár.
- `lotzi-splash.svg` – indító képernyő.
