# Konzept: Testspiel-App für Amateur- und Jugendfußball

Arbeitstitel: **„Testkick“** (Alternativen: *Spielpartner*, *Gegnerfinder*, *Testspielbörse*; vor Festlegung Marken- und Domainrecherche machen)

Stand: Oktober 2026 · Version 0.1 (Diskussionsgrundlage)

---

## 1. Das Problem

Ein Testspiel zu organisieren läuft heute so ab:

1. Man überlegt, welche Vereine in der Nähe in Frage kommen und ob die Mannschaft ungefähr gleich stark ist (Spielklasse, Tabellenplatz auf fussball.de).
2. Man sucht auf der Vereinswebsite die richtige Jugendabteilung und den richtigen Trainer. Die Daten sind oft veraltet oder unvollständig.
3. Man schreibt den Trainer per WhatsApp an oder ruft ihn an, ohne ihn zu kennen.
4. Man stimmt Datum, Uhrzeit, Heim/Auswärts, Platz (Kunstrasen/Rasen), Spielzeit und Schiedsrichter ab. Das dauert oft mehrere Tage.
5. Das Spiel muss in **DFBnet** angelegt bzw. beantragt werden, damit ein Schiedsrichter angesetzt werden kann. Das macht oft nicht der Trainer selbst, sondern der Jugendleiter oder Vereinsadmin.
6. Bei einer Absage geht alles von vorne los.

**Kern des Problems:** Es gibt keinen zentralen Ort, an dem Trainer sagen können *„Wir suchen am Samstag, 14.11., ein Testspiel, können heim spielen, B-Junioren, Leistungsklasse“* und an dem andere Trainer das sehen und mit einem Klick zusagen können.

## 2. Bestehende Angebote (kurzer Marktcheck)

| Angebot | Was es macht | Lücke |
|---|---|---|
| fussball.de / DFBnet | Offizielle Spielpläne, Tabellen, Vereins- und Mannschaftsseiten, Spielverwaltung | Keine Börse für Testspiele, keine Kontaktvermittlung, keine öffentliche API |
| DFB-Teampunkt-App | Festivalbörse für den Kinderfußball (G–E) | Nur Kinderfußball, keine Testspiele ab D-Jugend |
| FuPa | Community, Ergebnisse, Vereinsbörse | Keine strukturierte Testspielsuche mit Terminabgleich |
| WhatsApp-Gruppen, Facebook-Gruppen („Testspielbörse Kreis XY“) | Informelle Gesuche | Unstrukturiert, nicht durchsuchbar, nur lokal |

→ **Die Nische ist frei:** eine strukturierte, regionale Testspielbörse ab D-Jugend bis Senioren, mit Terminabgleich und Kontaktvermittlung.

## 3. Zielgruppen

- **Primär:** Trainer und Betreuer von Jugend- und Seniorenmannschaften im Amateurbereich (Kreis- bis Oberliga).
- **Sekundär:** Jugendleiter und Vereinsadmins. Sie verwalten mehrere Mannschaften und legen Spiele in DFBnet an.
- **Später:** Turnierveranstalter (Hallen- und Sommerturniere), Schiedsrichter-Ansetzer, regionale Sponsoren.

**Beispiel-Persona:** Trainer der B-Junioren (männlich) der DJK Rhede 1957 (Fußballverband Niederrhein, Kreis Rees/Bocholt). Er sucht in der Wintervorbereitung zwei Testspiele gegen gleich starke Gegner im Umkreis von 30 km, am liebsten samstags vormittags, Heimspiel auf Kunstrasen möglich.

## 4. Produktidee in einem Satz

> Trainer stellen **Testspiel-Gesuche** (Datum, Zeitfenster, Heim/Auswärts, Wunschstärke) ein. Die App zeigt **passende Gegner in der Region**. Mit einem Klick geht eine Anfrage raus, **auch an Trainer, die die App noch nicht nutzen** (Einladungslink per WhatsApp/E-Mail). Nach der Zusage erzeugt die App alles, was man für **DFBnet** und den Kalender braucht.

## 5. Funktionen

