# Blokka – Konzept & Architektur

> Arbeitstitel: **Blokka** (= *blocken* + *Quokka*). Eine App gegen Social-Media-Sucht und
> Doom-Scrolling: harte Zeitlimits pro App, kein „Noch 5 Minuten“-Knopf, dafür eine Streak
> wie bei Duolingo und ein Quokka-Maskottchen, das mitfiebert.

---

## 1. Die wichtigste Erkenntnis vorweg: „nur eine Web-App“ reicht technisch nicht

Eine reine Web-App (auch als PWA) **kann andere Apps weder messen noch blockieren**. Das
erlauben iOS und Android nur nativen Apps über spezielle System-Schnittstellen:

| Plattform | Nötige System-API | Was sie kann |
|---|---|---|
| **iOS** (ab 16) | Screen Time API: `FamilyControls`, `ManagedSettings`, `DeviceActivity` | Apps/Kategorien/Websites sperren, Limits per Schwellwert, Zeitfenster, eigener Sperrbildschirm |
| **Android** | `UsageStatsManager`, `AccessibilityService`, Overlay-Berechtigung, ggf. Device Admin | Nutzungszeit messen, Vordergrund-App erkennen, Sperr-Overlay anzeigen, Inhalte *innerhalb* von Apps erkennen (z. B. Shorts) |

**Lösung, die deinem Wunsch am nächsten kommt:** Eine Web-App als Kern (UI, Logik, Backend)
und **Capacitor** als Hülle für beide Stores. Die Oberfläche schreibst du einmal (ca. 80–85 %
des Codes). Dazu kommt **ein natives Plugin „Guard“ pro Plattform** (Swift für iOS, Kotlin für
Android), das nur das Überwachen/Blockieren übernimmt und eine schmale Schnittstelle anbietet:

```ts
Guard.requestPermissions()
Guard.pickApps()                      // iOS: System-Auswahldialog, Android: eigene Liste
Guard.applyRules(rules: LimitRule[])  // Limits, Zeitfenster, Wochentage
Guard.getTodayUsage()                 // Android: Minuten; iOS: nur "Limit erreicht ja/nein"
Guard.onEvent(cb)                     // limitReached, appInstalled, permissionRevoked, ...
```

Die Web-Version (im Browser) bleibt trotzdem nützlich: Dashboard, Streak, Buddy-Freigaben,
Onboarding – und zum schnellen Testen der Oberfläche.

---

## 2. Architektur

```
┌──────────────────────────── Smartphone ────────────────────────────┐
│  Capacitor-App                                                     │
│  ┌──────────────────────────────┐   ┌───────────────────────────┐  │
│  │ Web-UI (React/Vue + Vite)    │◄─►│ Guard-Plugin (nativ)      │  │
│  │ Streak, Limits, Quokka, Buddy│   │ iOS: Screen-Time-Extens.  │  │
│  └──────────────┬───────────────┘   │ Android: Service+Overlay  │  │
│                 │                   └───────────────────────────┘  │
└─────────────────┼──────────────────────────────────────────────────┘
                  │ HTTPS
┌─────────────────▼──────────────────┐
│ Supabase (Backend-as-a-Service)    │
│ Postgres · Auth · Row Level Sec.   │
│ Edge Functions (Streak-Abschluss,  │
│ Buddy-Freigaben, Push)             │
└────────────────────────────────────┘
```

**Grundprinzip gegen Schummeln:** Blockiert wird **lokal auf dem Gerät** (funktioniert auch
offline). Die **Streak wird auf dem Server** berechnet, mit Serverzeit – Uhr-Verstellen bringt
nichts. Das Gerät meldet Ereignisse + einen täglichen „Heartbeat“; fehlt der, ist der Tag
„nicht verifiziert“.

### Empfohlener Stack

| Teil | Wahl | Warum |
|---|---|---|
| Frontend | **React + TypeScript + Vite** (Alternativ Vue/Svelte) | Größtes Ökosystem, Capacitor-tauglich |
| Native Hülle | **Capacitor** | Echte Web-App bleibt Web-App; native Plugins ergänzbar |
| Backend + DB + Auth | **Supabase** | Postgres (Streak-Logik in SQL gut lösbar), Login, Row Level Security, großzügiges Gratis-Kontingent |
| Web-Hosting | **Cloudflare Pages** oder **Vercel** | Gratis, automatisches Deployment bei jedem Git-Push |
| Push | Firebase Cloud Messaging (Android + iOS via APNs) | Gratis |

