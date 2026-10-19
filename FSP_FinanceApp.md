# Spezifikation – FinanceApp

| | |
|---|---|
| **Version** | 1.0 (Entwurf) |
| **Zielgruppe** | Privater Gebrauch, ein Benutzer / Haushalt |
| **Plattform** | Windows-Desktop, C# / .NET 8, WPF (MVVM) |
| **Hauptwährung** | CHF |
| **Quellen** | `FinanceApp_Spezifikation.md` (Grobidee), `Finanzkontrolle_Vorlage.xlsx` (Excel-Vorlage) |

---

## 1. Zweck und Ziele

Die FinanceApp ersetzt die bisherige Excel-Vorlage „Finanzkontrolle“ und dient der privaten Überwachung von Einnahmen und Ausgaben.

Ziele:

1. Alle Einnahmen und Ausgaben als kategorisierte Transaktionen erfassen.
2. Zu Jahresbeginn ein Jahresbudget pro Kategorie planen (inkl. Fixkosten).
3. Pro Monat auf einen Blick Budget vs. effektive Ausgaben sehen.
4. Alle Konten (Spar-, Debit-, Anlage-, 3a-Konten usw.) mit Saldo und Entwicklung überblicken.
5. Am Jahresende bzw. jederzeit eine grafische Auswertung erhalten und Budgetüberschreitungen erkennen.
6. Sensible Finanzdaten zuverlässig schützen und regelmässig sichern.

### Nicht-Ziele (Phase 1)

- Kein Import von Bankdaten (CSV, CAMT, Open Banking). Die Erfassung erfolgt **ausschliesslich manuell**.
- Keine Mehrbenutzer- oder Mehrmandantenfähigkeit.
- Keine Mobile App und kein Server (siehe Abschnitt 11, nur architektonisch vorbereiten).
- Keine Mehrwährungs-Umrechnung mit Wechselkursen (siehe A-4).

---

## 2. Glossar

| Begriff | Bedeutung |
|---|---|
| **Transaktion** | Eine einzelne Buchung: Einnahme, Ausgabe oder Umbuchung. |
| **Kategorie** | Einordnung einer Transaktion. Ausgabe-Kategorie z. B. „Essen/Trinken“, Einnahme-Kategorie z. B. ein Arbeitgeber. |
| **Konto** | Bestand, von/auf den gebucht wird (Debit, Spar, Anlagefonds, 3a …). |
| **Jahresbudget** | Geplanter Gesamtbetrag einer Ausgabe-Kategorie für ein Jahr. |
| **Monatsbudget** | Variables Monatsbudget + Fixkosten-Anteil (im Normalfall Jahresbudget ÷ 12). |
| **Fixkost** | Wiederkehrende, planbare Ausgabe (Versicherung, Krankenkasse, Vereinsbeitrag …), monatlich oder jährlich. |
| **Wertstand** | Manuell erfasster Marktwert eines Anlagekontos zu einem Stichtag. |
| **Umbuchung** | Geldbewegung zwischen zwei eigenen Konten, weder Einnahme noch Ausgabe. |

---

## 3. Abgeleitet aus der Excel-Vorlage

Die Vorlage besteht aus einem Blatt **Budget** (in der Vorlage leer) und je einem Blatt pro Monat (Januar–Dezember). Jedes Monatsblatt enthält:

**Kopf:** Titel „Finanzkontrolle <Jahr>“, Monatsname.

**Budgettabelle (oben):** eine Zeile pro Kategorie plus Total.

| Spalte | Bedeutung | Formel / Logik in der Vorlage |
|---|---|---|
| Kategorie | Name | – |
| Jahresbudget | Budget für das ganze Jahr | aus Januar-Blatt übernommen |
| Monatsbudget | Budget pro Monat | Jahresbudget ÷ 12 |
| Laufend | Ausgaben des aktuellen Monats | `SUMIF` über Transaktionen des Monats pro Kategorie |
| Differenz | Rest im Monat | Monatsbudget − Laufend |
| Kumulative Ausgabe | Ausgaben seit Jahresbeginn | Summe bisheriger Monate inkl. laufendem |
| Differenz | Rest im Jahr | Jahresbudget − Kumulative Ausgabe |

Vorlage-Kategorien (Ausgangsbestand): Essen/Trinken, Kleidung, Haushalt, Freizeit/Ausgang, Geschenke, Sport, Kosmetik, Busse, Spende, …, Auto/Töff, ÖV, Unterhaltung, Miete, Ferien.

