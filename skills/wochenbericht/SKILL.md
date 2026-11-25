---
name: wochenbericht
description: Erstellt den wöchentlichen Besucherbericht aus Webmetic mit Vergleich zur Vorwoche und den Unternehmen, die heißer werden. Nutzen bei "Wochenbericht", "was war diese Woche auf der Website los", "Zusammenfassung der Besucher". / Builds the weekly visitor report from Webmetic, compared with the week before and with the companies heating up. Use for "weekly report", "what happened on the website this week", "visitor summary".
---

# Wochenbericht

Erstelle einen kompakten Wochenbericht über die identifizierten Unternehmen
auf der Website. Datenquelle sind die Webmetic-MCP-Tools.

## Vorgehen

1. **Domain klären.** Wenn der Nutzer mehrere Domains im Konto hat und keine
   nennt, frage nach. Die Domain muss exakt der Schreibweise im
   Webmetic-Konto entsprechen.
2. **Zeitraum festlegen.** Standard: die letzten 7 Tage, als Vergleich die
   7 Tage direkt davor (Vorwoche). Beide Zeiträume als explizite Daten
   (`from_date`, `to_date`) festlegen und in ALLEN Aufrufen genau so
   verwenden, damit die Zahlen zueinander passen. Wählt der Nutzer einen
   anderen Zeitraum, ist der Vergleich der gleich lange Zeitraum davor.
3. **Daten holen** (in dieser Reihenfolge, gleiche Domain):
   - `get_activity_summary` für die Berichtswoche: Gesamtzahlen,
     Top-Seiten, Quellen, Kampagnen, Intent-Events, Branchen.
   - `get_activity_summary` ein zweites Mal für die Vorwoche.
   - `list_visiting_companies` mit `sort="relevance"`, `limit=10` für die
     Top-Unternehmen der Berichtswoche.
   - `get_score_changes` für die Berichtswoche: wessen Relevanz gestiegen
     oder gefallen ist und wer neu heiß ist (Relevanz 70+).
   - `get_visit_highlights` mit `highlight="intensive"` und einmal mit
     `highlight="new"` für intensive Sitzungen und Erstbesucher.
4. **Bericht strukturieren:**
   - **Überblick**: identifizierte Unternehmen, Sitzungen und
     Seitenaufrufe der Berichtswoche, je mit dem Wert der Vorwoche
     („41 Unternehmen, Vorwoche 33"). Nur Veränderungen deuten, die die
     Zahlen tragen; bei kleinen Zahlen nicht von einem Trend sprechen.
   - **Top-Unternehmen**: die 5 bis 10 wichtigsten nach Relevanz mit dem
     interessantesten Detail (z. B. meistbesuchte Seite).
   - **Wer wird heißer**: aus `get_score_changes` die Aufsteiger und neu
     heißen Unternehmen mit Relevanz vorher und nachher und dem Grund in
     einem Halbsatz. Absteiger nur, wenn sie vorher heiß waren. Meldet das
     Tool, dass der Verlauf erst später beginnt, den Abschnitt durch einen
     Satz mit diesem Datum ersetzen.
   - **Kaufsignale**: Intent-Events der Woche (Formular-Submits,
     Kontakt-Klicks, Downloads). Bei Bedarf per
     `list_visiting_companies(event="form")` nachfassen, wer dahintersteckt.
     Fehlt die Event-Sektion in der Zusammenfassung, trackt die Domain im
     Zeitraum keine Events; den Abschnitt dann weglassen oder kurz erklären.
   - **Quellen und Kampagnen**: woher der Verkehr kam, bezahlte vs.
     organische Anteile, und welche Quelle oder Kampagne sich gegenüber der
     Vorwoche deutlich bewegt hat. Besuche über KI-Assistenten (chatgpt,
     perplexity, gemini, claude, copilot) als eigene Zeile nennen, wenn es
     welche gibt.
   - **Neu diese Woche**: Erstbesucher, die zur Zielgruppe passen.
   - **Empfehlung**: 1 bis 3 konkrete nächste Schritte für den Vertrieb.

## Stil

- In der Sprache des Nutzers antworten (fragt er auf Englisch, ist der
  Bericht englisch). Auf Deutsch: sachlich, „Unternehmen" statt „Firma".
- Zahlen immer mit Bezugsgröße („14 von 86 Unternehmen").
- Keine erfundenen Werte: nur, was die Tools liefern. Wenn ein Zeitraum
  keine Events enthält, das ehrlich benennen (fehlendes Tracking ist nicht
  fehlendes Interesse).
- Höchstens ein Angebot pro Bericht, nie als Liste: Steht in der Spalte
  Fit nur „-", fehlt das Zielkundenprofil; dann anbieten, es aus der
  Website zu entwerfen, und erst nach Bestätigung mit `set_target_profile`
  speichern. Will der Nutzer den Bericht regelmäßig, einmal eine
  E-Mail-Benachrichtigung anbieten (`create_email_alert`, nach Bestätigung
  von Art, Tagen, Uhrzeit und Empfängern).
