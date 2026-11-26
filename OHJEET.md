# Resp iPhonelle

Pakkauksessa on kolme tapaa saada Resp puhelimeen:

- **Reitti A** – Koti-valikkosovellus nettisivun kautta, toimii kaikilla koneilla
- **Reitti B** – oikea iOS-sovellus Xcodella, vaatii Macin
- **Reitti C** – oikea iOS-sovellus **Windowsilla ilman Macia ja ilman maksullista jäsenyyttä**, toimii 7 päivää kerrallaan

```
resp-ios/
├── www/                   ← sovellus (kumpikin reitti käyttää tätä)
│   ├── index.html
│   ├── manifest.webmanifest
│   └── kuvakkeet (png)
├── package.json           ← reitit B ja C
├── capacitor.config.json  ← reitit B ja C
├── .github/workflows/     ← reitti C (rakennus pilvessä)
└── OHJEET.md
```

---

## Reitti A: sovellus Koti-valikkoon (Mac tai Windows)

USB-kaapelia ei tarvita.

1. Avaa tietokoneella **app.netlify.com/drop**
2. Raahaa **www**-kansio sivulle
3. Saat osoitteen tyyliin `https://satunnainen-nimi.netlify.app` — lähetä se itsellesi esimerkiksi viestinä
4. Avaa osoite iPhonen **Safarissa** (ei Chromessa)
5. Paina jakopainiketta ja valitse **Lisää Koti-valikkoon**

Resp näkyy nyt Koti-valikossa omalla kuvakkeellaan ja aukeaa koko ruudulle ilman osoitepalkkia. Kamera toimii, koska yhteys on https.

Kun teet muutoksia, raahaa www-kansio uudelleen samalle Netlify-sivulle.

---

## Reitti B: oikea iOS-sovellus Xcodella (vain Mac)

### Tarvitset

- **Xcode** — ilmainen, App Storesta
- **Node.js** — LTS-versio osoitteesta nodejs.org
- **Apple ID** — tavallinen ilmainen tunnus riittää

### Ensimmäinen kerta

1. Pura paketti ja avaa Pääte (Terminal) kansiossa `resp-ios`
2. Asenna riippuvuudet:
   ```
   npm install
   ```
3. Luo iOS-projekti:
   ```
   npm run ios:setup
   ```
4. **Lisää kameralupa** tai kuittiskannaus kaatuu. Avaa tiedosto `ios/App/App/Info.plist` ja lisää ennen viimeistä `</dict>`-riviä:
   ```xml
   <key>NSCameraUsageDescription</key>
   <string>Resp käyttää kameraa kuittien skannaamiseen.</string>
   <key>NSPhotoLibraryUsageDescription</key>
   <string>Resp lukee kuitin kuvan kuvakirjastostasi.</string>
   ```
5. Avaa projekti Xcodessa:
   ```
   npm run ios:open
   ```
6. Valitse vasemmalta **App** ja välilehti **Signing & Capabilities**
   - **Team**: valitse Apple ID:si (tai *Add Account* ja kirjaudu)
   - Jos Xcode valittaa tunnisteesta, vaihda **Bundle Identifier** uniikiksi, esim. `fi.resp.prototyyppi.omanimi`
7. Kytke iPhone kaapelilla ja hyväksy puhelimessa **Luota tähän tietokoneeseen**
8. Valitse Xcoden yläpalkista oma puhelimesi ja paina **▶ (Run)**

### iPhonessa ensimmäisellä kerralla

- **Kehittäjätila:** Asetukset → Tietosuoja ja suojaus → Kehittäjätila → päälle. Puhelin käynnistyy uudelleen.
- **Luota kehittäjään:** Asetukset → Yleiset → VPN ja laitehallinta → Apple ID:si → Luota

Paina Xcodessa ▶ uudelleen, ja Resp aukeaa puhelimessa.

### Kun muutat sovellusta

1. Korvaa `www/index.html` uudella versiolla
2. Aja `npm run ios:sync`
3. Paina Xcodessa ▶

### Hyvä tietää

