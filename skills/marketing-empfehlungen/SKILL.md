---
name: marketing-empfehlungen
description: Was sollte das Marketing als Nächstes tun? Leitet aus den Webmetic-Daten höchstens 5 Maßnahmen ab, jede mit der Zahl, auf der sie beruht, und wer sie umsetzt. Kampagnen und Quellen, die am Zielprofil vorbeigehen, fehlende UTM-Parameter, Bestandskunden in bezahlten Anzeigen, Seiten mit viel Interesse und wenig Formularen, Themen der passenden Unternehmen, KI-Assistenten als eigener Kanal. Nutzen bei "Marketing-Empfehlungen", "was soll das Marketing jetzt tun", "welche Kampagne bringt die falschen Unternehmen", "wo verschwenden wir Werbebudget", "worüber sollten wir schreiben", "bringen ChatGPT und Co. Besucher". / What should marketing do next? Turns Webmetic data into at most 5 actions, each with the number behind it and who does it. Campaigns and sources that miss the target profile, missing UTM tags, existing customers in paid ads, pages with high interest and few form submissions, topics read by well-fitting companies, AI assistants as a channel of their own. Use for "marketing advice", "what should marketing do next", "which campaign brings the wrong companies", "where do we waste ad spend", "what should we write about", "do ChatGPT and co. send visitors".
---

# Marketing-Empfehlungen

Der Wochenbericht sagt, was passiert ist. Dieser Skill sagt, was das
Marketing daraufhin tun sollte: höchstens 5 Maßnahmen, jede mit der Zahl
aus den Tools, auf der sie beruht, und mit der Rolle, die sie umsetzt. Er
liest nur und ändert nichts im Konto.

## Begriffe

- **Passt zum Zielprofil:** Fit 70 oder mehr. **Außerhalb des
  Zielprofils:** Fit 0 (Branche, Größe oder Region liegt ganz daneben).
- **Hohe Relevanz:** Relevanz 50 oder mehr.
- **Bewertet** ist eine Zeile mit einer Zahl in der Spalte Fit. Zeilen mit
  Fit „-" und verborgene Zeilen (`[hidden]`, Free-Plan) zählen in keinem
  Anteil mit.
- **Bestandskunden:** Unternehmen mit dem Tag `Kunde`, `Customer` (der Name
  in englischen Konten) oder `HubSpot` (die HubSpot-Integration taggt
  synchronisierte Unternehmen automatisch).
- **KI-Assistenten:** Quellen, die `chatgpt`, `perplexity`, `gemini`,
  `claude` oder `copilot` enthalten. Sie kommen als `utm_source` (etwa
  `chatgpt.com`) oder als Referrer (`perplexity.ai`,
  `gemini.google.com`, `claude.ai`, `copilot.microsoft.com`) an.

Die Schwellen (70, 50, 10 Unternehmen, die Hälfte) sind Startwerte. Nennt
der Nutzer andere, gelten seine.

## Vorgehen

1. **Domain und Zeitraum klären.** Standard: die letzten 30 Tage, verglichen
   mit den 30 Tagen davor. Beide Zeiträume als explizite Daten
   (`from_date`, `to_date`) festlegen und in jedem Aufruf genau so
   verwenden. Nur nachfragen, was die Frage offenlässt.
2. **Überblick in beiden Zeiträumen.** `get_activity_summary` zweimal
   aufrufen, einmal je Zeitraum. Daraus: Top-Seiten mit Aufrufen,
   Quellen, die Zeile „Paid (tagged utm_medium)" mit der Zahl bezahlter
   Sitzungen, der Abschnitt „Campaigns (T of N sessions tagged)" mit den
   Kampagnen, „Intent events" und „Industries". Fehlt ein Abschnitt, gibt
   es im Zeitraum nichts davon.
3. **Tags.** `list_tags` aufrufen. Von `Kunde`, `Customer` und `HubSpot`
   nur die Tags verwenden, die es im Konto gibt; ein unbekannter Tag in
   `tag` oder `exclude_tag` bricht den Aufruf mit einem Fehler ab.
4. **Kampagnen und Quellen.** Für die bis zu 3 größten Kampagnen
   `list_visiting_companies(campaign="<Name>", sort="relevance", limit=50)`,
   für die bis zu 3 größten Quellen außer `(direct)`
   `list_visiting_companies(source="<Quelle>", sort="relevance", limit=50)`.
   Die Kopfzeile nennt die Zahl der Unternehmen; mit `page=2` usw.
   weiterblättern, bis alle Zeilen gezählt sind (höchstens 4 Seiten,
   darüber die gezählten 200 als Stichprobe benennen). Je Kampagne und
   Quelle zählen: bewertete Unternehmen, davon Fit 0, davon Fit 70+,
   davon Relevanz 50+.