### 5.1 MVP (Version 1, zum Testen mit Bekannten)

**A. Registrierung und Mannschaftsprofil**
- Anmeldung per **E-Mail-Link (Magic Link)**, ohne Passwort. Optional mit Google oder Apple.
- Verein auswählen (aus einem Vereinsverzeichnis) oder neu anlegen.
- Mannschaft anlegen: Altersklasse (A–E, Senioren, Frauen/Juniorinnen), Mannschaftsnummer (I, II), Spielklasse (z. B. Leistungsklasse, Kreisliga A), optional Tabellenplatz.
- Heimspielstätte: Adresse, Platzart (Rasen, Kunstrasen, Asche), Flutlicht ja/nein.
- Präferenzen: Suchradius (z. B. 40 km), bevorzugte Tage und Uhrzeiten, Spieldauer (z. B. 2×40 min).
- Mehrere Trainer pro Mannschaft möglich (Co-Trainer einladen).

**B. Testspiel-Gesuch („Slot“) einstellen**
- Datum plus Zeitfenster (z. B. Sa 14.11., 10–14 Uhr) oder mehrere Alternativen.
- Heim möglich / auswärts möglich / egal.
- Gewünschte Stärke: „gleich stark“, „etwas stärker“, „etwas schwächer“, „egal“.
- Notiz (z. B. „Kunstrasen, Kabinen vorhanden, Schiri von uns“).
- Sichtbarkeit: öffentlich in der Region oder nur gezielt an ausgewählte Mannschaften.

**C. Börse und Suche**
- Liste und Karte aller offenen Gesuche in der Umgebung, gefiltert nach Altersklasse, Datum, Radius und Stärke.
- Vorschläge: „Diese 5 Mannschaften passen zu deinem Gesuch“, sortiert nach Score (siehe Kapitel 7).
- Auch Mannschaften **ohne aktives Gesuch** werden angezeigt (z. B. alle B-Junioren der eigenen und der Nachbarliga), mit dem Hinweis „noch nicht in der App, jetzt einladen“.

**D. Anfrage → Gegenvorschlag → Zusage**
- Mit einem Klick eine Anfrage an ein Gesuch oder eine Mannschaft senden.
- Der Gegner kann zusagen, ablehnen oder einen Gegenvorschlag machen (anderer Termin, Heim/Auswärts tauschen).
- Status: *angefragt → bestätigt → in DFBnet angelegt → gespielt*. Absagen sind ebenfalls möglich.
- Ist ein Slot vergeben, werden andere offene Anfragen auf diesen Slot automatisch und freundlich abgesagt.

**E. Einladungslinks für Trainer ohne App** (der eigentliche Wachstumsmotor, siehe Kapitel 6)

**F. Nach der Zusage**
- **Kalendereintrag** (.ics-Datei bzw. Abo-Link für Google, Apple, Outlook).
- **DFBnet-Assistent:** Eine Übersicht mit allen Daten, die man zum Anlegen des Freundschaftsspiels braucht (Heimverein, Gastverein, Mannschaftsart, Datum, Anstoß, Spielstätte, Spielzeit, Schiedsrichter gewünscht ja/nein). Die Daten lassen sich kopieren, und es gibt einen Direktlink zum DFBnet-Login. Danach hakt man „in DFBnet angelegt“ ab.
- Optional: Nachricht an den Jugendleiter, falls der das Spiel in DFBnet anlegt (per WhatsApp-Link oder E-Mail).
- **Erinnerungen:** 2 Tage vorher „Steht das Spiel noch? In DFBnet angelegt?“ sowie Anfahrt und Treffpunkt.

### 5.2 Version 2 (nach den ersten Tests)

