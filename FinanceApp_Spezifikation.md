# Spezifikation Finanz App

Ich möchte gerne eine Finanz App entwickeln, in welchem ich meine Aus- und Einnahmen Überwachen kann. Jede Aus- und Eingabe (Transaktion) soll kategorisiert sein. Bspw. Essen/Trinken, Mobilität, Gesundheit, Haushalt, Sport etc. Einnahme Kategorien können unterschiedliche Arbeitsgeber sein. 

Zu Beginn eines Jahres möchte ich ein Jahresbudget erstellen. Für jede Kategorie wird dabei abgeschätzt was die monatlichen Ausgaben sind. Diese setzen sich jeweils zusammen aus Fixkosten und variablen Kosten. Die Fixkosten wird in einem separaten Teil geführt, hier kommen zum Beispiel Versicherungen, Krankenkasse aber auch Vereinsmitgliedschaftskosten. Jede dieser FIxkost wird kategorisiert und dann auf das monatliche Budget herunter gerechnet. 

Neben dem Budget sollen pro Monat ein eigener Tab auffindbar sein, innerhalb des Tabs sehe ich auf einen Blick zuoberst eine Tabelle mit allen Ausgaben. Siehe dazu im Finanzkontrolle_Vorlage.xlsx jeweils die Tabelle zuoberst beie einem "Monats" Sheet. 

Folgend soll pro Ausgabe eine Zeile zu finden sein. Die Zeile soll entweder als einnahme oder als Ausgabe hinterlegt sein, eine kleine Beschreibung und das Datum zeigen. Ausserdem wird für jede Zeile also für jede Ein- Ausgabe eine Kategorie gewählt. 

Bei einer Ein- und Ausgabe soll hinterlegt sein von welchem Konto diese Ausgabe getätigt wurde. Zum Beispeil vom Sparkonto oder vom Debitkonto. 

Kontoübersicht: 

Irgendwo soll eine Übersicht von allen Konten sein. Jedes Konto soll mit Guthaben hinterlegt sein. Die Übersicht würde ich mit Kacheln machen. Wenn ihc dann auf eine Kachel gehe sollen alle Informationen sowie die Entwciklung über einen gewählten Zeitraum ersichtlich sein. Ein Konto kann beispielsweise ein Anlegefonds sein, ein Sparkonto aber auch 3a bspw. Die Nutzerin soll dies beliebig wählen können. 

Jahresabschluss bzw. Übersicht

Grafische Darstellung der Entwicklung aller Konten so wie der Ausgaben je Kategorie über das Jahr. Kategorien welche überzogen werden monatlich sollen entsprechend markiert werden. Kategorien welche das Jahresbudget überstiegen haben sollen ebenfalls gekennzeichnet werden. 

Plattform

Das GUI soll auf Windows laufen und in C# geschrieben sein. 

Serveranbindung

Eventuell soll irgendwann noch eine Mobile App dazu entwickelt werden, dann bräuchte ich irgend eine Serverfunktionalität. 

Backup

Monatlich soll ein Backpu erstellt werden. 

Datenschutz

Finanzdaten sind sehr sensible Daten und ich wünsche mir daher, dass die App gut gesichert wird. 