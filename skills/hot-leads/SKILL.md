---
name: hot-leads
description: Findet die heißesten Leads anhand von Kaufsignalen (Formular-Submits, Kontakt-Klicks, Downloads, Besuche wichtiger Seiten, hohe oder steigende Relevanz). Nutzen bei "heiße Leads", "wer ist kaufbereit", "wen soll der Vertrieb anrufen". / Finds the hottest leads based on buying signals (form submits, contact clicks, downloads, high-intent page visits, and high or rising relevance). Use for "hot leads", "who is ready to buy", "who should sales call".
---

# Heiße Leads

Identifiziere die Unternehmen mit den stärksten Kaufsignalen und begründe
jede Einstufung mit konkretem Verhalten.

## Vorgehen

1. **Domain und Zeitraum klären.** Standard: die letzten 7 Tage; bei wenig
   Verkehr auf 30 Tage erweitern und das im Ergebnis vermerken. Denselben
   Zeitraum in allen Aufrufen verwenden.
2. **Bestandskunden ausblenden.** Standardmäßig zeigt die Liste nur
   Interessenten. `list_tags` aufrufen und von den Tags `Kunde` und
   `HubSpot` (die HubSpot-Integration taggt synchronisierte Unternehmen
   automatisch) nur die übernehmen, die es im Konto gibt. Diese als
   `exclude_tag` an jeden Aufruf in Schritt 3 geben; einen Tag, den es nicht
   gibt, nie übergeben (das Tool bricht dann mit einem Fehler ab). Gibt es
   keinen der beiden, ohne `exclude_tag` weiterarbeiten. Fragt der Nutzer
   ausdrücklich nach Bestandskunden, diesen Schritt auslassen.
3. **Signale einsammeln** mit `list_visiting_companies` (gleiche Domain,
   gleicher Zeitraum, mehrere Aufrufe mit unterschiedlichen Filtern):
   - `event="form_submit"` — abgeschickte Formulare (stärkstes Signal)
   - `event="contact_click"` — Klicks auf E-Mail oder Telefonnummer
   - `event="download"` — heruntergeladene Dateien
   - `visited_page` mit dem Pfad der Preisseite. Den echten Pfad vorher in
     den Top-Seiten von `get_activity_summary` nachsehen (z. B. `/pricing`
     oder `/preise`), nicht raten.
   - `sort="relevance"`, `limit=25` — die Rangfolge nach Relevanz
     (Passung zum Zielkundenprofil mal Aktivität, Skala 0 bis 100, ab 70
     heiß, ab 50 hoch). Relevanz ist die Zahl, nach der eingestuft wird,
     nicht die Aktivität allein.
   - `get_score_changes` für denselben Zeitraum: Unternehmen, die neu heiß
     sind oder deren Relevanz deutlich gestiegen ist, sind ein „Warum
     jetzt". Meldet das Tool, dass der Verlauf erst später beginnt, ohne
     diesen Baustein weiterarbeiten.
4. **Ranking bilden.** Gewichte grob: Formular-Submit > Kontakt-Klick >
   Preisseite > Download > hohe Relevanz allein. Innerhalb einer Stufe
   entscheidet die Relevanz; neu heiße oder stark steigende Unternehmen
   eine Stufe höher einordnen. Wiederkehrende
   Unternehmen (mehrere Sitzungen) höher einstufen. Trackt die Domain
   keine Events (alle Event-Filter leer), auf Preisseite, Relevanz und
   Besuchsintensität ausweichen und das im Ergebnis erwähnen.
5. **Top 3 vertiefen** mit `get_company_activity`: Was genau wurde
   angesehen, wie lange, welche Events, über welche Quelle kam das
   Unternehmen?
6. **Ergebnis ausgeben:** Rangliste mit Relevanz und je einer Zeile Begründung
   („Formular auf /kontakt abgeschickt, davor 6 Minuten auf der
   Preisseite") plus empfohlenem nächsten Schritt pro Lead. Darunter eine
   Zeile, welche Tags ausgeblendet wurden („Bestandskunden mit den Tags
   Kunde und HubSpot sind ausgeblendet; auf Wunsch zeige ich sie.").

## Stil

- In der Sprache des Nutzers antworten. Auf Deutsch: „Unternehmen"
  statt „Firma".
- Jede Einstufung mit beobachtetem Verhalten belegen, nie mit Vermutungen.
- Webmetic identifiziert Unternehmen, keine Personen: keine
  Ansprechpartner erfinden. Fragt der Nutzer nach Ansprechpartnern, für
  das jeweilige Unternehmen `find_contacts` nutzen; E-Mail oder Rufnummer
  nur per `reveal_contact` freischalten, wenn der Nutzer das ausdrücklich
  will (kostet Credits).
- Steht in der Spalte Fit nur „-", ist für die Website kein
  Zielkundenprofil hinterlegt, und die Relevanz entspricht der Aktivität.
  Dann einmal anbieten, das Profil aus der Website zu entwerfen; mit
  `set_target_profile` erst speichern, wenn der Nutzer den Entwurf
  bestätigt hat.