- **Push-Benachrichtigungen** (Web Push, funktioniert auch auf dem iPhone, sobald die Web-App auf dem Homescreen installiert ist).
- **Chat** pro Spiel (damit nicht alles wieder in WhatsApp landet; WhatsApp bleibt aber als Option).
- **Bewertung nach dem Spiel:** „War der Gegner passend stark?“ und „Würdet ihr wieder gegen sie spielen?“. Das verbessert das Matching (Kapitel 7) und schafft Vertrauen (Fairness, Pünktlichkeit).
- **Vereinsansicht für Jugendleiter:** alle Testspiele aller Mannschaften, Platzbelegung, DFBnet-Status.
- **Platzbelegung:** Die Web-App weiß, wann der eigene Platz frei ist, sodass keine Doppelbuchungen entstehen.
- **Turnier-Modul:** Hallen- und Sommerturniere ausschreiben, Anmeldungen und Spielplan verwalten.
- **Spielerbörse / Gastspieler?** Lieber nicht, wegen Jugendschutz und Datenschutz (siehe Kapitel 9).

### 5.3 Später / Visionen

- Native Apps im App Store und Play Store (aus derselben Codebasis, siehe Kapitel 10).
- Offizielle Kooperation mit einem Landesverband oder mit DFB-Medien (DFBnet-Anbindung).
- Schiedsrichter-Anfrage direkt aus der App an den Kreis-Ansetzer.
- Automatisches Zeitfenster-Matching: „Finde mir bis zum 30.11. drei Testspiele“, und die App schlägt Kombinationen vor.

## 6. Kontaktaufnahme und Einladungslinks: wie geht das realistisch?

### 6.1 Automatisch per WhatsApp schreiben? Lieber nicht.

Technisch geht es mit der **WhatsApp Business Platform (Cloud API)**, aber:
- **Kosten:** Meta rechnet seit Juli 2025 pro Nachricht ab. Für Deutschland sind das grob ca. 0,11 € pro Marketing-Nachricht und ca. 0,05 € pro Utility-Nachricht, dazu oft Gebühren eines Dienstleisters.
- **Opt-in-Pflicht:** WhatsApp erlaubt solche Nachrichten nur an Personen, die vorher eingewilligt haben. Kaltakquise an Nummern von Vereinswebsites verstößt gegen die WhatsApp-Richtlinien (Sperre der Nummer) und gegen deutsches Recht (§ 7 UWG, unerwünschte Werbung, sowie DSGVO).
- Die Nachricht käme von einer fremden Firmennummer und nicht vom Trainerkollegen. Das wirkt wie Spam.

### 6.2 Die bessere Lösung: Der Trainer schickt selbst, die App bereitet alles vor

**„Click-to-Chat“-Links** (`https://wa.me/49170…?text=…`) sind kostenlos und brauchen keine API. Der Trainer tippt auf „Per WhatsApp anfragen“, und sein eigenes WhatsApp öffnet sich mit einer vorformulierten Nachricht:

> Hallo Herr Müller, hier ist Daniel, Trainer der B-Junioren der DJK Rhede. Wir suchen ein Testspiel am Sa, 14.11., 11:00 Uhr. Wir könnten bei uns auf Kunstrasen spielen. Hättet ihr Lust? Ihr könnt hier direkt zu- oder absagen: https://testkick.de/e/AB12CD

Vorteile:
- **Persönlich:** Die Nachricht kommt vom Trainer selbst und nicht von einem Bot. Das ist rechtlich unkritisch und wird viel eher beantwortet.
- **Kostenlos**, funktioniert auf jedem Handy.
- Hinter dem Link `testkick.de/e/AB12CD` liegt eine **Einladungsseite ohne Login-Zwang**. Der Eingeladene sieht Datum, Ort, Gegner und Platz und kann mit einem Klick **zusagen, absagen oder einen anderen Termin vorschlagen**. Erst danach wird er gefragt: „Willst du deine Mannschaft auch eintragen? Dann findest du künftig selbst Gegner.“ So wächst die App ganz natürlich (*viraler Kreislauf*).

Weitere Kanäle mit derselben Mechanik:
- **E-Mail** (`mailto:`-Link oder Versand über die App mit Absender „Daniel (DJK Rhede) via Testkick“).
- **SMS** (`sms:`-Link).
- **Teilen-Button** (Web Share API) für WhatsApp-Gruppen, Telegram, Signal usw.
- **QR-Code**, z. B. für Aushänge oder zum Zeigen am Spielfeldrand.