- **Ilmaisella Apple ID:llä sovellus toimii 7 päivää**, jonka jälkeen se pitää asentaa Xcodesta uudelleen.
- **Testaajille jakamiseen** tarvitaan Apple Developer Program (maksullinen vuosijäsenyys). Silloin sovellus jaetaan TestFlightin kautta, eikä 7 päivän rajaa ole.
- **Windows-koneella** reitti B ei onnistu, koska iOS-sovelluksen rakentaminen vaatii Xcoden. Käytä reittiä A tai C.

---

## Reitti C: iOS-sovellus Windowsilla ilman Macia (7 päivää)

Ideana on, että GitHub rakentaa sovelluksen omalla Mac-palvelimellaan, ja Windows-koneesi allekirjoittaa ja asentaa sen puhelimeen tavallisella Apple ID:lläsi.

### Tarvitset

- **GitHub-tili** – ilmainen, github.com
- **Sideloadly** – ilmainen, sideloadly.io
- **iTunes Applen sivuilta** – ei Microsoft Storesta, Sideloadly ei toimi Storen versiolla
- **Apple ID** ja **USB-kaapeli**

### 1. Rakenna sovellus GitHubissa

1. Kirjaudu GitHubiin ja luo uusi repositorio (**New repository**)
   - **Public** = Mac-rakennukset ilmaisia. Huomaa että koodi näkyy silloin kaikille.
   - **Private** = koodi pysyy piilossa, mutta Mac-rakennus kuluttaa ilmaisia minuutteja moninkertaisella kertoimella
2. Valitse **Add file → Upload files** ja raahaa ikkunaan **koko resp-ios-kansion sisältö**, myös `.github`-kansio. Paina **Commit changes**.
3. Avaa välilehti **Actions**, valitse vasemmalta **Rakenna iOS-sovellus** ja paina **Run workflow**
4. Odota noin 5–10 minuuttia, kunnes rivin eteen tulee vihreä ✓
5. Avaa ajo ja lataa sivun alalaidasta **Resp-ipa**. Pura zip, niin saat tiedoston `Resp.ipa`.

### 2. Asenna puhelimeen Sideloadlyllä

1. Asenna iTunes Applen sivuilta ja sen jälkeen Sideloadly
2. Kytke iPhone kaapelilla ja hyväksy puhelimessa **Luota tähän tietokoneeseen**
3. Avaa Sideloadly: puhelimesi näkyy laitevalikossa
4. Kirjoita Apple ID kenttään **Apple account**
5. Raahaa `Resp.ipa` Sideloadlyn ikkunaan ja paina **Start**
6. Anna Apple ID:n salasana pyydettäessä – se lähetetään vain Applelle

### 3. iPhonessa ensimmäisellä kerralla

- **Luota kehittäjään:** Asetukset → Yleiset → VPN ja laitehallinta → Apple ID:si → Luota
- **Kehittäjätila:** Asetukset → Tietosuoja ja suojaus → Kehittäjätila → päälle, jos puhelin sitä pyytää

### Rajoitukset ilmaisella Apple ID:llä

- **7 päivää:** sen jälkeen sovellus ei aukea. Asenna sama `Resp.ipa` uudelleen Sideloadlyllä samalla Apple ID:llä – älä poista sovellusta välissä. Sideloadlyssä voi myös laittaa automaattisen uusinnan päälle.
- **Enintään 3 sivuladattua sovellusta** voi olla puhelimessa yhtä aikaa.
- **10 sovellustunnistetta viikossa:** jos Sideloadly ilmoittaa rajan täyttyneen, odota muutama päivä tai käytä toista Apple ID:tä.

Monet käyttävät sivulataukseen erillistä Apple ID:tä, jotta päätunnus pysyy erillään.

### Kun muutat sovellusta

Vaihda GitHubissa `www/index.html` uuteen versioon (**Add file → Upload files**), aja **Run workflow** uudelleen ja asenna uusi `Resp.ipa` Sideloadlyllä.

---

## Kaikilla reiteillä

Prototyyppi ei vielä tallenna tietoja. Kun sovellus suljetaan kokonaan, varasto ja ostoslista palaavat alkutilaan. Tallennus kannattaa lisätä ennen testaajille jakamista.
