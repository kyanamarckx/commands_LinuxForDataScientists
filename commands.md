# Commando's uit de cursus
## Chapter 1 : User Management

### Permissies (herhaling)

Overzicht:
- `ls -l`

Bestandspermissies:
- `u`: gebruiker, **u**ser
- `g`: groep, **g**roup
- `o`: andere gebruikers, **o**thers
- `a`: iedereen, **a**ll

Soorten permissies:
- `r`: lezen, **r**ead
- `w`: schrijven, **w**rite
- `x`: uitvoeren, e**x**ecute
  - bestand: uitvoeren als commando
  - directory: toegang met `cd`

Permissies instellen:
- `chmod MODUS FILE`

Octale permissies opvragen:
- `stat -c %a BESTAND`


### Speciale Permissies

Permissies van nieuwe bestanden en opvragen:
- `umask`

Speciale permissies:
- `u+s`: SETUID (set user id), gebruiker krijgt de rechten van de eigenaar van het bestand
- `g+s`: SETGID (set group id), gebruiker krijgt rechten van de groep van het bestand
- `+t`: restricted deletion, op directories, bestand in directory mag enkel door eigenaar verwijderd worden


### Beheer van Gebruikers

Commando's voor gebruikers en groepen:
- Gebruikers:
  - `useradd`: gebruiker toevoegen
  - `usermod`: gebruiker wijzigen
  - `userdel`: gebruiker verwijderen
- Groepen:
  - `groupadd`: groep toevoegen
  - `groupmod`: groep wijzigen
  - `groupdel`: groep verwijderen
- Info opvragen: 
  - `who`: toont alle ingelogde gebruikers
  - `groups [username]...`: toont alle groepen waartoe de gebruiker behoort
  - `id`: toont user en group names en ID's

Configuratiebestanden
- `/etc/passwd`, `/etc/shadow`: gebruikers
- `/etc/group`(, `/etc/gshadow`): groepen

Primaire VS Aanvullende Groepen:
- `sudo usermod -g groep gebruiker`: primaire groep aanpassen
- `sudo usermod -G groep1,groep2 gebruiker`: gebruiker toevoegen aan opgegeven groepen en verwijderen uit alle andere groepen
- `sudo usermod -aG groep gebruiker`: gebruiker toevoegen aan opgegeven groep, blijft lid van andere groepen

Eigenaar/Groep Veranderen:
*root-rechten nodig*
  - `chown user file` 
  - `chown user:group file`
  - `chgrp group file`

Root of Administrator:
- `root`: oorspronkelijk de enige administrator van het systeem
- `sudo`: kan root-rechten verlenen aan gebruiker
  - voor een bepaald commando
  - voor alle commando's
- `/etc/sudoers` toont wie allemaal admin kan zijn

Wachtwoord:
- `passwd`: eigen wachtwoord aanpassen
- `sudo passwd <username>`: wachtwoord aanpassen van een gebruiker als administrator
- `openssl passwd -6 -salt <salt-string> <desired passwd>`: wachtwoord genereren in hash-vorm



## Chapter 2 : Combining Commands Towards Scripting

### Streams, Pipes, Redirects

Input & Output / Normaal Gedrag:
- `stdin`: standard input, invoer toestenbord
- `stdout`: standard output, afdrukken op scherm
- `stderr`: standard error, afdrukken op scherm

I/O Redirection:
- `cmd > file`: schrijf uitvoer van cmd weg naar file
- `cmd >> file`: voeg toe aan einde van file
- `cmd 2> file`: schrijf foutboodschappen van cmd weg naar file
- `cmd < file`: gebruik inhoud van file als invoer voor cmd
- `cmd1 | cmd2`: gebruik uitvoer van cmd1 als invoer voor cmd2

Foutboodschappen Afdrukken:
- `printf 'Error: %s is not a directory\n' "${dir}" >&2`

Here Documents:
- Als je meer dan 1 lijn wilt afdrukken:
  - `cat << _EOF_`
    
    `<CODE>`

    `_EOF_`
  
Pipes:
-  `<code1> | <code2>`: uitkomst van code1 wordt gebruikt als invoer voor code2

Here Strings:
- `<command> <<< "string"`: wordt gebruikt voor input redirection van tekst of een variabele


### Filters

Filters:
- `filter`

  1. leest van stdin of bestand,
  2. tekst transformeert en,
  3. wegschrijft naar stdout

- Voorbeeld:
  - `filter file1 file2...`
  - `filter < file`
  - `cmd | filter`