### 6.3 Woher kommen die Kontaktdaten?

Das ist der heikelste Punkt, rechtlich und technisch:

| Datenart | Quelle | Empfehlung |
|---|---|---|
| Vereine, Mannschaften, Ligen, Tabellen (öffentliche Sportdaten) | fussball.de | **Keine öffentliche API.** Automatisiertes Auslesen (Scraping) ist nach den Nutzungsbedingungen problematisch. Für das MVP: Vereine und Mannschaften von den Nutzern selbst anlegen lassen, ergänzt durch eine **einmalig manuell gepflegte Startliste** für die Test-Region (z. B. Kreise Rees/Bocholt und Borken). Später eine Kooperation oder Lizenz anfragen. |
| Link zur Vereinswebsite bzw. Jugendseite | öffentlich | Unkritisch. Pro Verein den Link zur Kontaktseite speichern, z. B. `djk-rhede.de/…/fussball-jugend/b-jugend`. |
| Name und Handynummer von Trainern auf Vereinsseiten | Vereinswebsites | **Nicht automatisch auslesen und zentral speichern.** Das wären personenbezogene Daten, die zu einem anderen Zweck veröffentlicht wurden (DSGVO Art. 6 und Art. 14: Informationspflicht gegenüber jeder betroffenen Person). |

**Pragmatische Lösung:**
1. Bei nicht registrierten Mannschaften zeigt die App **„Kontakt auf der Vereinsseite“** mit Direktlink an. Der Trainer sieht dort die Nummer und tippt sie in den Einladungsdialog (oder kopiert sie hinein).
2. Die eingegebene Nummer wird nur für **diese eine Einladung** verwendet und nicht in einem öffentlichen Verzeichnis gespeichert.
3. Sobald der eingeladene Trainer die Einladung annimmt, kann er **selbst** entscheiden, ob er sein Profil anlegt und für andere sichtbar wird. Das ist die rechtlich saubere Einwilligung.
4. Optional: Vereine bzw. Jugendleiter können ihre Trainer selbst eintragen (*„Verein beanspruchen“*).

## 7. Matching: Wie findet die App „passend starke“ Gegner?

Für jedes Paar aus eigenem Gesuch und möglichem Gegner berechnet die App einen Score aus:

| Faktor | Gewicht | Datenbasis |
|---|---|---|
| Entfernung (Fahrzeit) | hoch | Adresse der Spielstätte → Koordinaten (OpenStreetMap). Radius-Suche in der Datenbank (PostGIS). |
| Termin passt | K.-o.-Kriterium | Überschneidung der Zeitfenster, Heim/Auswärts kompatibel (einer heim, einer auswärts) |
| Spielstärke | hoch | Spielklassen-Stufe (z. B. Niederrheinliga > Leistungsklasse > Kreisliga) ± Tabellenplatz → **Stärke-Index**. Wird nach jedem Spiel per Bewertung („zu stark / passend / zu schwach“) und Ergebnis nachjustiert (Elo-ähnlich). |
| Wunsch „stärker/schwächer“ | mittel | Abweichung vom gewünschten Stärke-Index |
| Zuverlässigkeit | mittel | Absagequote, Antwortgeschwindigkeit |
| Abwechslung | gering | Gegner, gegen die man kürzlich gespielt hat, rutschen nach unten |

Die Spielklassen-Stufen werden einmalig pro Verband und Altersklasse als Tabelle gepflegt. Mit Niederrhein (FVN) und Westfalen (FLVW) fängt man an, weil Rhede direkt an der Verbandsgrenze liegt und Gegner aus dem Kreis Borken (Westfalen) genauso nah sind.

## 8. DFBnet-Integration: realistische Einschätzung

- **Es gibt keine öffentliche Schnittstelle**, über die eine fremde App Spiele in DFBnet anlegen kann. DFBnet gehört DFB-Medien, und Zugänge bekommen nur berechtigte Vereinsfunktionäre.
- Eine Automatisierung über gespeicherte DFBnet-Zugangsdaten der Nutzer (Bot klickt sich durch) **auf keinen Fall**: Das ist ein Sicherheitsrisiko, verstößt vermutlich gegen die Nutzungsbedingungen und kann jederzeit kaputtgehen.

