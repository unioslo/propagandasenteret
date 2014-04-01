## Propagandasenteret

### Funksjon

Propagandasenteret består av 
- et grafisk brukergrensesnitt ("kontrollrommet" `propagandasenteret.hta`) som man kjører på sin egen maskin
- et klientscript (`infoskjerm_controller.vbs`) som kjører fra en *delt lokal mappe* (f.eks. `C:\SHOW`) på infoskjermmaskinene (klientene) og følger med på endringer i mappen. 

Klientscriptet starter alltid den nyeste powerpoint-filen i mappen sin. Eldre filer flyttes automatisk til en arkiv-mappe. For å unngå å låse powerpoint-filen, tar scriptet en kopi og starter kopien. Hvis scriptet ser at den aktive powerpointfilen har blitt endret, starter den endrede filen. Scriptet venter til filen er lukket, slik at man kan lagre underveis mens man jobber med en fil uten at endringene umiddelbart blir synlige.

Kontrollrommet og klientscriptene kommuniserer kun med hverandre ved hjelp av filer. Det er dermed ikke nødvendig å åpne noen nye porter, men vanlig fildeling må fungere. Fra kontrollrommet kan man også omstarte infoskjermscriptet (ved problemer) eller maskinen (ved alvorlige problemer). Kontrollrommet gir beskjed til klienten ved å opprette spesielle filer i den delte mappen.

### Installasjon

[Last ned en zip](https://github.com/scriptotek/propagandasenter/archive/master.zip) og pakk ut filene.

#### På klientene (infoskjerm-maskinene)

- Opprett mappen `C:\SHOW` og undermappene `C:\SHOW\script` og `C:\SHOW\arkiv`. 
- Kopier `infoskjerm_controller.vbs` til `C:\SHOW\script`
- Del mappen `C:\SHOW` med alle som skal bruke Propagandasenteret (standard mappedeling i Windows)
- Kopier filene i mappen `oppstartsscript` til en oppstartsmappe (`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup` på vår versjon av Windows). Det ene av disse scriptene tar seg av å holde `infoskjerm_controller.vbs`-scriptet i live og eventuelt omstarte. Det andre tar seg av å omstarte maskinen hvis man ber om det fra kontrollrommet.

#### Kontrollrommet

Før man kan kjøre `propagandasenteret.hta` må man konfigurere hvilke klienter den skal sjekke. Dette gjøres ved å åpne filen i en teksteditor, f.eks. Notepad, og redigere listen over klienter som starter på linje 177. Her er listen slik den er satt opp for våre fem maskiner:

    machines = [
      ["Foajé inngang", "ubreal59"],
      ["Foajé øst", "ubreal42"],
      ["Skranken", "ubreal36"],
      ["2. messanin", "ubreal54"],
      ["Bjørnehjørnet", "ubreal41"]
    ],

Det er ingen begrensninger på hvor mange maskiner man kan ha med i listen.

![Kontrollrommet](https://raw.github.com/scriptotek/propagandasenter/master/propagandasenteret.png)

Etter man har lagret kan `propagandasenteret.hta` kjøres direkte.  Det følger imidlertid også med et script, `start_propagandasenteret.bat`, som man kan bruke hvis man vil kjøre programmet fra en nettverksdisk. `start_propagandasenteret.bat` starter Propagandasenteret fra en lokal mappe, `%APPDATA%\Scriptotek\Propagandasenteret`, og tar seg av å kopiere filene dit hvis de ikke allerede finnes. Det tar seg også av å oppdatere filene hvis versjonen på nettverksdisken har blitt oppdatert. Dette er praktisk hvis mange skal bruke programmet. Hvis en ny person hos oss vil bruke Propagandasenteret, lager vi derfor en snarvei fra `start_propagandasenteret.bat` på nettverskdisken vår til personens skriverbord. Vi legger også gjerne på ikonet fra `Broadcast.ico`. 

### Spørsmål og svar

*Hva hvis PowerPoint kræsjer?*

PowerPoint *vil* kræsje en sjelden gang. Scriptet vil da automatisk starte programmet på nytt, *men* det er da viktig at ikke en dialogboks blokkerer systemet!
 - For å skru av "Windows is checking for a solution…", se <http://tinyurl.com/btfc6fl>
 - For å skru av "auto recovery"; File > Powerpoint options > Save og fjern
   avkryssing for "Save autorecover information every ..."

*Låser scriptet den aktive PowerPoint-filen?*

Nei, scriptet lagrer en midlertidig kopi, som den kjører istedet for originalfilen. Denne legges i scriptFolder, skjules, og startes i readonly-modus (hvorfor ikke?)

*Kan jeg vise nettsider i PowerPoint?*

Ja, ved hjelp av [LiveWeb](http://skp.mvps.org/liveweb.htm). Merk at denne vil bruke en gammel versjon av IE med mindre nettsiden man viser indikerer støtte for nyere versjoner, f.eks. ved hjelp av `<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" >`

*Kan scriptet vise en bestemt nettside (f.eks. en nedtelling) på alle skjermer like før stengetid?*

Ja, men det krever at man setter opp nettsiden selv. I `infoskjerm_controller.vbs` kan man skru på `aapningstiderEnabled`, angi åpningstider i `aapningstider`-lista (standard er 8-22 alle dager), og angi URLer til nettside som skal vises rett før stenging og etter stenging på hhv. linje 567 og 556.