cat, tac en shuf:
- `cat`: wat binnenkomt op de stdin afdrukken op de stdout
- `tac`: idem, maar in omgekeerde volgorde
- `shuf`: in willekeurige volgorde

head en tail:
- `head`: toont eerste 10 lijnen van bestand
- `tail`: toont laatste 10 lijnen van bestand
- `head -5` of `tail -5`: toont eerste/laatste 5 lijnen van bestand

cut, paste en join:
- `cut`: selecteer kolommen uit gestructureerde tekst (bvb. CSV bestand)
- `paste`: voeg bestanden regel per regel samen
- `join`: voeg bestanden samen adhv gemeenschappelijke kolom

sort en uniq
- `sort`: inhoud van bestand sorteren
- `uniq -c`: inhoud van bestand sorteren en dubbele uithalen

fmt, nl, wc:
- `fmt`: formatter voor tekstbestanden
- `nl`: numbering lines
- `wc`: word count

column:
- `column`: invoer in kolommen afdrukken
  - `-t`, `--table`: afdrukken in kolommen
  - `-J`, `--json`: vertalen naar JSON
  - `-s`, `--separator`: input separator
  - `-N`, `--table-columns`: namen velden

Zoeken in een file:
- `grep`

tr:
- `tr`:
  - **tr**anslate
  - teken per teken ipv lijn per lijn
  - enkel stdin, geen files

Stream Editor:
- `sed`

AWK-taal:
- `awk {command}`: wat tussen de accolades staat wordt uitgevoerd op elke regel


### Intro Bash Scripts

Een Script Schrijven:
1. `sudo nano script.sh`: maak een bestand aan met een teksteditor
2. `#! /bin/bash`
   
   `echo "Hello ${USER}"`
3. `chmod +x script.sh`: maak bestand uitvoerbaar
4. `./script.sh`: voer script uit

De shebang:
- `#!`: shebang, eerste regel van een script
  - absoluut pad naar de interpreter voor het script

Variabelen:
- `variabele=waarde`: declaratie, nooit spaties rond = en nooit spaties in de naam van de variabele
- `${variabele}`: waarde van de variabele opvragen
- Gebruik in strings met dubbele quotes

Onbestaande variabelen opvragen:
- Onbestaande variabele wordt beschouwd als *lege string*
  - Oorzaak van veel fouten in shell-scripts
  - `set -o nounset`: script afbreken

Zichtbaarheid Variabelen (scope):
- Enkel binnen zelfde shell, niet binnen subshells
  - Script oproepen creëert een subshell
  - Maak "globale", of *omgevingsvariabele* met `export`

Naamgeving Variabelen:
- Kleine letters, bvb. `foo_bar`: lokale variabelen
- Hoofdletters, bvb. `FOO_BAR`: omgevingsvariabelen


### Vi(m) VS Nano

Bestand Aanmaken:
1. `vim bestand.txt`: met teksteditor Vi/Vim
2. `nano bestand.txt`: met teksteditor Nano
3. `touch bestand.txt`: leeg bestand

Vim Survival Guide:
- `i`: normal mode naar insert mode
- `<Esc>`: insert mode naar normal mode
- `:w`: opslaan
- `:wq`: opslaan en afsluiten
- `:q!`: afsluiten zonder opslaan



## Chapter 3.1 : Software installatie, netwerkconfiguratie

### Software installatie

Debian dpkg:
- `dpkg -i <package_name>.deb`: installeer .deb package met dpkg
- `dpkg -l`: overzicht van geïnstalleerde packages op Debian
  
Debian apt:
- `apt install <package_name>`: package installeren 
- `sudo apt update`: bijwerken van info op de repo servers
- `sudo apt upgrade`: bijwerken van alle packages op jouw systeem
- `sudo apt install <package_name>`: nieuwe versie wordt geïnstalleerd
  
Red hat yum/dnf:
- `rpm`: RedHat Package Manager
- `yum`: Yellowdog Update Manager
- `dnf`: DaNdiFied yum
- `sudo dnf install <package_name>`: installatie package
- `dnf check-update`: bijwerken van info op de repo servers; aftoetsen van wat kan bijgewerkt worden
- `sudo dnf upgrade`: bijwerken van alle packages op jouw systeem
- `sudo dnf ugrade <package_name>`: bijwerken van enkele package op jouw systeem
- `dnf list installed`: lijst van geïnstalleerde packages
- `dnf list available`: lijst beschikbare packages
- `dnf provides *bin/fortune`: commando fortune installeren
- `dnf deplist curl`: dependencies van curl


## Chapter 3.2 : Collecting Data, Web Scraping with curl

### Interacting with Web Servers from the Terminal