**Fixkosten-Block** (rechts oben): Spalten *Fixkosten*, *Betrag Mtl*, *Jährlich*.

**Transaktionstabelle (unten):** Spalten *Datum, Ausgabe, Einnahme, Kategorie, Beschreibung*, dazu Total-Zeile und **Endsaldo**. Zusätzlich gibt es je Konto (z. B. „Jugendspar“) eine eigene Transaktionstabelle gleichen Aufbaus.

**Verlauf:** Diagramm „Kumulative Ausgabe“ pro Monat.

> Die App übernimmt diese Struktur und Logik. Formeln werden zu automatisch berechneten Werten.

---

## 4. Fachliche Anforderungen

### 4.1 Kategorien

| ID | Anforderung |
|---|---|
| K-1 | Der Benutzer kann **Ausgabe-Kategorien** anlegen, umbenennen und deaktivieren (archivieren). Beispiele: Essen/Trinken, Mobilität, Gesundheit, Haushalt, Sport. |
| K-2 | Der Benutzer kann **Einnahme-Kategorien** anlegen, typischerweise je Arbeitgeber oder Einnahmequelle. |
| K-3 | Jede Kategorie hat genau einen Typ (Ausgabe / Einnahme). |
| K-4 | Kategorien mit bestehenden Transaktionen werden nicht gelöscht, nur archiviert. Archivierte Kategorien bleiben in der Historie sichtbar, sind aber bei neuer Erfassung nicht wählbar. |
| K-5 | Kategorien sind flach (keine Hierarchie). Unterkategorien sind ein mögliches späteres Feature. |
| K-6 | Optional: Farbe pro Kategorie für Diagramme. |

### 4.2 Konten

| ID | Anforderung |
|---|---|
| A-1 | Der Benutzer kann beliebig viele Konten anlegen. |
| A-2 | Jedes Konto hat: Name, **Kontotyp**, Währung (Standard CHF), Anfangssaldo mit Stichtag, optional Farbe/Icon, Status aktiv/archiviert. |
| A-3 | Kontotypen sind frei vom Benutzer definierbar. Vordefiniert sind Debitkonto, Sparkonto, Anlagefonds, Säule 3a und Bargeld. Jeder Typ ist entweder **Transaktionskonto** (Saldo ergibt sich aus Buchungen) oder **Anlagekonto** (Wert ergibt sich aus Wertständen, siehe A-6). |
| A-4 | **Währung:** Hauptwährung ist CHF. Ein Konto darf eine andere Währung haben, Beträge werden dann in dieser Währung geführt. Es erfolgt **keine automatische Umrechnung**. In Gesamtauswertungen werden Konten in Fremdwährung getrennt ausgewiesen bzw. nicht in die CHF-Summe eingerechnet (Hinweis in der UI). |
| A-5 | Aktueller Saldo (Transaktionskonto) = Anfangssaldo + Einnahmen − Ausgaben ± Umbuchungen. |
| A-6 | Bei Anlagekonten kann der Benutzer **Wertstände** (Datum, Betrag) erfassen. Wert = letzter Wertstand + später erfasste Ein-/Auszahlungen. Die Differenz zwischen Einzahlungen und Wertstand wird als Gewinn/Verlust ausgewiesen. |
| A-7 | Konten werden nicht gelöscht, wenn Buchungen existieren, sondern nur archiviert. |

### 4.3 Transaktionen

| ID | Anforderung |
|---|---|
| T-1 | Eine Transaktion hat: **Typ** (Einnahme / Ausgabe / Umbuchung), **Datum**, **Betrag** (> 0), **Beschreibung** (kurzer Text), **Kategorie**, **Konto**. |
| T-2 | Der Typ bestimmt die Darstellung (Spalte *Einnahme* oder *Ausgabe*, wie in der Vorlage). Der Betrag wird intern positiv gespeichert. |
| T-3 | Pflichtfelder: Typ, Datum, Betrag, Konto, Kategorie (bei Umbuchung entfällt die Kategorie). |
| T-4 | **Umbuchung:** enthält Quell- und Zielkonto, Datum, Betrag, Beschreibung. Sie ändert beide Salden, zählt aber **nicht** als Einnahme/Ausgabe und fliesst nicht ins Budget. |
| T-5 | Transaktionen können bearbeitet und gelöscht werden (mit Bestätigung). |
| T-6 | Eine Transaktion kann optional einer **Fixkost** zugeordnet werden (siehe 4.5). |
| T-7 | Das Datum bestimmt den Monat, in dem die Transaktion in Budget und Auswertung erscheint. |
| T-8 | Schnellerfassung: neue Transaktion mit der Tastatur in wenigen Schritten (Vorschlag: letzte Kategorie/Konto vorbelegt, Autovervollständigung der Beschreibung). |
| T-9 | Die Liste je Monat ist filter- und sortierbar (Kategorie, Konto, Typ, Textsuche). |