Alternative „Google-Weg“ wie bei deinem Kollegen: **Firebase** (Firestore + Auth + Hosting +
Functions). Funktioniert ebenso, ist aber eine Dokumenten-DB – Auswertungen wie „Tage in Folge
ohne Verstoß“ sind in Postgres angenehmer. Ich empfehle Supabase.

### Kosten bis zum Livebetrieb

| Posten | Kosten |
|---|---|
| Supabase Free, Cloudflare Pages, FCM | 0 € (Hinweis: Supabase-Free-Projekte pausieren nach ~1 Woche ohne Aktivität) |
| Google Play Developer-Konto | einmalig 25 $ |
| Apple Developer Program | 99 $/Jahr (erst nötig, wenn Freunde über TestFlight testen sollen) |
| Domain (optional) | ~10–15 €/Jahr |

### Mit Freunden testen

- **Web-Version:** Link verschicken – sofort, gratis (zeigt UI & Streak, blockiert aber nichts).
- **Android:** APK direkt verschicken oder Google-Play-„Interner Test“. Achtung: neue private
  Entwicklerkonten brauchen vor dem öffentlichen Release einen geschlossenen Test mit
  mindestens 12 Testern über 14 Tage – die Freunde-Testgruppe passt perfekt dafür.
- **iOS:** Auf dem eigenen iPhone direkt aus Xcode. Für Freunde via TestFlight braucht es das
  bezahlte Konto **und** die bei Apple zu beantragende Berechtigung
  *Family Controls (Distribution)*. **Diesen Antrag früh stellen** – die Prüfung kann dauern.

---

## 3. Was geht wo? (ehrliche Machbarkeits-Matrix)

| Feature | Android | iOS |
|---|---|---|
| Tageslimit pro App | ✅ | ✅ (Schwellwert-Ereignis) |
| Gesamtlimit für mehrere Apps / Kategorie | ✅ | ✅ |
| Sperrzeiten (z. B. 22–6 Uhr) | ✅ | ✅ |
| Andere Limits je Wochentag (Sonntag +10 Min) | ✅ | ✅ |
| Eigener Sperrbildschirm mit Quokka, **ohne** „5 Min mehr“ | ✅ (Overlay) | ✅ (ShieldConfiguration-Extension) |
| Exakte Minuten im Dashboard | ✅ | ⚠️ nur lokal anzeigbar (DeviceActivityReport), nicht an Server sendbar |
| YouTube erlauben, **Shorts sperren** | ✅ via Accessibility (erkennt Shorts-Ansicht; muss bei YouTube-Updates gepflegt werden) | ❌ in der YouTube-App nicht möglich. Workaround: YouTube-App sperren, youtube.com in Safari erlauben und `/shorts` per Safari-Content-Blocker sperren |
| Inkognito-Modus verhindern | ⚠️ Chrome-Inkognito per Accessibility erkennen & schließen | ✅ für Safari: aktivierter Webfilter deaktiviert Privat-Modus; Dritt-Browser ggf. ganz sperren |
| Neue App installiert → sofort Limit abfragen | ✅ (Install-Ereignis → Benachrichtigung „Limit festlegen?“) | ❌ keine Benachrichtigung möglich. Lösung: **Kategorien** sperren (z. B. „Soziale Netzwerke“) – neue Apps sind automatisch erfasst |
| Limit bleibt nach Löschen + Neuinstallation | ✅ (Paketname ist stabil, Regeln liegen in Blokka + Server) | ✅ (Regeln liegen bei Blokka, App-Token bleiben auf dem Gerät gültig) |
| Deinstallation von Blokka erschweren | ⚠️ Device Admin / Accessibility kann Einstellungen sperren | ⚠️ Entzug der Berechtigung erfordert Face ID; optional `denyAppRemoval` |

Wichtige Besonderheiten:

- **iOS gibt App-Namen nicht heraus.** Die App wird als verschlüsseltes Token übergeben; den
  Namen/Icon kann nur das System anzeigen. Der Server kennt also bei iOS nicht „Instagram“,
  sondern ein Token. Das Datenmodell (unten) berücksichtigt das.
- **Google Play ist streng bei Accessibility-Nutzung.** Man muss den Zweck im Store
  deklarieren und in der App deutlich offenlegen. Digital-Wellbeing-Apps (z. B. ScreenZen,
  one sec) werden zugelassen – das ist machbar, aber einplanen.
- Wer wirklich will, kann jede Sperre umgehen (Berechtigung entziehen, Werkseinstellungen).
  Ziel ist nicht Unmöglichkeit, sondern **genug Reibung + ein Grund, es nicht zu tun** – genau
  dafür ist die Streak da: **Jeder Umgehungsversuch wird erkannt und kostet die Flamme.**

---

## 4. Gamification: Streak, Eukalyptus & Quokka

### Regeln für den Tag

Ein Tag (lokale Mitternacht bis Mitternacht, Zeitzone wird beim Tag fixiert) ist **sauber**, wenn
es keinen Verstoß gab. Verstöße sind:

1. Ein Limit wurde trotz Sperre überschritten (z. B. Overlay umgangen)
2. „Heute kein Limit“ für eine App aktiviert
3. Ein Limit wurde **erhöht** oder eine App/Sperrzeit **entfernt**
4. Schutz deaktiviert: Berechtigung entzogen, Dienst beendet, kein Heartbeat

Strenger machen ist immer erlaubt und kostenlos: Limit senken, App hinzufügen, Sperrzeit
verlängern.

### Lockern: zwei Wege (Vorschlag)

Vor jeder Lockerung erscheint ein Dialog mit traurigem Quokka:

> „Wenn du das Limit für Instagram von 10 auf 60 Minuten erhöhst, **erlischt deine 23-Tage-Flamme.**“
>
> [ Sofort, Flamme verlieren ] [ Ab übermorgen, Flamme behalten ] [ Abbrechen ]

- **Sofort** → zählt als Verstoß (Eukalyptusblatt wird verbraucht, falls vorhanden, sonst Streak = 0).
- **Mit Bedenkzeit** → Änderung wird erst nach 48 h wirksam, kein Verstoß. Genau diese
  Wartezeit bricht den Sucht-Impuls („Ich will *jetzt*“), lässt aber echte Anpassungen zu.

### Eukalyptusblätter (= Streak-Freeze)

Quokkas lieben Blätter – daher **Eukalyptusblätter** statt „Streak-Freeze“:

- 2 Blätter pro Monat, werden am Monatsersten aufgefüllt (max. Vorrat 2).
- Ein Verstoß verbraucht automatisch ein Blatt → Streak bleibt erhalten (der Tag zählt aber
  nicht als „geschafft“).
- Optional: +1 Blatt als Belohnung für 30 saubere Tage (Premium: höherer Vorrat).

### Zwei Ebenen von Streaks

- **Globale Flamme:** alle Apps eingehalten (die Haupt-Zahl auf dem Startbildschirm).
- **App-Abzeichen:** „Instagram: 41 Tage im Limit“ – motiviert, auch wenn die globale Flamme
  gerade neu startet.

### Das Quokka-Maskottchen (Name-Idee: „Koko“)

| Stimmung | Wann |
|---|---|
| 😊 Strahlend (Standard – Quokkas gelten als „glücklichste Tiere der Welt“) | Streak läuft |
| 🤩 Stolz mit Konfetti | Meilensteine: 7, 30, 100, 365 Tage |
| 😟 Besorgt | 80 % eines Limits erreicht |
| 😴 Schlafend mit Nachtmütze | Sperrzeit 22–6 Uhr – erscheint auf dem Sperrbildschirm |
| ✋ Mit Stoppschild | Sperrbildschirm bei erreichtem Limit |
| 😢 Traurig, Blatt fällt | Verstoß / Streak verloren |
| 🌱 Mutig, „Neustart“ | Erster Tag nach Streak-Verlust (positiv bleiben!) |