cURL:
- `curl <link>`: print uitvoer naar stdout
- `curl '<link>' > <file>`: print uitvoer naar <file> en print progress information naar stdout 
  - `-s` of `--silent`: schakelt print van progress information uit

HTTP Request Methods:
- `curl -X GET <link>`
- `curl -X POST <link>`
- `curl -X PUT <link>`
- `curl -X DELETE <link>`

Saving the Result:
- `-o`, `--output <file>`
- `-O`, `--remote-name`

Show Headers:
- `-i`, `--include`: toont ook de HTTP response headers
- `-I`, `--head`: toont **enkel** de headers

Follow Redirects:
- `-L`, `--location`

POST Data:
- `-d`, `--data var=value&var=value` (als je een & moet gebruiken moet je de link tussen enkele quotes zetten: '')



## Chapter 4 : Installatie van een Webserver, Scripting

### Webserver Installatie

Services opstarten:
- `sudo systemctl start <service>`: service nu manueel opstarten
- `sudo systemctl enable <service>`: service automatisch opstarten bij booten

Test de services:
- `systemctl status <service>`: de status van de service opvragen

Toon sockets/poorten in gebruik:
- `ss`: toon de sockets
  - `-l`, `--listening`: toont de server sockets
  - `-t`, `--tcp`: toont de TCP sockets
  - `-u`, `--udp`: toont de UDP sockets
  - `-n`, `--numeric`: toont de poortnummers
  - `-p`, `--processes`: toont de processen (root permissies zijn verplicht)
- `netstat`: bestaat niet meer, is veranderd door `ss`

Logbestanden:
- `sudo journalctl`: toon logbestanden

journalctl:
- root permissies zijn nodig
- `-f`, `--folow`: toont de laatste logs en wacht voor veranderingen
- `-u <service>`, `--unit=<service>`: toont enkel de logs van de service
- `/usr/sbin/dhclient`: match executable (dhclient)
- `/dev/sda`: match device node (/dev/sda)
- `_TRANSPORT=audit`: toont de audit logs
- `-b`, `--boot`: toont log sinds de laatste boot
- `-k`, `--dmesg`: toont kernel berichten (zoals dmesg)
- `-r`, `--reverse`: toont de output omgekeerd (nieuwste eerst)
- `-p err`, `--priority=err`: toont alleen errors en erger
- `--since=yesterday`: sinds gisteren

Website vanaf GUI Linux VM bekijken:
- `ip a`: toont het ip adres


### Scripting (vervolg)

Positionele parameters:
- `${0}`: naam van het script
- `${1}, ${2}, ...`: eerste, tweede, ... argument
- `${10}`: tiende argument
- `${*}`: alle argumenten; ${1} ${2} ${3} ...
- `${@}`: alle argumenten; "\${1}" "\${2}" "\${3}" ...
- `${#}`: aantal positionele parameters
- `shift`: schuift positionele parameters op naar links
- `set par1 par2 par3`: positionele parameters instellen

Exit-status:
- `echo "$?"`: 0 als het commando geslaagd is, 1-255 als commando gefaald is

If-lus: 
- `if EXPRESSION`
  
  `then`
  
    `# ...`
  
  `elif EXPRESSION`
  
  `then`
  
    `# ...`
  
  `else`
  
    `# ...`
  
  `fi`

- `if [ "${#}" -gt '2' ]; then`
  
    `printf 'Expected at most 2 arguments, got %d\n' "${#}" >&2`

    `exit 1`

  `fi`

While-lus:
- `while EXPRESSION; do`

    `# ...`

  `done`

Until-lus:
- `until EXPRESSION; do`
  
    `# ...`

  `done`

While-lus met teller:
- `counter=0`
  
  `while [ "${counter}" -le '10' ]; do`

    `echo "${counter}"`

    `counter=$((counter + 1))`

  `done`

For-lus:
- `for ITEM in LIST; do`
  
    `# ...`

  `done`

- `for FILE in *.md; do`

    `printf 'Processing file %s\n' "${file}"`

    `# ...`

  `done`

For-lus met teller:
- `for i in {1..10}; do`
  
    `echo "${i}"`
  
  `done`
- `for i in {2..20..2}; do`
  
    `echo "${i}"`

  `done`
- `for i in $(seq 1 10); do`
  
    `echo "{i}"`

  `done`
- `for i in $(seq 2 2 20); do`
  
    `echo "${i}"`

  `done`
- `for ((i=0; i<=10; i++)); do`

    `echo "${i}"`

  `done`

Itereren over positionele parameters (while):
- `while [ "$#" -gt 0 ]; do`
  
    `printf 'Arg: %s\n' "${1}"`

    `# ...`

    `shift`

  `done`

