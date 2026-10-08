# Tiles-Kursformat – Community-Fork für Moodle 5.3

Dieser Fork hält das Kursformat „Tiles“ (`format_tiles`) auf aktuellen Moodle-Versionen lauffähig.

## Warum es diesen Fork gibt

- Das Original-Plugin von David Watson (https://moodle.org/plugins/format_tiles) wird seit Januar 2026 nicht mehr aktualisiert.
  Die letzte offizielle Version ist 5.1.0.2 und unterstützt nur Moodle 5.1.
- Schon unter Moodle 5.2 funktioniert die offizielle Version nicht mehr richtig, weil `core/modal_factory` entfernt wurde.
  Der Icon-Picker ist nicht anklickbar, und in der Browser-Konsole erscheint ein „Uncaught Error“.
- Der Maintainer hat sich nicht geäußert. Die Support-Links liefern einen 404-Fehler.
  Diskussion: https://moodle.org/mod/forum/discuss.php?d=482550
- Mehrere Einrichtungen pflegen deshalb eigene Forks. Der aktivste stammt von ByCS (Bayern):
  https://github.com/bycs-lp/moodle-format_tiles

## Woraus dieser Stand besteht

| Quelle | Inhalt |
| --- | --- |
| `bycs-lp/master-forked` (Stand 24.08.2026) | Basis. Enthält u. a. MBS-10884 (Ersatz für das entfernte `modal_factory`, nötig ab Moodle 5.2), MBS-10576 und MBS-10738 |
| `bycs-lp/MBS-10701` | Fix: `install.xml` passt nicht zum Datenbankschema |
| `bycs-lp/MBS-11032` | Fix: Icon-Picker beim Anlegen eines neuen Kurses |
| `bycs-lp/MBS-11035` | Styling des animierten Navigationsbuttons |
| `bycs-lp/MBS-11185`, `MBS-11186`, `MBS-11313` | **Sicherheit:** Ausgaben werden escaped (XSS) |
| `bycs-lp/MBS-11187` | **Sicherheit:** Kachelbilder setzen nur mit Recht `moodle/course:update` |
| eigene Änderung | `restricted_context_exception` auf `core_external\` umgestellt; `supported = [501, 503]` |

Die bycs-Zweige waren am 08.10.2026 noch nicht in deren Hauptbranch übernommen. Sie ließen sich ohne Konflikte zusammenführen.

## Prüfung gegen Moodle 5.3

Die Upgrade-Notes von Moodle 5.3 (`UPGRADING.md`, Branch `MOODLE_503_STABLE`, Build 20261005) wurden mit dem Plugin-Code abgeglichen:

- Keine der in 5.3 **entfernten** APIs wird verwendet.
- In 5.3 sind folgende Klassen als **veraltet** markiert: `admin_setting_*`, `admin_settingpage` (neu: `core_admin\setting\…`) und die Legacy-Klassen `external_*`.
  Sie funktionieren weiter und erzeugen nur Debug-Hinweise.
  Sie werden bewusst **nicht** umgestellt, damit das Plugin weiter unter 5.1 und 5.2 läuft.
  Spätestens wenn Moodle sie entfernt, muss man sie umstellen.
- Seit 5.3 ist der Modal-Titel ein `<h2>` statt `<h5>`. `templates/course_module_subtile_inner.mustache` nutzt noch `<h5>`.
  Das ist ein kosmetisches Barrierefreiheitsthema und kein Fehler.
- Alle PHP-Dateien bestehen `php -l` (PHP 8.3).
- `amd/build/filter_buttons.min.js` ist älter als der Quellcode. Der Unterschied ist reine Formatierung, deshalb ist kein Neubau nötig.

## Teststatus

**Noch nicht im Browser auf Moodle 5.3 getestet.** Vor dem Produktiveinsatz auf einem Testsystem prüfen:

- [ ] Installation/Upgrade über eine bestehende Tiles-Version läuft ohne Fehler durch
- [ ] Kurs im Kachelformat anzeigen (als Lehrkraft und als Schüler)
- [ ] Bearbeitungsmodus: Kacheln anlegen, verschieben, löschen
- [ ] Icon-Picker: Icon und Foto setzen (in bestehendem und in neuem Kurs)
- [ ] Aktivitäten im Modal öffnen (PDF, Seite, URL)
- [ ] Fortschrittsanzeige auf den Kacheln
- [ ] Subsections innerhalb einer Kachel
- [ ] Filter-Buttons
- [ ] Browser-Konsole frei von JavaScript-Fehlern
- [ ] Backup und Wiederherstellung eines Tiles-Kurses

Ergebnis, Datum, Moodle-Build und Tester hier eintragen:

| Datum | Moodle-Version | Getestet von | Ergebnis |
| --- | --- | --- | --- |
| | | | |

## Bekannte offene Punkte

- Die Fortschrittsanzeige zählt Aktivitäten in Subsections nicht mit.
  Das stammt aus dem Original und ist hier nicht behoben.
  Gemeldet: https://moodle.org/mod/forum/discuss.php?d=380547

## Pflege: Änderungen von bycs nachziehen

```bash
git remote add bycs https://github.com/bycs-lp/moodle-format_tiles.git   # einmalig
git fetch bycs

# Neue Commits im bycs-Hauptbranch, die hier noch fehlen:
git log --oneline moodle53..bycs/master-forked

# Neue Fix-Zweige von bycs (nach Datum sortiert):
git for-each-ref --sort=-committerdate --format='%(committerdate:short) %(refname:short)' refs/remotes/bycs/ | head

# Übernehmen:
git merge bycs/master-forked
git merge bycs/<Zweigname>
```

Nach jedem Nachziehen `version.php` prüfen und `$plugin->version` erhöhen (Datum + laufende Nummer), damit Moodle das Update erkennt.

## Neue Moodle-Version unterstützen

1. In `UPGRADING.md` der neuen Moodle-Version die Abschnitte „Removed“ und „Deprecated“ lesen.
2. Prüfen, ob das Plugin betroffene Klassen oder Funktionen verwendet (`grep` im Plugin-Verzeichnis).
3. Anpassen und `$plugin->supported` erweitern.
4. Die Test-Checkliste oben auf einem Testsystem durchgehen.

## ZIP für die Installation bauen

```bash
git archive --format=zip --prefix=tiles/ -o format_tiles.zip HEAD
```

Das Verzeichnis im ZIP muss `tiles` heißen. Moodle installiert es nach `course/format/tiles`.

## Lizenz

GPL v3 oder neuer, wie das Original. Ursprünglicher Autor: David Watson (https://evolutioncode.uk).