Umsetzung: ein SVG-Charakter mit austauschbaren Augen/Mund/Accessoires (oder Rive/Lottie für
Animationen). So lässt sich jede Stimmung in Web, iOS-Sperrbildschirm und Android-Overlay
gleich darstellen.

### Weitere Ideen gegen Doom-Scrolling

- **Atempause vor dem Öffnen** (wie „one sec“): Beim Öffnen von Instagram erst 5 Sekunden
  Quokka-Atemübung – oft reicht das, um nicht reinzugehen. (Android leicht, iOS per
  Kurzbefehle-Automation umsetzbar.)
- **„Zurückgewonnene Zeit“:** „Diese Woche hast du 4 h 20 min zurückgewonnen = 2 Bücher-Kapitel.“
- **Freunde-Flammen:** Streaks von Freunden sehen, gemeinsame Challenges.

---

## 5. Buddy-Modus (Vertrauensperson)

Optional bei der Einrichtung: Man lädt eine Vertrauensperson per Link ein (braucht keine eigene
App – die Web-Version reicht).

- **Variante PIN:** Buddy legt einen PIN fest; Lockerungen sind nur mit PIN möglich.
- **Variante Anfrage (empfohlen):** Nutzer stellt eine Anfrage („Ich brauche heute 20 Min
  mehr YouTube für ein Tutorial“), Buddy bekommt Push/Mail und gibt frei oder lehnt ab.
  Vorteil: kein PIN, der heimlich abgeschaut wird, und Buddy sieht den Grund.
- **Vom Buddy genehmigte Ausnahmen kosten keine Flamme** – das ist die Belohnung dafür, sich
  einen Buddy zu holen.
- Notfall-Ausweg, falls der Buddy nicht mehr erreichbar ist (Trennung o. Ä.): Buddy-Modus
  verlassen geht nur mit 7 Tagen Wartezeit + Streak-Verlust.

---

## 6. Konfigurationsmodell

Eine **Regel** gilt für eine App, eine Gruppe von Apps oder eine Kategorie:

```jsonc
{
  "target": { "type": "app", "android": "com.instagram.android", "iosToken": "…" },
  "dailyLimitMin": 10,
  "weekdayOverrides": { "sun": 20, "sat": 15 },   // Sonntag gönnen
  "blockedWindows": [ { "from": "22:00", "to": "06:00", "days": "all" } ],
  "subFeatures": { "youtubeShorts": "blocked" }   // nur Android
}
```

Plus ein optionales **Gesamtlimit** über alle überwachten Apps (z. B. 45 Min/Tag).

---

## 7. Datenbank-Entwurf (Postgres / Supabase)

```sql
create table profiles (
  id uuid primary key references auth.users,
  display_name text,
  timezone text not null default 'Europe/Berlin',
  created_at timestamptz default now()
);

create table devices (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles not null,
  platform text check (platform in ('ios','android','web')),
  last_heartbeat_at timestamptz
);

-- Eine überwachte App; Android über Paketname, iOS über Token (gerätegebunden)
create table targets (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles not null,
  kind text check (kind in ('app','category','web_domain','group')),
  android_package text,
  ios_token text,
  device_id uuid references devices,
  label text
);

create table limit_rules (
  id uuid primary key default gen_random_uuid(),
  target_id uuid references targets not null,
  daily_limit_min int,
  weekday_overrides jsonb default '{}',
  blocked_windows jsonb default '[]',
  sub_features jsonb default '{}',
  valid_from timestamptz not null default now(),   -- für 48-h-Bedenkzeit
  valid_to timestamptz
);

create table violations (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles not null,
  target_id uuid references targets,
  day date not null,
  reason text check (reason in ('limit_exceeded','no_limit_today','rule_loosened',
                                'protection_disabled','missing_heartbeat')),
  approved_by_buddy boolean default false,
  created_at timestamptz default now()
);

create table day_results (
  user_id uuid references profiles,
  day date,
  status text check (status in ('clean','frozen','broken','unverified')),
  primary key (user_id, day)
);

create table streaks (
  user_id uuid primary key references profiles,
  current int default 0,
  longest int default 0,
  freezes_left int default 2,
  freezes_refill_at date
);

create table buddies (
  user_id uuid references profiles,
  buddy_user_id uuid references profiles,
  mode text check (mode in ('pin','request')),
  pin_hash text,
  primary key (user_id, buddy_user_id)
);

create table change_requests (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles,
  payload jsonb not null,
  reason text,
  status text default 'pending' check (status in ('pending','approved','denied')),
  decided_at timestamptz
);
```