5. **Bestandskunden in bezahlten Anzeigen.** Gibt es bezahlte Sitzungen
   und mindestens einen der Kunden-Tags:
   `list_visiting_companies(source="paid", tag=[<vorhandene Kunden-Tags>])`.
   Die Sitzungszahl in der Kopfzeile umfasst alle Sitzungen dieser
   Unternehmen, nicht nur die bezahlten; als Beleg nur die Zahl der
   Unternehmen nennen.
6. **Seiten mit Kaufabsicht.** Unter den Top-Seiten die Pfade mit
   Kontakt, Demo, Preis oder Angebot (etwa `/kontakt`, `/demo`, `/preise`,
   `/pricing`). Je Seite `list_visiting_companies(visited_page="<Pfad>")`
   und dasselbe mit `event="form_submit"`. Der zweite Aufruf zählt
   Unternehmen, die diese Seite sahen und irgendwo auf der Website ein
   Formular abschickten. Fehlt „Intent events" in der Zusammenfassung,
   ist unbekannt, ob Formulare erfasst werden: Regel 5 dann auslassen und
   das unter „Unbekannt" nennen.
7. **Themen der passenden Unternehmen.**
   `list_visiting_companies(sort="relevance", limit=50)` und unter den
   Zeilen mit Fit 70+ die Spalte Top page nach Seite oder Bereich (erster
   Pfadteil, etwa `/blog`, `/loesungen`) zählen. Für eine Branche, die
   gegenüber dem Vorzeitraum gewachsen ist, dasselbe mit
   `industry="<Slug aus Industries>"`.
8. **KI-Assistenten.** In beiden Zeiträumen je Assistent
   `list_visiting_companies(source="chatgpt")` usw.; ein leeres Ergebnis
   ist eine gültige Antwort. Unternehmen, die über mehrere Assistenten
   kamen, nur einmal zählen.
9. **Maßnahmen wählen** mit der Tabelle unten, höchstens 5, die größte
   Zahl zuerst, je Kampagne, Quelle oder Seite nur eine.
10. **Ausgeben** im Format unten.

## Regeln für Maßnahmen

Von oben nach unten prüfen, je Kampagne, Quelle oder Seite gewinnt die
erste passende Zeile.