### 4.4 Jahresbudget

| ID | Anforderung |
|---|---|
| B-1 | Zu Jahresbeginn legt der Benutzer ein **Budget für ein Kalenderjahr** an, ein Budget pro Jahr. |
| B-2 | Pro Ausgabe-Kategorie wird das **variable Monatsbudget** geschätzt (Betrag je Monat). |
| B-3 | **Monatsbudget einer Kategorie = variables Monatsbudget + monatlicher Anteil der zugeordneten Fixkosten.** |
| B-4 | **Jahresbudget einer Kategorie = Summe der zwölf Monatsbudgets** (Normalfall: Monatsbudget × 12). |
| B-5 | Das Budget kann unterjährig angepasst werden. Vergangene Monate bleiben dabei unverändert, Änderungen wirken ab einem gewählten Monat (Historie bleibt nachvollziehbar). |
| B-6 | Ein neues Jahresbudget kann aus dem Vorjahr kopiert werden (Beträge anpassbar). |
| B-7 | Budget-Totalzeile: Summe aller Kategorien. Zusätzlich geplantes Einkommen vs. geplante Ausgaben (geplanter Überschuss), sofern Einnahmen geplant werden (optional, siehe offene Punkte). |
| B-8 | Einnahme-Kategorien haben kein Budget, ausser optional ein erwartetes Monatseinkommen (siehe offene Punkte). |

### 4.5 Fixkosten

| ID | Anforderung |
|---|---|
| F-1 | Fixkosten werden **in einem separaten Bereich** des Jahresbudgets geführt (Beispiele: Versicherungen, Krankenkasse, Vereinsmitgliedschaft). |
| F-2 | Jede Fixkost hat: Bezeichnung, **Kategorie**, Betrag, **Intervall** (monatlich, vierteljährlich, halbjährlich, jährlich), Fälligkeitsmonat/-tag, Gültigkeitszeitraum, Konto (optional, Standardkonto). |
| F-3 | Fixkosten werden **auf das Monatsbudget heruntergerechnet** (Jahresbetrag ÷ 12) und fliessen automatisch in das Monatsbudget der Kategorie ein (B-3). |
| F-4 | Die Vorlage zeigt Betrag *monatlich* und *jährlich*. Die App zeigt beide Werte automatisch (monatlicher Betrag ⇄ jährlicher Betrag). |
| F-5 | Fixkosten werden **nicht automatisch gebucht**. Der Benutzer erfasst die Transaktion **manuell**. |
| F-6 | Pro Fälligkeitsperiode kann der Benutzer eine Fixkost **abhaken** (Status *bezahlt*). Abhaken ist **eine Markierung**, die mit einer manuell erfassten Transaktion verknüpft wird. Beim Abhaken wählt der Benutzer eine bestehende Transaktion aus oder erfasst direkt eine neue, vorausgefüllte. Ohne verknüpfte Transaktion kann nicht abgehakt werden. |
| F-7 | Die App zeigt pro Monat die **fälligen / offenen / bezahlten Fixkosten** (Checkliste). Überfällige Fixkosten sind markiert. |
| F-8 | Wird die verknüpfte Transaktion gelöscht, wird die Markierung zurückgesetzt (mit Hinweis). |
| F-9 | Die verknüpfte Zahlung zählt in „Laufend“ wie jede andere Ausgabe (keine Doppelzählung). |

### 4.6 Monatsansicht

Für jeden Monat gibt es einen eigenen Tab (Januar … Dezember) je Budgetjahr, zusätzlich die Tabs „Budget“, „Konten“ und „Jahresübersicht“.