Ein **nächtlicher Job** (Supabase Edge Function per Cron) schließt für jeden Nutzer in dessen
Zeitzone den Vortag ab: Verstöße prüfen → Blatt verbrauchen oder Streak zurücksetzen →
`day_results` + `streaks` aktualisieren → Push mit passendem Quokka.

Alle Tabellen bekommen **Row Level Security** („jeder sieht nur seine Daten, Buddy nur die
Anfragen an ihn“).

---

## 8. Abo-Modell (später)

| Gratis | Premium (z. B. 3,99 €/Monat oder 29,99 €/Jahr) |
|---|---|
| Bis zu 3 Apps mit Limit | Unbegrenzt Apps & Kategorien |
| Globale Streak, 2 Blätter/Monat | Wochentags-Regeln, Sperrzeiten, Gesamtlimit |
| | Buddy-Modus, Shorts-Blocker, Statistiken, extra Blätter |

Technisch: **RevenueCat** (gratis bis ~2.500 $ Monatsumsatz) übernimmt Käufe in beiden Stores.
Achtung: Digitale Abos in der App müssen über Apple/Google abgerechnet werden (15–30 % Gebühr;
15 % im ersten Jahr bzw. für kleine Entwickler).

---

## 9. Name

Wichtig vorweg: **Die Reihenfolge im App Store ist nicht alphabetisch.** Ranking entsteht aus
Suchbegriffen (Titel + Untertitel + Keywords), Downloads und Bewertungen. Ein Name mit „Q“
schadet also nicht. Entscheidend ist ein guter **Untertitel**, z. B.
„Blokka – Bildschirmzeit & App-Limit“.

Vorschläge (alle noch auf Marke/Domain zu prüfen – DPMA, EUIPO, App Stores):

| Name | Idee |
|---|---|
| **Blokka** ⭐ | *blocken* + *Quokka*, kurz, beginnt mit B, international aussprechbar |
| **Bokka** | noch kürzer, verspielt |
| **Quokki** | direkt, süß, eindeutiges Maskottchen |
| **Achtsam Quokka** | beginnt mit A, beschreibt den Zweck |
| **Bremsi** | deutsch, frech – „bremst“ das Scrollen |

---

## 10. Fahrplan

| Phase | Inhalt | Ergebnis |
|---|---|---|
| **0 – Setup** (1 Woche) | Eigenes Repo, React+Vite+Capacitor, Supabase-Projekt, CI-Deploy auf Cloudflare Pages, Apple-Entitlement **beantragen** | Leere App läuft im Web, auf Android und iPhone |
| **1 – Kern-UI** | Onboarding, Limits einstellen, Streak-Anzeige, Quokka-Stimmungen, Lockerungs-Dialog, Blätter | Web-Prototyp für Freunde (Streak per Selbstauskunft) |
| **2 – Android-Guard** | UsageStats, Overlay-Sperre, Sperrzeiten, Install-Erkennung, Heartbeat | Echte Sperren auf Android, Test mit 12+ Freunden |
| **3 – iOS-Guard** | FamilyControls-Auswahl, DeviceActivity-Monitor, Shield mit Quokka | Echte Sperren auf iOS, TestFlight |
| **4 – Buddy & Extras** | Buddy-Anfragen, Shorts-Blocker (Android), Inkognito-Sperre, Push | Feature-komplett für Beta |
| **5 – Launch** | Datenschutz (DSGVO – Nutzungsdaten sind sensibel!), Store-Texte, RevenueCat-Abo | Release |

Empfehlung: **Android zuerst**, weil günstiger, ohne Apple-Freigabe testbar und technisch
mächtiger (Shorts, Inkognito, Install-Erkennung). Parallel den Apple-Antrag stellen.
