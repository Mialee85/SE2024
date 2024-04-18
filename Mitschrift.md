# Plan

|Zeit|Mo|Di|Mi|Do|Fr|
| ----- | ----- | ----- | ----- | ----- | ----- |
| 8  | -    |     - | SE P | SE VL | - |
| 10 | WS I | FP P  | SE P | - | - |
| 12 | WS I | WS II | FP VL| - | - |
| 14 | -    | WS II | -    | - | - |
| 16 | -    | -     | -    | - | - |

SE Praktikum - Termine: 10.04.2024, 24.04.2024, 15.05.2024, 05.06.2024, 19.06.2024
Prog 2 Praktikum - Termine: tba


# VL 04.04.2024
## Grundsätzliches
- 10.00 Uhr Praktikumsgruppen
- Laborprotokol schreiben -> Klausurvorbereitung
- Linux-System update 2022-04 LTS

## Inhalt
### Wichtiges
Node modules nicht in git !!!

### git
git init - lokales git erstellen
git status - pwd, branch, Änderungen ...
git diff - Veränderung
git log - Log
git reset --hard *commit-ID* - Änderungen löschen und zurück auf bestimmten Commit

### Docker
Nicht unter Windows

# VL 18.04.2024

Aufbau Docker Netzwerk mit
- phpMyAdmin Container
- MariaDB Container
- Admin verfügbar auf Port 80 für http
- var/lib/mysql von mariaDB auslagern auf anderen Speicherort.

Emfohlen Docker Login, um Container zu ziehen

## Docker-compose.yml
Immer Version angeben bei images.

Befehl zum hochfahren: "docker compose up"

Anhang -d nur für Produktivumgebung. In Entwicklung weg lassen und jeden Container in eigener Bash laufen lassen.

"docker-compose down" zum anhalten

"docker exec --tty -- interactive mariadb /bin/bash" Interactive shell innerhalb des Containers ansprechen

"docker compose up --build" Erzwingt neuen build der docker compose