| ID | Anforderung |
|---|---|
| M-1 | **Oben** im Monats-Tab: Budgettabelle analog Vorlage mit den Spalten *Kategorie, Jahresbudget, Monatsbudget, Laufend, Differenz (Monat), Kumulative Ausgabe, Differenz (Jahr)* und Totalzeile. |
| M-2 | Darunter: **Transaktionsliste** des Monats, eine Zeile pro Transaktion mit *Datum, Typ (Einnahme/Ausgabe), Betrag, Kategorie, Konto, Beschreibung*. |
| M-3 | Totalzeile der Transaktionen: Summe Einnahmen, Summe Ausgaben, **Endsaldo** (Einnahmen − Ausgaben des Monats). |
| M-4 | Zeilen mit überzogenem Monatsbudget sind farblich markiert (siehe 4.8). |
| M-5 | Fixkosten-Checkliste des Monats (siehe F-7) im Monats-Tab (Bereich rechts oben analog Vorlage). |
| M-6 | Neue Transaktionen können direkt im Monats-Tab erfasst werden (Inline oder Dialog). |
| M-7 | Optional ein kleines Diagramm „Verlauf der kumulativen Ausgaben“ (vgl. Vorlage). |
| M-8 | Berechnungen aktualisieren sich sofort bei jeder Änderung. |

### 4.7 Kontoübersicht

| ID | Anforderung |
|---|---|
| U-1 | Eine Übersicht zeigt **alle aktiven Konten als Kacheln**: Name, Kontotyp, aktueller Saldo/Wert, Mini-Verlaufsgrafik (Sparkline), Währung. |
| U-2 | Kopfzeile: **Gesamtvermögen** (Summe aller CHF-Konten), optional aufgeteilt nach Kontotyp. |
| U-3 | Ein Klick auf eine Kachel öffnet die **Detailansicht**: alle Kontoinformationen, Transaktionsliste des Kontos, Saldoentwicklung als Liniendiagramm. |
| U-4 | Der **Zeitraum** der Entwicklung ist frei wählbar (Voreinstellungen: 1 Monat, 3 Monate, Jahr, seit Anfang, benutzerdefiniert von–bis). |
| U-5 | Bei Anlagekonten zeigt die Detailansicht zusätzlich Wertstände, Einzahlungen und Gewinn/Verlust (absolut und in %). |
| U-6 | Konten lassen sich in der Übersicht anlegen, bearbeiten, ordnen und archivieren. |

### 4.8 Jahresübersicht / Jahresabschluss

| ID | Anforderung |
|---|---|
| J-1 | **Grafische Darstellung der Entwicklung aller Konten** über das Jahr (Liniendiagramm, Konten einzeln ein-/ausblendbar, optional gestapelt als Gesamtvermögen). |
| J-2 | **Grafische Darstellung der Ausgaben je Kategorie über das Jahr** (z. B. Säulendiagramm pro Monat, gestapelt nach Kategorie, zusätzlich Tabelle Kategorie × Monat). |
| J-3 | Vergleich Budget vs. Ist pro Kategorie (Monat und Jahr). |
| J-4 | **Monatliche Überziehung:** Eine Kategorie, deren Ist-Ausgaben im Monat das **Monatsbudget** übersteigen, wird in diesem Monat deutlich markiert (Farbe + Symbol + Tooltip mit Betrag der Überziehung). |
| J-5 | **Jährliche Überziehung:** Eine Kategorie, deren **kumulierte Jahresausgaben das Jahresbudget** übersteigen, wird separat und stärker gekennzeichnet (z. B. rot, anderes Symbol). |
| J-6 | Die Kennzeichnung erscheint in Monats-Tab, Jahresübersicht und Diagrammen einheitlich. Zusätzlich gibt es eine Warnstufe „nahe am Limit“ (Schwellwert konfigurierbar, Standard 90 %). |
| J-7 | Kennzahlen: Total Einnahmen, Total Ausgaben, Sparquote, Vermögenszuwachs, Anzahl überzogener Kategorien. |
| J-8 | Das Jahr ist wählbar. Der Jahresabschluss ist jederzeit abrufbar (auch unterjährig als „Stand heute“). |
| J-9 | Export der Jahresübersicht als **PDF** und der Transaktionen als **CSV/Excel** (nice-to-have, siehe Priorisierung). |

---

## 5. Datenmodell (konzeptionell)