| # | Wann | Maßnahme | Beleg | Wer |
|---|---|---|---|---|
| 0 | Der Nutzer sagt, dass Anzeigen laufen, aber die Zusammenfassung hat keine Zeile „Paid (tagged utm_medium)" | Ändern: in allen Anzeigen-URLs `utm_source` und `utm_medium` (etwa `cpc`) setzen, sonst lassen sich bezahlte Besuche nicht von organischen trennen | 0 von <N> Sitzungen mit bezahltem utm_medium | Marketing, wer die bezahlten Kampagnen betreut |
| 1 | Bezahlte Sitzungen P, kampagnenmarkierte Sitzungen T (aus „Campaigns", fehlt der Abschnitt: 0), und P minus T ist mindestens die Hälfte von P | Ändern: in allen Anzeigen `utm_campaign` setzen | mindestens <P minus T> von <P> bezahlten Sitzungen ohne Kampagnennamen | Marketing, wer die bezahlten Kampagnen betreut |
| 2 | Kampagne mit mindestens 10 bewerteten Unternehmen, davon die Hälfte oder mehr mit Fit 0 | Ändern: Targeting von <Kampagne> prüfen | <n> von <m> bewerteten Unternehmen außerhalb des Zielprofils | Marketing, wer die Kampagne betreut |
| 3 | Quelle mit mindestens 10 bewerteten Unternehmen, davon die Hälfte oder mehr mit Fit 0 | Ändern: prüfen, was <Quelle> bringt, bevor dort mehr Aufwand hineingeht | <n> von <m> bewerteten Unternehmen außerhalb des Zielprofils | Marketing |
| 4 | Kampagne oder Quelle mit 3 oder mehr Unternehmen mit Relevanz 50+ | Mehr davon: <Kampagne oder Quelle> weiterführen und diese Unternehmen an den Vertrieb geben (Skill `hot-leads`) | <h> Unternehmen mit Relevanz 50+, <f> mit Fit 70+ | Marketing mit dem Vertrieb |
| 5 | Mindestens 1 Bestandskunde kam über bezahlte Anzeigen | Ändern: Bestandskunden als Ausschlussliste in den bezahlten Kampagnen hinterlegen | <n> Bestandskunden kamen über bezahlte Anzeigen | Marketing, wer die bezahlten Kampagnen betreut |
| 6 | Seite mit Kaufabsicht, mindestens 10 Unternehmen, weniger als jedes zehnte davon mit `form_submit` | Ändern: Formular und Seite prüfen (Länge, Pflichtfelder, sichtbarer Button, Ladezeit) | <n> Unternehmen sahen <Seite>, <f> davon schickten ein Formular ab | Wer die Website betreut |
| 7 | Eine Seite oder ein Bereich ist bei 3 oder mehr Unternehmen mit Fit 70+ die meistbesuchte Seite | Mehr davon: Inhalte zu <Thema> ausbauen und von den meistbesuchten Seiten verlinken | <n> von <m> Unternehmen mit Fit 70+ lasen vor allem <Seite oder Bereich> | Content oder SEO |
| 8 | Eine Branche hat mindestens 5 Unternehmen mehr als im Vorzeitraum | Mehr davon: für <Branche> über <Thema> schreiben (Thema aus Schritt 7) | <n> Unternehmen jetzt gegenüber <m> davor | Content oder SEO |
| 9 | Eine Top-Seite des Vorzeitraums mit mindestens 20 Aufrufen hat die Hälfte oder mehr ihrer Aufrufe verloren | Ändern: <Seite> prüfen (Ranking, Verlinkung, Ladezeit) | <v> Aufrufe gegenüber <w> davor | SEO oder wer die Website betreut |
| 10 | 3 oder mehr Unternehmen kamen über KI-Assistenten, oder es sind mehr als im Vorzeitraum | Mehr davon: KI-Assistenten als eigenen Kanal berichten und die Seiten pflegen, auf denen diese Besucher landen | <n> Unternehmen über KI-Assistenten, <f> davon mit Fit 70+, davor <m> | Marketing mit Content oder SEO |

Zu Regel 9: Ist die Seite im aktuellen Zeitraum nicht mehr unter den Top
10, ihre Aufrufe nicht schätzen. Stattdessen in beiden Zeiträumen
`list_visiting_companies(visited_page="<Pfad>")` aufrufen und die Zahl der
Unternehmen vergleichen.

## Ausgabe

Überschrift `Marketing-Empfehlungen · <Domain> · <Start> bis <Ende>`,
darunter eine Zeile zu den Kanälen („Bezahlt: <P> Sitzungen, davon
mindestens <P minus T> ohne Kampagnennamen. KI-Assistenten: <n>
Unternehmen, davor <m>."). Dann:

| # | Maßnahme | Kampagne, Quelle oder Seite | Beleg | Wer |
|---|---|---|---|---|

Der Beleg enthält die Zahl selbst („14 von 20 bewerteten Unternehmen aus
‚messe-2026' außerhalb des Zielprofils"). Danach:

- **KI-Assistenten:** immer eine eigene Zeile, auch wenn es keine Maßnahme
  ist („Im Zeitraum kamen 4 Unternehmen über ChatGPT und Perplexity, davor
  1."). Bei 0 genau das sagen.
- **Unbekannt:** was die Daten nicht beantworten, etwa Werbeausgaben,
  Klicks und Gebote, Besuche ohne UTM-Parameter, Formulare ohne erfasste
  Events, Fit ohne Zielkundenprofil.
- Höchstens ein Angebot: für die Unternehmen einer guten Kampagne der
  Skill `hot-leads`, oder, wenn Fit überall „-" ist, das Zielkundenprofil
  aus der Website zu entwerfen (`set_target_profile` erst nach
  Bestätigung).

## Grenzen

- Jede Zahl stammt aus einem Tool-Aufruf mit demselben Zeitraum. Nichts
  schätzen, nichts hochrechnen; fehlt eine Zahl, steht sie unter
  „Unbekannt".
- Keine Preis-, Budget- oder Gebotsempfehlungen: Webmetic sieht weder
  Ausgaben noch Klicks noch Gebote. Nennt der Nutzer Ausgaben je Kampagne,
  darf er die Kosten je passendem Unternehmen bekommen, sonst nicht.
- Zu kleine Zahlen (unter den Schwellen der Tabelle) ergeben keine
  Maßnahme. Das kurz sagen („Für die übrigen Kampagnen sind die Zahlen zu
  klein.").
- Keine Anrufliste (das ist `hot-leads`) und keine Nachrichten (das ist
  `ansprache-vorbereitung`).
- Ohne Zielkundenprofil (Fit überall „-") die Regeln 2, 3 und 7 sowie die
  Fit-Angaben auslassen und das unter „Unbekannt" nennen.
- Es werden Unternehmen identifiziert, keine Personen.

## Stil

- In der Sprache des Nutzers antworten. Auf Deutsch: Sie, „Unternehmen"
  statt „Firma", keine Gedankenstriche, große Zahlen als 1.000+ statt mit
  Tilde.
- Jede Maßnahme ist ein einfacher Satz und beginnt mit „Ändern:" oder
  „Mehr davon:". Die Rolle nennen, nie eine Person.
- Zahlen immer mit Bezugsgröße („14 von 20 bewerteten Unternehmen"), nie
  identifizierte Unternehmen neben Gesamtbesuchern oder einer Quote.