**Was die App stattdessen tut (MVP):**
1. **DFBnet-Checkliste** pro bestätigtem Spiel mit allen Feldern in der Reihenfolge, in der DFBnet sie abfragt, jeweils mit Kopier-Button.
2. Hinweis auf die **Fristen** im jeweiligen Kreis (Schiri-Anforderung oft einige Tage vorher; wird pro Kreis gepflegt).
3. Wer legt an? Standardmäßig die **Heimmannschaft**. Die App erinnert genau diese Seite und zeigt der Gastmannschaft den Status.
4. Wenn der Trainer selbst keinen DFBnet-Zugang hat: Button „An Jugendleiter weiterleiten“ (WhatsApp/E-Mail mit allen Daten).

**Später:** Mit Nutzerzahlen im Rücken bei DFB-Medien bzw. beim Landesverband anfragen (Kooperation, offizielle Schnittstelle). Das ist auch ein Argument für Investoren oder Förderprogramme.

## 9. Datenschutz, Recht und Jugendschutz

- **Keine Spielerdaten.** Die App verwaltet nur Mannschaften und Trainer (Erwachsene). Damit entfallen die sensibelsten Fragen rund um Minderjährige.
- Hosting in der **EU** (z. B. Supabase-Region Frankfurt), Auftragsverarbeitungsvertrag (AVV) mit allen Dienstleistern.
- **Impressum** und **Datenschutzerklärung** sind Pflicht, sobald die App öffentlich erreichbar ist, auch in der Testphase. Für den privaten Test mit ein paar Bekannten reicht eine einfache Version. Vor dem öffentlichen Start sollte ein Generator (z. B. eRecht24) oder ein Anwalt draufschauen.
- **Cookie-Banner** ist erst nötig, wenn Werbung oder Tracking dazukommt (TDDDG). Ohne Werbung: nur technisch notwendige Cookies, also kein Banner.
- **Sichtbarkeit:** Telefonnummern von Trainern sind standardmäßig **nur für eingeloggte, verifizierte Trainer** sichtbar, nie öffentlich.
- **Verifizierung gegen Fake-Accounts:** (a) Einladung durch einen bereits verifizierten Trainer bzw. Jugendleiter desselben Vereins, (b) Bestätigung durch den Vereinsadmin, (c) im MVP manuell durch dich.
- **Löschen:** Konto und Daten jederzeit selbst löschbar (DSGVO Art. 17).

## 10. Technik

### 10.1 Grundsatzentscheidung: Progressive Web App (PWA)

Eine **PWA** ist eine Website, die sich wie eine App verhält:
- Läuft auf **Android, iPhone und PC** aus **einer** Codebasis.
- Lässt sich auf den **Homescreen installieren** (Icon, Vollbild, ohne Browserleiste).
- **Push-Benachrichtigungen** funktionieren auf Android und, seit iOS 16.4, auch auf dem iPhone (wenn die App auf dem Homescreen installiert ist).
- **Kein App-Store nötig.** Zum Testen schickst du einfach einen Link. Kein Apple-Developer-Account (99 €/Jahr) und kein Review-Prozess.
- Später kann dieselbe App mit **Capacitor** in echte Store-Apps verpackt werden, falls gewünscht.

### 10.2 Empfohlener Stack (alles mit kostenlosem Einstieg)