```
Category      (Id, Name, Type[Income|Expense], Color, IsArchived)
AccountType   (Id, Name, Kind[Transactional|Investment])
Account       (Id, Name, AccountTypeId, Currency, OpeningBalance, OpeningDate, Color, IsArchived, SortOrder)
Transaction   (Id, Type[Income|Expense|Transfer], Date, Amount, Description,
               CategoryId?, AccountId, TargetAccountId?, FixedCostId?, CreatedAt, UpdatedAt)
ValuationSnapshot (Id, AccountId, Date, Value)          -- Wertstände Anlagekonten
Budget        (Id, Year)
BudgetLine    (Id, BudgetId, CategoryId, MonthFrom, VariableMonthlyAmount)
FixedCost     (Id, Name, CategoryId, Amount, Interval, DueMonth, DueDay, ValidFrom, ValidTo, DefaultAccountId?)
FixedCostPayment (Id, FixedCostId, PeriodYear, PeriodIndex, TransactionId, CheckedAt)
Setting       (Key, Value)    -- z. B. Warnschwelle, Backup-Pfad, Standardwährung
```

Regeln:

- Beträge als `decimal` (Rappen-genau), nie `double`.
- Datum ohne Zeitzone (`DateOnly`).
- Salden und Budget-Ist-Werte werden **berechnet**, nicht redundant gespeichert (ggf. gecacht).
- Alle IDs als GUID (Vorbereitung auf spätere Synchronisation).
- `CreatedAt`/`UpdatedAt` und Soft-Delete-Flag für spätere Sync-Fähigkeit.

---

## 6. Nicht-funktionale Anforderungen

### 6.1 Plattform & Technologie

| ID | Anforderung |
|---|---|
| P-1 | Windows 10/11, **.NET 8 (LTS)**, **WPF** mit MVVM (z. B. CommunityToolkit.Mvvm). |
| P-2 | Persistenz: lokale **SQLite-Datenbank mit SQLCipher-Verschlüsselung** (z. B. Entity Framework Core + `Microsoft.Data.Sqlite` mit SQLCipher). |
| P-3 | Diagramme über eine WPF-taugliche Bibliothek (z. B. LiveCharts2 oder OxyPlot). |
| P-4 | Schichten-Architektur: **UI (WPF) → Application/Services → Domain → Persistenz**. Die Fachlogik (Budget, Salden, Überziehung) liegt UI-unabhängig in einer separaten Class Library und ist per Unit-Tests abgedeckt. |
| P-5 | Installation per MSIX oder einfachem Installer/Portable-Variante. |
| P-6 | Sprache der Oberfläche: Deutsch (Schweiz), Zahlen-/Datumsformat de-CH, Lokalisierung vorbereiten. |

### 6.2 Sicherheit & Datenschutz

| ID | Anforderung |
|---|---|
| S-1 | Beim Start Abfrage eines **Master-Passworts**. Ohne korrektes Passwort ist kein Datenzugriff möglich. |
| S-2 | Die Datenbank wird **vollständig verschlüsselt** (SQLCipher, AES-256). Der Schlüssel wird per **KDF** (z. B. Argon2id oder PBKDF2 mit hoher Iterationszahl) aus dem Passwort abgeleitet. Das Passwort wird nie im Klartext gespeichert. |
| S-3 | **Keine Hintertür zur Wiederherstellung:** Bei Passwortverlust sind die Daten nicht rekonstruierbar. Die App weist bei der Einrichtung deutlich darauf hin. Optional wird ein **Recovery-Schlüssel** zum Ausdrucken/sicheren Aufbewahren erzeugt. |
| S-4 | Automatische **Sperre** nach konfigurierbarer Inaktivität (Standard 10 Minuten) sowie manuell. Beim Sperren werden Daten aus dem Speicher der UI geleert. |
| S-5 | **Backups sind verschlüsselt** (gleiches Verfahren). |
| S-6 | Keine Telemetrie, keine Netzwerkverbindung in Phase 1, keine Finanzdaten in Logs. |
| S-7 | Passwort-Änderung möglich (Re-Key der DB). Passwortrichtlinie: Mindestlänge 12 Zeichen, Stärkeanzeige. |
| S-8 | Ablage der Daten im Benutzerprofil (`%LOCALAPPDATA%\FinanceApp`) mit eingeschränkten Dateirechten. Temporäre Dateien/Exporte werden nach Gebrauch gelöscht, Exporte weisen auf die Unverschlüsselung hin. |
| S-9 | Bei mehrfach falscher Passworteingabe: Verzögerung (Rate-Limiting) der Eingabe. |
| S-10 | Abhängigkeiten werden aktuell gehalten (NuGet-Updates, Schwachstellen-Scan). |