Itereren over positionele parameters (for)
- `for arg in "${@}"; do`
  
    `printf 'Arg: %s\n' "${arg}"`

    `# ...`

  `done`


### Opzetten werkomgeving

Basisconfiguratie Git:
- `git config --global user.name "VOORNAAM NAAM"`
- `git config --global user.email "VOORNAAM.NAAM@student.hogent.be"`
- `git config --global push.default simple`

Werken met Git:
- `git status`: toont de status van de working directory en de staging area (toont welke veranderingen al gestaged zijn en welke niet)
- `git add .`: voegt de veranderingen aan de working directory toe
- `git commit -m "beschrijving wijzigingen"`: kijkt naar de veranderingen en verwijderd / voegt ze toe aan de working directory
- `git push`: upload de lokale repo naar de remote repo



## Chapter 5: Advanced Text Processing

### Pattern Matching

Globbing vs Regex:
- `man 7 glob`: manpage van globbing bekijken
- `man 7 regex`: manpage van regex bekijken

Meerdere bestanden opgeven met globbing:
- `cp a.txt b.doc c.jpg /tmp`: elk bestand apart opgeven 
- `cp /media/usbstick/*.jpg ~/Pictures/`: globbing-patronen gebruiken

Globbing-patronen:
- `?`: één willekeurig teken
- `*`: willekeurige string (ook leeg)
- `[...]`: elk teken opgesomd tussen []
- `[A-Z]`: van A tem Z
- `[!...]`: niet & van de opgesomde tekens


### Advanced AWK

Basisstructuur:
- `{action}`: voer uit voor elke lijn
- `/regex/ { action }`: voer enkel uit als regex overeenkomt
- `condition { action }`: voer enkel uit als voorwaarde voldaan is
- `{ print }`: wordt verondersteld indien er geen actie is

Begin, End:
- `BEGIN { ... }`: voer één keer uit, vóór verwerken input
- `END { ... }`: voer één keer uit, na verwerken input

Nuttige voorgedefinieerde variabelen:
- `FNR`: huidige lijnnummer
- `FS`: Field Separator (equivalent van optie -F)
- `NF`: Number Fields (aantal kolommen)
- `NR`: Number of Records (aantal lijnen/rijen)
- `OFS`: Output Field separator


### JSON Parsing: jq

jq als pretty printer:
- `curl -s '<link>' | jq`: geeft een mooiere stdout
- `curl -s '<link>' | jq '.[0]'`: toont enkel eerste element
- `jq '.[0] | {message: .commit.message, name: .commit.committer.name}'`: toont enkel specifieke velden
  - `.key` haal het key-veld op
- `jq '.[] | {message: .commit.message, name: .commit.committer.name}'`: geeft elk element van de array terug (niet in JSON)
- `jq '[.[] | {message: .commit.message, name: .commit.committer.name}]'`: geeft elk element van de array als JSON terug



## Chapter 6: Scripting

### Scripting (vervolg)

Fouten opsporen:
- werk stap voor stap
- open 2 vensters tegelijk (editor en terminal)
- `bash -n script.sh`: syntax check van script.sh
- `shellcheck script.sh`: shellcheck van script.sh
- `printf` en `echo`: druk veel info af
- `bash -x script.sh`: debug mode in terminal
  - `set -x` en `set +x`: debug mode aan en uitzetten

Fouten voorkomen:
- begin elk script met:
  - `set -o errexit`: abort on nonzero exit-status
  - `set -o nounse`: abort on unbound variable
  - `set -o pipefail`: don't hide errors within pipes

Booleans in Bash:
- booleaanse variabelen bestaan niet
- wel gelijknamige commando's `true` en `false`
  - zijn gebaseerd op exit-status van een proces

Logische operatoren:
- `if COMMAND; then`
  
    `# A`

  `else`

    `# B`

  `fi`
  - A-blok wordt uitgevoerd als exit-status van COMMAND 0 is (geslaagd, TRUE)
  - B-blok wordt uitgevoerd als exit-status van COMMAND verschillend is van 0 (gefaald, FALSE)

Operatoren && en ||:
- `comand1 && command2`: command2 wordt enkel uitgevierd als command succesvol was (exit 0)
- `command1 || command2`: command2 wordt enkel uitgevoerd als command 1 niet succesvol was (exit ≠ 0)

Het commando test:
- evalueren logische expressies
- geeft geschikte exit-status
  - 0 = TRUE
  - 1 = FALSE
- ` [ `: alias voor test
  - is een commando
  - spaties voor en na