| Bereich | Empfehlung | Warum | Kosten zum Start |
|---|---|---|---|
| Frontend | **Next.js (React) + TypeScript** | Sehr verbreitet, viele Entwickler, gute PWA-Unterstützung | 0 € |
| UI | **Tailwind CSS + shadcn/ui** | Schnell, modern, gut für Mobilgeräte | 0 € |
| Backend und Datenbank | **Supabase** (PostgreSQL + Auth + Realtime + Storage) | Login (Magic Link, Google, Apple) fertig eingebaut, Zugriffsrechte direkt in der DB (Row Level Security), **PostGIS** für die Umkreissuche, EU-Hosting | 0 € (Free Tier; Projekte pausieren nach 1 Woche Inaktivität, Pro ab ca. 25 $/Monat) |
| Hosting | **Vercel** (oder Cloudflare Pages) | Automatisches Deployment bei jedem Git-Push, Vorschau-Links pro Branch | 0 € (Hobby-Tarif; für kommerzielle Nutzung später Pro) |
| E-Mails | **Resend** oder Brevo | Magic Links, Einladungen, Erinnerungen | 0 € (ca. 3.000 Mails/Monat) |
| Karten und Geodaten | **OpenStreetMap + MapLibre**, Geocoding über Nominatim bzw. Photon | Kostenlos, ohne Google-Abhängigkeit | 0 € |
| Push | **Web Push (VAPID)** | Standard, kein Drittanbieter nötig | 0 € |
| Domain | z. B. `testkick.de` | Wirkt seriöser, ist aber für den Test optional (Vercel-Subdomain reicht) | ca. 10 €/Jahr |
| Fehler-Monitoring | Sentry (Free) | Abstürze bei Testern sehen | 0 € |

**→ Der komplette Test mit bis zu ein paar hundert Nutzern ist für 0 € möglich.**

### 10.3 Architektur (vereinfacht)

```
 Handy / PC (PWA, Next.js)
        │  HTTPS
        ▼
 Vercel (Next.js Server: Seiten, API-Routen, Einladungsseiten)
        │
        ├──► Supabase Auth  (Magic Link, Google, Apple)
        ├──► Supabase Postgres + PostGIS  (Daten, Umkreissuche, Row Level Security)
        ├──► Supabase Realtime  (Live-Status, später Chat)
        ├──► Supabase Edge Functions / Cron  (Erinnerungen, Matching-Vorschläge)
        ├──► Resend  (E-Mails)
        └──► Web Push  (Benachrichtigungen)
```

### 10.4 Datenmodell (Kern)

```
verband        (id, name, kürzel)                               z. B. FVN, FLVW
kreis          (id, verband_id, name)                           z. B. Rees/Bocholt
verein         (id, name, kreis_id, website_url, kontaktseite_url, logo_url, verifiziert)
spielstaette   (id, verein_id, name, adresse, geo_point, platzart, flutlicht)
mannschaft     (id, verein_id, altersklasse, geschlecht, nummer, spielklasse_id,
                staerke_index, heim_spielstaette_id)
spielklasse    (id, verband_id, altersklasse, name, stufe)      stufe → Stärke-Index
profil         (id = auth.user, name, telefon, email, sichtbarkeit)
mannschaft_trainer (mannschaft_id, profil_id, rolle)            Trainer, Co-Trainer, Jugendleiter
gesuch         (id, mannschaft_id, datum, von, bis, heim_ok, auswaerts_ok,
                staerke_wunsch, notiz, status, sichtbarkeit)
anfrage        (id, gesuch_id?, von_mannschaft_id, an_mannschaft_id, vorschlag_termin,
                heim_mannschaft_id, status, nachricht)
einladung      (id, token, anfrage_id, kanal, empfaenger_name, angenommen_am)  ohne Login einsehbar
spiel          (id, heim_mannschaft_id, gast_mannschaft_id, anstoss, spielstaette_id,
                dfbnet_status, schiri_gewuenscht, status, ergebnis)
bewertung      (id, spiel_id, von_mannschaft_id, staerke_passend, fairness, kommentar)
```

### 10.5 Wo entwickeln?

Dieses Repository (`virtual-teaching-landingpage`) ist eine Landingpage für ein anderes Thema. Für die App empfehle ich ein **eigenes, neues Repository** (z. B. `testkick-app`). Dann gibt es eine saubere Trennung, eigenes Vercel-Projekt und eigene Issues. Dieses Konzept liegt vorerst hier unter `docs/testspiel-app/`.

## 11. Finanzierung und Geldverdienen