### 6.3 Backup

| ID | Anforderung |
|---|---|
| BK-1 | **Monatlich** wird automatisch ein Backup erstellt, und zwar beim Start der App, sobald seit dem letzten Backup ein neuer Kalendermonat begonnen hat (kein Hintergrunddienst nötig). |
| BK-2 | Das Backup ist eine **verschlüsselte Kopie** der Datenbank mit Zeitstempel im Dateinamen (z. B. `FinanceApp_2026-10.fcbak`). |
| BK-3 | **Zielverzeichnis frei wählbar** (lokaler Ordner, USB-Laufwerk, synchronisierter Cloud-Ordner). Standard: `%USERPROFILE%\Documents\FinanceApp\Backups`. |
| BK-4 | Aufbewahrung konfigurierbar (Standard: letzte 12 Backups, ältere werden gelöscht). |
| BK-5 | Ein manuelles Backup ist jederzeit auslösbar. Die **Wiederherstellung** aus einem Backup erfolgt über die UI (mit Bestätigung, die aktuelle DB wird vorher gesichert). |
| BK-6 | Nach jedem Backup wird die Integrität geprüft (Öffnen + `PRAGMA integrity_check`). Fehler werden dem Benutzer gemeldet. Es gibt eine Erinnerung, falls das Zielverzeichnis nicht erreichbar ist. |

### 6.4 Qualität

| ID | Anforderung |
|---|---|
| Q-1 | Bedienung ohne Schulung, Tastaturbedienung der Erfassung. |
| Q-2 | Reaktionszeit < 200 ms bei bis zu ca. 100 000 Transaktionen. |
| Q-3 | Datenintegrität: Schreibvorgänge sind transaktional, keine Datenverluste bei Absturz. |
| Q-4 | Unit-Tests für Fachlogik (Budget, Fixkosten, Salden, Überziehungslogik), Test von Backup/Restore. |
| Q-5 | Datenbank-Schema mit Versionierung und Migrationen. |

---

## 7. Oberflächenstruktur

Hauptnavigation (links oder oben):

1. **Dashboard / Jahresübersicht** (J-1 … J-8)
2. **Budget** (Jahresbudget + Fixkosten) (B-*, F-*)
3. **Monate:** Tabs Januar … Dezember (M-*)
4. **Konten** (Kacheln → Detail) (U-*)
5. **Kategorien** (K-*)
6. **Einstellungen** (Sicherheit, Backup, Warnschwelle, Sprache)

Skizze Monats-Tab:

```
┌ Finanzkontrolle 2026 ─ Oktober ─────────────────────────────────────────┐
│ Kategorie │ Jahresb. │ Monatsb. │ Laufend │ Diff │ Kumuliert │ Diff     │
│ Essen/…   │   7 200  │    600   │   412   │ 188  │   3 980   │ 3 220    │
│ …                                                         ⚠ Überzogen   │
│ Total     │                                                             │
├──────────────────────────────────────────────  Fixkosten (Checkliste) ─┤
│ Datum │ Ausgabe │ Einnahme │ Kategorie │ Konto │ Beschreibung           │
│ …                                                                        │
│ Total / Endsaldo                                                         │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## 8. Anwendungsfälle (Auswahl)

1. **Jahr einrichten:** Budget für das Jahr anlegen (optional Vorjahr kopieren), variable Monatsbeträge je Kategorie eintragen, Fixkosten erfassen. Die App berechnet Monats- und Jahresbudget.
2. **Ausgabe erfassen:** Monat öffnen, „Neue Transaktion“, Typ Ausgabe, Datum, Betrag, Beschreibung, Kategorie, Konto, Speichern. Budgettabelle und Kontosaldo aktualisieren sich.
3. **Fixkost abhaken:** Fixkosten-Checkliste, Fixkost „Krankenkasse“, vorhandene Transaktion wählen oder neue anlegen, Status *bezahlt*.
4. **Umbuchung:** Debitkonto → Sparkonto, Betrag 500 CHF. Beide Salden ändern sich, das Budget bleibt unverändert.
5. **Anlagewert aktualisieren:** Konto „Fonds“, „Wertstand erfassen“, Datum + Wert. Verlauf und Gewinn/Verlust werden aktualisiert.
6. **Jahresabschluss:** Jahresübersicht, Diagramme prüfen, überzogene Kategorien (monatlich/jährlich) erkennen, ggf. PDF exportieren.
7. **Backup wiederherstellen:** Einstellungen, Backup, Datei wählen, Passwort bestätigen, Wiederherstellen.

---

## 9. Abnahmekriterien (Auszug)

- Eine Ausgabe in Kategorie X im Monat M erhöht „Laufend“ und „Kumulative Ausgabe“ der Kategorie exakt um den Betrag. Die Differenzen werden korrekt berechnet.
- Eine Fixkost von 1 200 CHF/Jahr erhöht das Monatsbudget ihrer Kategorie um 100 CHF.
- Eine Umbuchung verändert Kontosalden, aber nicht „Laufend“ irgendeiner Kategorie.
- Überschreitet „Laufend“ das Monatsbudget, ist die Kategorie in diesem Monat markiert. Überschreitet „Kumulative Ausgabe“ das Jahresbudget, trägt sie die Jahres-Kennzeichnung.
- Ohne korrektes Master-Passwort sind weder Datenbank noch Backups lesbar (Prüfung: Datei im Hex-/SQLite-Tool nicht öffnbar).
- Am Monatswechsel wird beim nächsten Start ein verschlüsseltes Backup erzeugt und erfolgreich auf Integrität geprüft.
- Die Wiederherstellung eines Backups ergibt exakt den gesicherten Datenstand.

---

## 10. Priorisierung / Roadmap

| Phase | Inhalt |
|---|---|
| **MVP** | Passwort + verschlüsselte DB, Kategorien, Konten (inkl. Typen), Transaktionen, Umbuchungen, Jahresbudget, Fixkosten (inkl. Abhaken), Monatsansicht, Kontoübersicht mit Kacheln und Detail, monatliches Backup + Restore |
| **Phase 2** | Jahresübersicht mit Diagrammen und Überziehungs-Kennzeichnung (J-*), Wertstände Anlagekonten, Sperre bei Inaktivität, Warnschwelle |
| **Phase 3** | Export (PDF/CSV/Excel), Excel-Import der bestehenden Vorlage (einmalige Datenübernahme, optional) |
| **Später** | Server-/Sync-Funktionalität, Mobile App (siehe 11) |

> Hinweis: Die Reihenfolge MVP/Phase 2 ist ein Vorschlag. Die Überziehungs-Markierung (J-4/J-5) kann bei Bedarf bereits ins MVP.

---

## 11. Ausblick: Server & Mobile App

Phase 1 läuft komplett lokal. Um später eine Mobile App zu ermöglichen, wird bereits jetzt vorbereitet:

- Fachlogik in separater, UI-unabhängiger Bibliothek (wiederverwendbar im Server bzw. in der Mobile App, z. B. .NET MAUI).
- GUID-IDs, `UpdatedAt`, Soft-Delete für spätere Synchronisation.
- Zugriff auf Daten über Repository-/Service-Schnittstellen, sodass ein Server-Backend (z. B. ASP.NET Core Web API) ergänzt werden kann.

Voraussetzungen für eine spätere Serveranbindung (separat zu spezifizieren): Ende-zu-Ende-Verschlüsselung (der Server sieht nur verschlüsselte Daten), Authentifizierung, Hosting (selbst gehostet vs. Cloud), Konfliktauflösung bei der Synchronisation.

---

## 12. Offene Punkte / Annahmen

Annahmen, die getroffen wurden und bestätigt werden sollten:

1. Das Budget wird **pro Kalenderjahr** geführt, ein abweichendes Geschäftsjahr ist nicht vorgesehen.
2. Nicht verbrauchtes Monatsbudget wird nicht „übertragen“. Stattdessen wird, wie in der Vorlage, zusätzlich die kumulierte Jahresdifferenz gezeigt.
3. Einnahmen werden nicht budgetiert (Einnahme-Kategorien dienen nur der Auswertung). *Alternative:* erwartetes Monatseinkommen je Arbeitgeber planen.
4. Fremdwährungskonten werden nicht in CHF umgerechnet (A-4).
5. Die Kategorien der Excel-Vorlage dienen als Startvorschlag, können aber angepasst werden.
6. Vorlagenfehler (z. B. die Beschriftung „Halbjahresbudget“ statt „Jahresbudget“ in einzelnen Monatsblättern, Formelbereiche mit unterschiedlicher Länge) werden nicht übernommen.
7. Die Schwelle „nahe am Limit“ beträgt 90 % (konfigurierbar).
