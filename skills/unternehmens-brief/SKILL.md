---
name: unternehmens-brief
description: Tiefer Steckbrief zu einem einzelnen benannten Unternehmen aus Webmetic — Profil, kompletter Besuchsverlauf, Interessen, Einstufung und ein konkreter nächster Schritt. Nutzen bei "erzähl mir was über [Unternehmen]", "Steckbrief zu", "Deep Dive", "was wissen wir über", "Profil von", "analysiere [Unternehmen]", "alles zu [Unternehmen]". / Deep structured brief on one named company from Webmetic — profile, full visit history, interests, scoring and one concrete next step. Use for "tell me about [company]", "brief me on", "deep dive on", "what do we know about", "profile of", "research [company]".
---

# Unternehmens-Brief

Ein strukturierter Steckbrief zu genau einem Unternehmen. Jede Aussage kommt aus
den Werkzeugen. Das Gegenstück zum Wochenbericht: der geht in die Breite, dieser
in die Tiefe.

Der Leser entscheidet damit eine Frage: Nehme ich dieses Unternehmen heute in die
Hand oder nicht. Darauf arbeitet der Brief hin.

## Sprache

In der Sprache des Nutzers antworten, inklusive Überschriften und Feldnamen. Daten
nicht übersetzen: Unternehmensnamen, Domains, Seitenpfade und Adressen bleiben
unverändert. Auf Deutsch „Unternehmen" statt „Firma".

## Wann diese Skill greift

Der Nutzer nennt **ein** Unternehmen und will Recherche, Profil oder Steckbrief.
Nicht auslösen bei einer Liste oder einer Rangfolge über viele Besucher — das ist
`hot-leads` oder `wochenbericht`. Nicht bei einer Zielkundenliste — das ist
`abm-check`.

## Ablauf