Kostenlose Basis ist richtig, denn das Netzwerk wächst nur, wenn jeder Trainer mitmacht. Werbung allein trägt aber kaum. Eine grobe Rechnung: 5.000 aktive Trainer × 20 Seitenaufrufe/Monat × ca. 2–5 € pro 1.000 Aufrufe ≈ **200–500 €/Monat**. Dafür sieht die App schlechter aus, und es braucht einen Cookie-Banner. Besser ist ein Mix:

| Modell | Beschreibung | Potenzial | Wann |
|---|---|---|---|
| **Regionale Sponsoren** statt anonymer Werbung | „Testspielbörse Kreis Rees/Bocholt, präsentiert von Sparkasse/Sporthaus XY“. Ein Logo pro Kreis, fester Monatsbetrag. Passt zum Amateurfußball, braucht kein Tracking und keinen Cookie-Banner. | mittel–hoch | ab ca. 100 aktiven Mannschaften in einer Region |
| **Freemium „Verein Pro“** | Basis kostenlos für Trainer. Für Vereine z. B. 5–10 €/Monat: Vereinsübersicht aller Mannschaften, Platzbelegung, Vereinslogo und -seite, Statistiken, Export für DFBnet und Kalender. | hoch (planbar, wiederkehrend) | Version 2 |
| **Turnier-Modul** | Turniere ausschreiben, Anmeldungen und Spielpläne verwalten, Startgeld online kassieren. Kleine Gebühr pro Turnier bzw. Teilnehmer. | mittel | Version 2–3 |
| **Affiliate / Partner** | Trainingsausrüstung, Trikots, Mannschaftsfahrten, Busunternehmen (Provision pro Vermittlung) | gering–mittel | jederzeit |
| **Programmatische Werbung** (z. B. Google AdSense) | Nur für Nutzer ohne Pro-Konto, dezent | gering | optional |
| **Kooperation / Lizenz** mit Verband oder DFB-Medien | Die App als offizielles Tool; Lizenz oder Übernahme | sehr hoch, aber unsicher | wenn Reichweite da ist |
| **Förderung** | z. B. Ehrenamtsförderung, Stiftungen, Digitalisierungsprogramme des Landessportbunds (Förderprogramme prüfen) | einmalig | früh |

**Empfehlung:** In der Startphase komplett **werbefrei und kostenlos** (das schafft Vertrauen und Wachstum). Danach zuerst **regionale Sponsoren**, dann **Verein Pro** und das **Turnier-Modul**.

Zur Größenordnung: In Deutschland gibt es rund 24.000 Fußballvereine mit weit über 100.000 Mannschaften. Schon 2 % der Vereine mit „Verein Pro“ zu 7 €/Monat wären rund 40.000 €/Jahr.

## 12. Fahrplan

| Phase | Inhalt | Dauer (grob, nebenberuflich mit KI-Unterstützung) |
|---|---|---|
| **0 – Klärung** | Offene Fragen (Kapitel 13), Name, Test-Region festlegen, Startliste der Vereine im Umkreis erstellen | 1 Woche |
| **1 – Klickbarer Prototyp** | Die wichtigsten Screens (Gesuch erstellen, Börse, Einladungsseite) als PWA ohne echtes Backend, zum Zeigen | 1–2 Wochen |
| **2 – MVP** | Login, Mannschaftsprofil, Gesuche, Suche mit Umkreis, Anfrage/Zusage, Einladungslinks (WhatsApp/E-Mail), Kalender-Export, DFBnet-Checkliste | 4–6 Wochen |
| **3 – Pilot** | 10–20 Trainer aus deinem Umfeld (DJK Rhede plus Nachbarvereine), Wintervorbereitung als echter Test, Feedback sammeln | 4–8 Wochen |
| **4 – Version 2** | Push, Chat, Bewertungen und Stärke-Index, Jugendleiter-Ansicht | 6–8 Wochen |
| **5 – Ausrollen** | Weitere Kreise, Sponsoren, Verein Pro, ggf. Store-Apps | laufend |

**Gutes Timing:** Die **Wintervorbereitung (Januar/Februar)** und die **Sommervorbereitung (Juli/August)** sind die Hochsaisons für Testspiele. Ein Pilot vor der Wintervorbereitung 2027 wäre ideal.

