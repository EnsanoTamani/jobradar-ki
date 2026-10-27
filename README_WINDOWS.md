# JobRadar KI – Online-Web-App (ohne Python)

## Was enthalten ist
- Ein Admin-Login (nur Jobcoach, keine Teilnehmer-Logins)
- Teilnehmer anlegen, bearbeiten, löschen und mit mehreren Berufsbegriffen versehen
- PostgreSQL-Datenbank mit Stellen, Suchbegriffen, Zuordnungen und Bewerbungsstatus
- PDF-Export aller Stellen oder pro Teilnehmer mit Firma, Ort, E-Mail, Telefon und Anzeigenlink
- Adzuna-Integration für automatischen Abruf (optional, benötigt API-Schlüssel und Freigabe zur vorgesehenen Nutzung)
- Nur Stellen mit einer aus der Anzeigenbeschreibung gewonnenen Bewerbungs-E-Mail werden importiert.
- Manuelles Erfassen von Stellen aus Kleinanzeigen, Arbeitsagentur, Indeed etc. mit E-Mail

## WICHTIG: Umfang
Die Suche in Adzuna ist eine erste Datenquelle, keine vollständige Suche im gesamten Internet. Portale wie Kleinanzeigen, Indeed und die Bundesagentur für Arbeit werden NICHT automatisiert durchsucht. E-Mail-Adressen werden aus verfügbaren API-Beschreibungen erkannt; fehlen sie, wird die Stelle nicht importiert. Matching ist ein einfacher Berufsbegriffs- und Kenntnisseabgleich, keine trainierte KI. Radius und Arbeitszeit werden derzeit im Profil gespeichert, aber NICHT zuverlässig als harte Matching-Bedingungen geprüft. Für produktiven Einsatz sind zusätzliche Prüfungen und Datenschutzmaßnahmen nötig.

## Online veröffentlichen (Windows, ohne Installation von Python)
1. Konto bei https://github.com erstellen und ein **privates** Repository `jobradar-ki` anlegen.
2. Inhalt des entpackten Projektordners (nicht die ZIP selbst) im GitHub-Repository hochladen. **Niemals eine echte .env-Datei hochladen.**
3. Konto bei https://render.com anlegen und mit GitHub verbinden.
4. In Render auf **New → Blueprint** gehen und das Repository auswählen. Die mitgelieferte `render.yaml` legt einen Webdienst und eine PostgreSQL-Datenbank in Frankfurt an. Die ausgewählten Tarife können kostenpflichtig sein; Preise vor Bestätigung kontrollieren.
5. Bei der Einrichtung als geheime Umgebungsvariablen eintragen:
   - `ADMIN_EMAIL`: deine gewünschte Login-E-Mail
   - `ADMIN_PASSWORD`: ein **neues, starkes Passwort mit mindestens 12 Zeichen** (nicht ein bereits im Chat geteiltes Passwort)
   - `ADZUNA_APP_ID` und `ADZUNA_APP_KEY`: optional, beantragbar unter https://developer.adzuna.com/
   - `SESSION_SECRET` wird vom Blueprint generiert, `DATABASE_URL` automatisch verbunden.
6. Auf **Apply / Deploy** klicken, bis Render die Webadresse `https://...onrender.com` anbietet.
7. Webseite öffnen, mit der Admin-E-Mail und dem Admin-Passwort anmelden. Unter Teilnehmer ein Profil speichern. Unter Stellen neue Anzeigen erfassen und den PDF-Export testen.
8. Mit gültigen Adzuna-Schlüsseln läuft die Suche laut Voreinstellung einmal pro Stunde (`SEARCH_SCHEDULE=0 * * * *`), solange der Webdienst läuft. Ein zusätzlicher Klick **Jetzt suchen** startet sie auch manuell.

## Datenschutz / vor echtem Einsatz
Diese Version ist ein technischer MVP-Prototyp, nicht automatisch DSGVO-fertig. Vor Eingabe realer Teilnehmerdaten: Rechtsgrundlage und Information der Betroffenen klären, Auftragsverarbeitungsvertrag mit Hostinganbieter prüfen, Lösch-/Aufbewahrungsrichtlinie festlegen, Sicherheit prüfen und Schutz der Zugangsdaten sicherstellen. Für sensible Daten zuerst nur Testprofile verwenden. Nur eine vertrauenswürdige, dauerhaft aktive Instanz betreiben. Rate-Limit bei Login ist aktuell pro Prozess im Speicher und für größere Installationen durch einen zentralen Limiter zu ersetzen.

## Optional lokal starten (falls Node.js vorhanden)
`npm install` → `.env.example` als `.env` speichern und ausfüllen → `npm start` → http://localhost:3000
Für lokale Ausführung muss PostgreSQL erreichbar sein.

## Hinweise zum Betrieb
- Render-Webdienste nutzen standardmäßig ein flüchtiges Dateisystem: die App speichert Teilnehmer daher in PostgreSQL.
- Bei kostenlosen/pausierenden Hostingangeboten funktioniert die zeitgesteuerte Suche nicht zuverlässig. Verwende für echte Hintergrundsuche einen stets laufenden Dienst.
- Aus der Adzuna-Kurzbeschreibung extrahierte Adressen sind nicht zwangsläufig für Bewerbungen bestimmt. Vor Kontaktaufnahme prüfen.
- Produktiver Betrieb erfordert außerdem Monitoring, Backups, Datenexport/Löschung und ggf. externe Sicherheitsprüfung.