1. **Unternehmen auflösen** mit `get_company_profile` (Name, Domain oder
   company_id). Zeitraum: 90 Tage, wenn der Nutzer nichts anderes sagt.
   - Trifft das Werkzeug erkennbar eine andere Schreibweise als gefragt, das
     sichtbar machen („gefunden als …") statt es stillschweigend zu übernehmen.
   - Kein Treffer: klar sagen, einen größeren Zeitraum oder eine andere
     Schreibweise anbieten, nichts erfinden.
2. **Besuchsverlauf holen** mit `get_company_activity`, gleicher Zeitraum wie in
   Schritt 1. Daraus verdichten: Zahl der Besuche, erster und letzter Besuch,
   meistbesuchte Seiten, Quellen, Ereignisse (Formular, Download, Kontaktklick),
   und ein Trend aus dem Vergleich der letzten 30 Tage mit den 30 davor:
   zunehmend, gleichbleibend, abnehmend oder eingeschlafen.
3. **Einstufung erklären** mit `explain_company_score`, gleicher Zeitraum.
   Relevanz ist die Zahl, nach der sortiert wird. Sie verbindet die Passung zum
   Zielkundenprofil mit der Aktivität auf der Website. Aus der Antwort: welche
   Kriterien der Passung erfüllt sind und welche fehlen oder unbekannt sind, und
   was die Aktivität trägt. Ohne hinterlegtes Profil entspricht die Relevanz der
   Aktivität; dann das auch so sagen statt eine Passung zu behaupten.
4. **Kommentare lesen** mit `get_company_notes`: was das Team zu diesem
   Unternehmen schon festgehalten hat. Die neuesten drei in den Brief, und die
   Einschätzung darauf abstimmen (z. B. schon im Gespräch, schon abgesagt).
5. **Brief ausgeben** nach dem festen Format unten.
6. **Speichern anbieten.** Nach dem Brief einmal anbieten, die Einschätzung und
   den nächsten Schritt als kurzen Kommentar zu speichern. `add_company_note`
   erst aufrufen, wenn der Nutzer den Text bestätigt hat.

**Abbruchgründe** (Brief entfällt, stattdessen ein Satz): Unternehmen nicht
gefunden; im Free-Plan verborgen (dann ist nur bekannt, dass und wann es da war,
mehr gibt der Plan nicht her); kein identifizierter Besuch im Zeitraum.

## Zahlen und Begriffe

- **Eine Zahl pro Größe im ganzen Brief.** Mitarbeiterzahl, Umsatz, Besuchszahl:
  Wenn der Einleitungssatz rundet, rundet er dieselbe Zahl, die auch im Steckbrief
  steht. Zwei verschiedene Zahlen für dieselbe Größe zerstören das Vertrauen in
  den ganzen Brief.
- **Keine internen Feldnamen.** Nicht „lead_score", „fit_score", „relevance_score"
  oder „session_engagement" schreiben, sondern Aktivität, Passung, Relevanz und
  Verweildauer in Sekunden oder Minuten.
- **Webmetic erkennt Unternehmen, keine Personen.** Nie formulieren, als habe eine
  bestimmte Person die Seite besucht.
- **Keine Ansprechpartner in diesem Brief.** Namen, E-Mail-Adressen und Rufnummern
  gehören in `ansprechpartner` und werden dort ausdrücklich angefordert.
- „Kein identifizierter Besuch" ist nicht dasselbe wie „kein Besuch".

## Ausgabeformat

Bei jedem Lauf gleich. Das Format auch dann nicht umbauen oder „verbessern", wenn
die Skill in derselben Unterhaltung schon einmal gelaufen ist. Abschnitte ohne
Daten ganz weglassen statt sie leer zu zeigen.

```
# Unternehmens-Brief — {Unternehmen}
*Stand {heutiges Datum} · Zeitraum: letzte {N} Tage*

{Ein Satz: was sie tun, eine Größenangabe, die Einstufung. Die Größenangabe ist
dieselbe Zahl wie im Steckbrief.}

## Steckbrief
- **Branche:** {Branche}
- **Größe:** {Mitarbeiter} · {Umsatz, wenn vorhanden}
- **Sitz:** {Ort, Land}
- **Website:** {Domain}
- **Einstufung:** Relevanz {Wert} · Passung {Wert}: {welche Kriterien passen, welche
  fehlen} · Aktivität {Wert}: {was sie trägt}

## Besuche (letzte {N} Tage)
- **Besuche:** {Anzahl}
- **Erster / letzter Besuch:** {Datum} / {Datum}
- **Meistbesuchte Seiten:** {Pfade}
- **Quellen:** {Direkt, Google, LinkedIn …}
- **Kaufsignale:** {Formular, Download, Kontaktklick, je mit Datum — oder "keine"}
- **Auffällige Besuche:** {z. B. "3 Seiten in einer Sitzung, 71 Sekunden auf /preise"}
- **Trend:** {zunehmend / gleichbleibend / abnehmend / eingeschlafen} — {ein Satz}

## Kommentare im Team
- {Datum} · {Autor}: {Kern des Kommentars in einem Satz}

## Einschätzung und nächster Schritt
{Ein kurzer Absatz, der die Lage zusammenfasst, dann ein bis zwei konkrete
Schritte. Jeder Schritt sagt zuerst, was er bringt.}

Zum Weitermachen kopieren:

Wer sind die Ansprechpartner bei {Unternehmen}?
```

## Quellenregel

Jede Angabe stammt aus einer Antwort der Werkzeuge. Keine Branche, keine
Mitarbeiterzahl, keinen Seitenpfad und kein Datum erfinden. Fehlt ein Feld, die
Zeile weglassen statt sie zu füllen. Antwortet ein Werkzeug mit einem Fehler, das
sagen statt auf ältere Angaben auszuweichen. Zahlen in verschiedenen Abschnitten
müssen zueinander passen: Eine genannte Anzahl entspricht den aufgezählten
Einträgen.

## Was nicht zu tun ist

- Keine Ansprechpartner suchen und nichts freischalten. Das ist ein anderer
  Auftrag und wird ausdrücklich verlangt.
- Nicht aus einer dünnen Datenlage einen vollen Brief machen. Ein Besuch auf der
  Startseite ist ein Satz, kein Steckbrief.
- Keine Kaufabsicht behaupten, die die Besuche nicht hergeben.
- Kein Unternehmen briefen, das im Free-Plan verborgen ist.
