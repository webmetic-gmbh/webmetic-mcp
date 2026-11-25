<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/webmetic-logo-white.svg">
    <img src="assets/webmetic-logo-black.svg" alt="Webmetic" width="320">
  </picture>
</p>

<h1 align="center">Webmetic MCP</h1>

<p align="center">
  <b>Ihre Website-Besucher direkt in Claude, Cursor und Co.</b><br>
  <i>Your website visitors, right inside Claude, Cursor and friends.</i>
</p>

<p align="center">
  <a href="#deutsch">Deutsch</a> · <a href="#english">English</a> · <a href="https://webmetic.de/mcp">webmetic.de/mcp</a>
</p>

---

## Deutsch

Webmetic identifiziert die Unternehmen, die Ihre Website besuchen. Über
diesen MCP-Server beantwortet Ihr KI-Assistent Fragen wie „Welche
Unternehmen waren diese Woche auf unserer Website?" live aus Ihren
Webmetic-Daten. Die Besucherdaten liest er nur; auf Wunsch findet der
Assistent zusätzlich Ansprechpartner bei einem erkannten Unternehmen,
schaltet E-Mail oder Rufnummer aus Ihrem Credit-Guthaben frei und richtet
Ihr Konto ein: Zielkundenprofil, Ansprechpartner, Benutzer,
Benachrichtigungen. Die Tags Ihrer Unternehmen (etwa „Kunde") zeigt er an,
filtert danach und vergibt oder entfernt sie auf Ihre Anweisung. Ein Konto
legen Sie direkt im Anmeldefenster an.

Der Server läuft gehostet unter `mcp.webmetic.de` — es gibt nichts zu
installieren. Dieses Repository enthält das offizielle Claude-Code-Plugin
und die Einrichtung für alle gängigen Clients.

**Sie brauchen nur die Server-Adresse:** `https://mcp.webmetic.de/mcp` —
angemeldet wird beim ersten Zugriff per Webmetic-Login im Browser.

Alternativ (z. B. für Skripte und CI) funktioniert Ihr API-Schlüssel aus dem
Webmetic-Dashboard (beginnt mit `wmtc_`) als `Authorization`-Header: der
rohe Schlüssel, ohne „Bearer"-Präfix. Behandeln Sie ihn wie ein Passwort.

### Claude Code: Plugin (empfohlen)

Das Plugin bringt den MCP-Server und acht fertige Skills mit:

| Skill | Was er tut | Beispiel-Frage |
|---|---|---|
| `getting-started` | Einrichtung: prüft Konto und Tracking-Code, gibt den Code mit, wenn er fehlt, danach eine echte Auswertung | „Richte Webmetic für mich ein." |
| `wochenbericht` | Wochenbericht: Unternehmen, Top-Seiten, Quellen, Kaufsignale, Empfehlungen | „Erstell mir den Wochenbericht." |
| `hot-leads` | Rangliste der kaufbereitesten Unternehmen ohne Bestandskunden, jede Einstufung mit Verhalten belegt | „Wen soll der Vertrieb diese Woche anrufen?" |
| `abm-check` | Zielkundenliste gegen die Besucher abgleichen, bis zu 50 auf einmal | „Prüf meine Zielkundenliste: Kronfeld Chemie, Hagedorn Papierwerke, Rotorwerk …" |
| `ansprechpartner` | Ansprechpartner bei einem Unternehmen, das die Website besucht hat; E-Mail oder Rufnummer nur auf Wunsch (Credits) | „Wen kann ich bei Musterbau anrufen?" |
| `unternehmens-brief` | Steckbrief zu einem Unternehmen: Profil, Besuchsverlauf, Interessen, Einstufung, nächster Schritt | „Was wissen wir über Musterbau?" |
| `ansprache-vorbereitung` | Belegte Gesprächsaufhänger für die Ansprache eines besuchenden Unternehmens | „Wie spreche ich Musterbau an?" |
| `marketing-empfehlungen` | Höchstens 5 Maßnahmen für das Marketing, jede mit der Zahl, auf der sie beruht: Kampagnen, Quellen, Seiten, KI-Assistenten | „Was sollte unser Marketing als Nächstes tun?" |

```
/plugin marketplace add webmetic-gmbh/webmetic-mcp
/plugin install webmetic@webmetic
```

Beim ersten Zugriff melden Sie sich einmal im Browser mit Ihrem
Webmetic-Login an — fertig. Kein API-Schlüssel, keine Konfiguration.

### Claude Code: nur der Server (ohne Plugin)

```bash
claude mcp add --transport http webmetic https://mcp.webmetic.de/mcp
```

Beim ersten Zugriff öffnet sich das Webmetic-Login im Browser.

### Cursor

In `~/.cursor/mcp.json` (oder projektweise `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "webmetic": { "url": "https://mcp.webmetic.de/mcp" }
  }
}
```

### VS Code (GitHub Copilot, Agent-Modus)

In `.vscode/mcp.json`:

```json
{
  "servers": {
    "webmetic": { "type": "http", "url": "https://mcp.webmetic.de/mcp" }
  }
}
```

### Claude Desktop und claude.ai (Web)

Kein API-Schlüssel nötig:

1. **Einstellungen → Connectoren → Eigenen Connector hinzufügen**
2. URL: `https://mcp.webmetic.de/mcp`
3. **Hinzufügen**, dann **Verbinden**: Es öffnet sich das Webmetic-Login im
   Browser. Anmelden oder dort ein Konto anlegen, „Zugriff erlauben" — fertig.

### ChatGPT

Auch per Anmeldung, ohne API-Schlüssel:

1. In ChatGPT den **Entwicklermodus** aktivieren (Einstellungen →
   Connectoren → Erweitert), dann einen neuen Connector erstellen.
2. URL: `https://mcp.webmetic.de/mcp`, Authentifizierung: **OAuth**.
3. Beim Verbinden mit dem Webmetic-Login anmelden und Zugriff erlauben.

### API-Schlüssel (Skripte, CI, SSO-Konten)

Wo kein Browser-Login möglich ist, funktioniert Ihr API-Schlüssel aus dem
Webmetic-Dashboard als `Authorization`-Header — z. B. in Claude Code:

```bash
claude mcp add --transport http webmetic https://mcp.webmetic.de/mcp \
  --header "Authorization: wmtc_IHR_SCHLUESSEL"
```

In jeder `mcp.json` entsprechend:
`"headers": { "Authorization": "wmtc_IHR_SCHLUESSEL" }`.

Das gilt auch für Single-Sign-On-Konten: Die Anmeldung per Passwort ist
dort noch nicht verfügbar.

### Die ersten Fragen

- „Richte Webmetic für mich ein."
- „Welche Unternehmen waren diese Woche auf unserer Website?"
- „Wer sind unsere heißesten Leads? Sortiere nach Lead-Score."
- „Was hat [Unternehmen] bei uns angeschaut?"
- „Wer hat ein Formular abgeschickt oder auf unsere Telefonnummer geklickt?"
- „Welche Unternehmen kamen über die LinkedIn-Kampagne?"
- „Wer sind die Ansprechpartner im Marketing bei [Unternehmen]?"
- „Schreib mir den Wochenbericht: Besucher, Top-Seiten, Quellen, Branchen."
- „Prüf meine Zielkundenliste: [Namen oder Domains]"

### Was der Server kann

| Tool | Beantwortet |
|---|---|
| `list_visiting_companies` | Wer war da? Mit Filtern: Lead-Score, Branche, Land, besuchte Seite, Quelle, Event, Kampagne, Tag (mit oder ohne) |
| `get_activity_summary` | Top-Seiten, Quellen, Kampagnen, Kaufsignale, Branchen im Zeitraum |
| `get_company_profile` | Wer ist Unternehmen X? Unternehmensdaten und Kontaktinformationen |
| `get_company_activity` | Was hat X angesehen? Kompletter Besuchsverlauf inklusive Events |
| `get_visit_highlights` | Intensive Besuche, wiederkehrende Besucher, Erstbesucher |
| `check_accounts` | ABM: bis zu 50 Zielkunden in einem Aufruf abgleichen |
| `find_contacts` | Ansprechpartner bei einem Unternehmen: Name, Position, welche Kontaktdaten vorliegen. Suche kostenlos, Filter nach Abteilung und Seniorität |
| `reveal_contact` | E-Mail (mit LinkedIn) oder Rufnummer eines Ansprechpartners freischalten: 2 bzw. 8 Credits aus Ihrem Guthaben, der Client fragt vor jedem Aufruf nach |
| `get_account_status` | Ist das Konto bereit? Websites mit erstem und letztem Besuch, Plan, Zielkundenprofil, Ansprechpartner-Einwilligung, Benutzer, Benachrichtigungen, Credits |
| `check_tracking_setup` | Liegt der Tracking-Code auf der Website, gehört er zu diesem Konto, lädt er vor dem Cookie-Banner? Öffnet die eigene Website in einem echten Browser |
| `set_target_profile` | Zielkundenprofil einer Website speichern (Branchen, Größe, Umsatz, Region, Zielseite); Webmetic bewertet alle erkannten Unternehmen neu |
| `enable_contacts` | Ansprechpartner freischalten: Einwilligung speichern und Willkommens-Credits gutschreiben, nach Ihrem ausdrücklichen Ja |
| `invite_user` | Einen Benutzer anlegen; das Passwort kommt per E-Mail |
| `create_email_alert` | Eine E-Mail-Benachrichtigung anlegen: neue, wiederkehrende oder interessierte Unternehmen, an Werktagen zur gewählten Uhrzeit |
| `list_tags` | Welche Tags gibt es? Je Tag die Zahl der Unternehmen und ob er von Hand, automatisch über die API oder von einem Segment gesetzt wurde |
| `tag_companies` | Unternehmen per Domain oder company_id einen Tag geben, bis zu 500 je Aufruf, auch vor dem ersten Besuch. Ein neuer Tag-Name legt den Tag an, der Assistent sagt dann Bescheid |
| `untag_companies` | Einen Tag von Unternehmen entfernen; der Tag selbst bleibt. Der Client fragt vorher nach |
| `search_docs` | Wie geht X in Webmetic? Durchsucht die Dokumentation auf webmetic.de/docs, Deutsch oder Englisch |
| `get_doc_page` | Eine Seite der Dokumentation als Markdown, damit der Assistent mit den genauen Schritten antwortet |
| `read_skill` | Liest einen der Skills oben, damit jeder Client (auch ohne Plugin) die wiederkehrenden Fragen nach derselben Anleitung beantwortet |

Deutsch funktioniert durchgehend: Umlaute, Schreibweisen wie „Muenchen",
deutsche Datumsangaben („01.08.2026", „seit gestern") und deutsche
Unternehmensnamen werden verstanden.

Dazu bringt der Server sechs fertige Prompts mit, in claude.ai im
„+"-Menü des Connectors: `wochenbericht`, `heisse-leads`,
`unternehmens-analyse`, `zielkunden-check`, `ansprechpartner` und
`einrichten` („Richte Webmetic für mich ein").

### Datenschutz

Besucher werden als Unternehmen identifiziert, niemals als Personen;
Formular-Inhalte werden nicht erfasst. Ansprechpartner stammen aus einer
separaten Kontaktdatenbank und werden nur auf Ihre ausdrückliche Frage zu
einem benannten Unternehmen gesucht. Die Besucherdaten liest der Assistent
nur; das Freischalten von Kontaktdaten kostet Credits, und die vier
Einrichtungs-Tools sowie die beiden Tag-Tools ändern Ihr Konto nur auf Ihre
Anweisung. Beides bestätigt der KI-Client vor jedem Aufruf. Zahlung und Abo bleiben im Dashboard. Der Server ist in Deutschland gehostet, der API-Schlüssel
bleibt in Ihrer lokalen Client-Konfiguration.

### Fehlerbilder

| Meldung | Ursache und Lösung |
|---|---|
| „API key was rejected" | Schlüssel prüfen: roh, ohne „Bearer", frisch aus dem Dashboard kopiert |
| „not authorized for domain" | Domain-Schreibweise muss exakt dem Konto entsprechen (`www.`, Subdomain) |
| „0 identified companies" | Kein Fehler: im Zeitraum gab es keine identifizierbaren Besuche. Zeitraum vergrößern; kommt nie etwas an, prüft `check_tracking_setup` den Tracking-Code |
| „The contact setup is not done yet" | Ansprechpartner im Chat freischalten: Der Assistent zeigt die drei Zustimmungen und ruft `enable_contacts` nach Ihrem Ja auf; das schreibt auch die Willkommens-Credits gut |
| „Not enough credits" | Guthaben im Dashboard aufladen; Suchen bleiben kostenlos, nur das Freischalten kostet |
| Verbindung schlägt fehl | `https://mcp.webmetic.de/mcp` exakt so verwenden (mit `/mcp` am Ende) |

Fragen? support@webmetic.de · Dokumentation: https://webmetic.de/mcp

---

## English

Webmetic identifies the companies visiting your website. Through this MCP
server your AI assistant answers questions like "which companies visited
our website this week?" live from your Webmetic data. Visitor data is only
read; on request the assistant also finds contacts at an identified
company, reveals an e-mail or phone number from your credit balance, and
sets up your account: target profile, contacts, users, e-mail alerts. It
shows your company tags (e.g. "Customer"), filters by them, and adds or
removes them on your instruction. A new account is created right in the
sign-in window.

The server is hosted at `mcp.webmetic.de` — there is nothing to install.
This repository ships the official Claude Code plugin and the setup for
all common clients.

**All you need is the server address:** `https://mcp.webmetic.de/mcp` —
you sign in once via your Webmetic login in the browser on first use.

Alternatively (e.g. for scripts and CI) your API key from the Webmetic
dashboard (starts with `wmtc_`) works as the `Authorization` header: the
raw key, no "Bearer" prefix. Treat it like a password.

### Claude Code: plugin (recommended)

The plugin ships the MCP server plus eight ready-made skills:

| Skill | What it does | Example prompt |
|---|---|---|
| `getting-started` | Setup: checks the account and the tracking code, hands over the code when it is missing, then shows real output | "Set up Webmetic for me." |
| `wochenbericht` | Weekly report: companies, top pages, sources, buying signals, recommendations | "Build me the weekly report." |
| `hot-leads` | Ranks the most sales-ready companies, existing customers left out, every rating backed by observed behavior | "Who should sales call this week?" |
| `abm-check` | Matches a target-account list against your visitors, up to 50 at once | "Check my target accounts: Kronfeld Chemie, Hagedorn Papierwerke, Rotorwerk …" |
| `ansprechpartner` | Contacts at a company that visited your website; e-mail or phone only on request (credits) | "Who can I call at Musterbau?" |
| `unternehmens-brief` | Brief on one company: profile, visit history, interests, rating, next step | "What do we know about Musterbau?" |
| `ansprache-vorbereitung` | Grounded talking points for reaching out to a visiting company | "How do I approach Musterbau?" |
| `marketing-empfehlungen` | At most 5 actions for marketing, each with the number behind it: campaigns, sources, pages, AI assistants | "What should our marketing do next?" |

```
/plugin marketplace add webmetic-gmbh/webmetic-mcp
/plugin install webmetic@webmetic
```

On first use you sign in once with your Webmetic login in the browser —
done. No API key, no configuration.

### Claude Code: server only (without the plugin)

```bash
claude mcp add --transport http webmetic https://mcp.webmetic.de/mcp
```

Your browser opens the Webmetic sign-in on first use.

### Cursor / VS Code

Same URL-only configuration as shown in the German section above — no key
needed, sign-in happens in the browser.

### Claude Desktop, claude.ai (web) and ChatGPT

No API key needed: add `https://mcp.webmetic.de/mcp` as a custom
connector (ChatGPT: enable developer mode, authentication **OAuth**),
click connect, and sign in once with your Webmetic login in the browser (or
create the account right there).

### API key (scripts, CI, SSO accounts)

Where no browser sign-in is possible, your API key from the Webmetic
dashboard works as the `Authorization` header:

```bash
claude mcp add --transport http webmetic https://mcp.webmetic.de/mcp \
  --header "Authorization: wmtc_YOUR_KEY"
```

Or in any `mcp.json`: `"headers": { "Authorization": "wmtc_YOUR_KEY" }`.
This also applies to single-sign-on accounts: password sign-in is not
available for SSO yet.

### First questions to ask

- "Set up Webmetic for me."
- "Which companies visited our website this week?"
- "Who are our hottest leads? Sort by lead score."
- "What did [company] look at on our site?"
- "Who submitted a form or clicked our phone number?"
- "Which companies came in through the LinkedIn campaign?"
- "Who are the marketing contacts at [company]?"
- "Write the weekly report: visitors, top pages, sources, industries."
- "Check my target-account list: [names or domains]"

### What the server can do

| Tool | Answers |
|---|---|
| `list_visiting_companies` | Who visited? Filters: lead score, industry, country, visited page, source, event, campaign, tag (with or without) |
| `get_activity_summary` | Top pages, sources, campaigns, buying signals, industries for a period |
| `get_company_profile` | Who is company X? Firmographics and contact details |
| `get_company_activity` | What did X look at? Full visit trail including events |
| `get_visit_highlights` | Intensive visits, returning visitors, first-time visitors |
| `check_accounts` | ABM: match up to 50 target accounts in one call |
| `find_contacts` | Contacts at one company: name, position, which contact data is on file. Search is free, filters by department and seniority |
| `reveal_contact` | Reveal a contact's e-mail (with LinkedIn) or phone number: 2 or 8 credits from your balance, the client asks before every call |
| `get_account_status` | Is the account ready? Websites with first and last visit, plan, target profile, contact consent, users, alerts, credits |
| `check_tracking_setup` | Is the tracking code on the website, does it belong to this account, does it load before the cookie banner? Opens your own website in a real browser |
| `set_target_profile` | Save a website's target profile (industries, size, revenue, region, goal page); Webmetic re-rates every identified company |
| `enable_contacts` | Enable contacts: record the consent and credit the welcome credits, after your explicit yes |
| `invite_user` | Add a login for a colleague; the password comes by e-mail |
| `create_email_alert` | Create an e-mail alert: new, returning or intensive visitors, on workdays at the chosen hour |
| `list_tags` | Which tags exist? Per tag the number of companies and whether it was set by hand, automatically via the API or by a segment |
| `tag_companies` | Tag companies by domain or company_id, up to 500 per call, even before their first visit. A new tag name creates the tag, and the assistant tells you |
| `untag_companies` | Remove a tag from companies; the tag itself stays. The client asks before the call |
| `search_docs` | How does X work in Webmetic? Searches the documentation at webmetic.de/docs, German or English |
| `get_doc_page` | One documentation page as Markdown, so the assistant answers with the exact steps |
| `read_skill` | Reads one of the skills above, so every client (also without the plugin) answers the recurring questions by the same playbook |

German input works throughout: umlauts, spellings like "Muenchen", German
date formats ("01.08.2026", "seit gestern"), and German company names are
all understood — and so is English.

The server also ships six ready-made prompts, in claude.ai under the
connector's "+" menu: `wochenbericht` (weekly report), `heisse-leads`
(hot leads), `unternehmens-analyse` (company analysis), `zielkunden-check`
(target-account check), `ansprechpartner` (contacts) and `einrichten`
(set up Webmetic).

### Privacy

Visitors are identified as companies, never as persons; form contents are
not captured. Contacts come from a separate contact database and are only
looked up when you explicitly ask for them at a named company. Visitor data
is only read; revealing contact data costs credits, and the four setup tools
and the two tag tools change your account only on your instruction — your AI client confirms both
before every call. Payment and subscription stay in the dashboard. The server is hosted in Germany and your API
key stays in your local client configuration.

### Troubleshooting

| Message | Cause and fix |
|---|---|
| "API key was rejected" | Check the key: raw, no "Bearer", freshly copied from the dashboard |
| "not authorized for domain" | Domain spelling must exactly match your account (`www.`, subdomain) |
| "0 identified companies" | Not an error: no identifiable visits in the period. Widen the date range; if nothing ever arrives, `check_tracking_setup` checks the tracking code |
| "The contact setup is not done yet" | Enable contacts in the chat: the assistant shows the three consents and calls `enable_contacts` after your yes; this also credits the welcome credits |
| "Not enough credits" | Top up credits in the dashboard; searches stay free, only revealing costs |
| Connection fails | Use `https://mcp.webmetic.de/mcp` exactly (with `/mcp` at the end) |

Questions? support@webmetic.de · Documentation: https://webmetic.de/mcp

---

<p align="center"><sub>MIT License · © 2026 Webmetic GmbH</sub></p>