### Erfolgskennzahlen für den Pilot
- Anteil der Anfragen, die zu einem bestätigten Spiel führen
- Zeit von der ersten Anfrage bis zur Zusage (Ziel: < 24 h statt mehrerer Tage)
- Anteil der eingeladenen Trainer, die sich danach selbst registrieren
- „Würdest du die App einem Trainerkollegen empfehlen?“ (0–10)

## 13. Was ich noch von dir brauche

1. **Name und Domain:** Gefällt dir „Testkick“, oder hast du eine eigene Idee?
2. **Test-Region:** Nur Kreis Rees/Bocholt (FVN) oder gleich auch Kreis Borken (FLVW) und die Niederlande-Grenzregion dazu?
3. **Altersklassen im Pilot:** Nur Jugend ab D, oder auch Senioren und Juniorinnen?
4. **DFBnet-Praxis bei euch:** Legst du Freundschaftsspiele selbst in DFBnet an, oder macht das euer Jugendleiter? Welche Felder werden abgefragt, und welche Fristen gelten im Kreis (für die Checkliste)? Ein Screenshot der Eingabemaske wäre super.
5. **Startliste:** 15–30 Vereine im Umkreis, mit denen du testen möchtest, plus 3–5 Trainer, die du persönlich kennst und die mitmachen würden.
6. **Repository:** Soll ich ein neues Repository für die App anlegen (Empfehlung), oder hier im bestehenden Repo in einem Unterordner starten?
7. **Accounts (kostenlos):** Du brauchst ein GitHub-Konto (hast du), ein Vercel-Konto und ein Supabase-Konto. Ich kann dich beim Einrichten Schritt für Schritt anleiten.
8. **Anspruch:** Eher Hobbyprojekt für die eigene Region oder perspektivisch ein Produkt, mit dem du Geld verdienen willst? Das beeinflusst z. B., ob du eine UG gründest, sobald Sponsoren- oder Abo-Einnahmen fließen.

### Entscheidungen (Stand 22.10.2026)
- **Name:** Testkick.
- **Pilot-Region:** Kreis Rees/Bocholt (FVN) und Kreis Borken (FLVW).
- **Altersklasse im Pilot:** B-Junioren (Jahrgänge 2010/2011).
- **DFBnet:** Testspiele werden von den Trainern selbst in DFBnet angelegt. Die Checkliste orientiert sich an der Eingabemaske (Screenshot folgt).
- **Startliste:** Alle Mannschaften der B-Junioren Kreisliga B, Gruppe 1 (Gruppe von DJK Rhede) und der zweiten Kreisliga-B-Gruppe, siehe `startliste-vereine.csv` (wird noch vervollständigt).
- **Repository:** Neues, eigenes Repository `testkick`.

**Nächster Schritt:** Wenn du die Fragen beantwortet hast, baue ich als Erstes den **klickbaren Prototyp (Phase 1)**. Den kannst du direkt auf deinem Handy installieren und Trainerkollegen zeigen.

---

### Quellen und Hintergründe
- fussball.de, Vereinsseite DJK Rhede 1957: https://www.fussball.de/verein/djk-rhede-1957-niederrhein/-/id/00ES8GN8VS000083VV0AG08LVUPGND5I
- DJK Rhede 57 e.V., Fußball-Jugend: https://www.djk-rhede.de/index.php/fussball-jugend/d-jugend
- DFB-Teampunkt-App (Festivalbörse Kinderfußball): https://www.dfb.de/training-service/teampunkt-app
- WhatsApp-API-Preise Deutschland (Drittanbieter-Übersichten, vor Nutzung mit der offiziellen Meta-Preisliste abgleichen): https://www.engagelab.com/de_DE/blog/whatsapp-business-api-pricing, https://chatmaxima.com/de/whatsapp-api-pricing/germany/
- DFBnet-Überblick (DFB): https://dfb.de/fileadmin/user_upload/DFBnetBroschuere_ENG_FINAL_WEB.pdf
