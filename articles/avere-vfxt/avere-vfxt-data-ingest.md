---
title: Flytta data till Avere vFXT för Azure
description: Så här lägger du till data i en ny lagringsvolym för användning med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153726"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Flytta data till vFXT-klustret - Parallell data intag

När du har skapat ett nytt vFXT-kluster kan din första uppgift vara att flytta data till en ny lagringsvolym i Azure. Men om din vanliga metod för att flytta data utfärdar ett enkelt kopieringskommando från en klient, kommer du förmodligen att se en långsam kopieringsprestanda. Entrådad kopiering är inte ett bra alternativ för att kopiera data till Avere vFXT-klustrets serverdelslagring.

Eftersom Avere vFXT för Azure-klustret är en skalbar cache för flera klienter, är det snabbaste och mest effektiva sättet att kopiera data till det med flera klienter. Den här tekniken parallelliserar inmatning av filer och objekt.

![Diagram som visar dataförflyttning med flera klienter: Längst upp till vänster har en ikon för lokal maskinvarulagring flera pilar som kommer från den. Pilarna pekar på fyra klientdatorer. Från varje klientmaskin pekar tre pilar mot Avere vFXT. Från Avere vFXT pekar flera pilar på Blob-lagring.](media/avere-vfxt-parallel-ingest.png)

De ``cp`` ``copy`` kommandon eller kommandon som ofta används för att använda för att överföra data från ett lagringssystem till ett annat är processer med en tråd som bara kopierar en fil i taget. Det innebär att filservern bara matar in en fil i taget , vilket är slöseri med klustrets resurser.

I den här artikeln beskrivs strategier för att skapa ett filkopieringssystem med flera klienter med flera trådar för att flytta data till Avere vFXT-klustret. Det förklarar filöverföring begrepp och beslutspunkter som kan användas för effektiv datakopiering med hjälp av flera klienter och enkla kopieringskommandon.

Det förklarar också några verktyg som kan hjälpa. Verktyget ``msrsync`` kan användas för att delvis automatisera processen att dela upp en ``rsync`` datauppsättning i buckets och använda kommandon. Skriptet ``parallelcp`` är ett annat verktyg som läser källkatalogen och utfärdar kopieringskommandon automatiskt. ``rsync`` Verktyget kan också användas i två faser för att ge en snabbare kopia som fortfarande ger datakonsekvens.

Klicka på länken för att hoppa till ett avsnitt:

* [Exempel på manuell kopia](#manual-copy-example) - En grundlig förklaring med hjälp av kopieringskommandon
* [Exempel på tvåfasrsynkron](#use-a-two-phase-rsync-process)
* [Delvis automatiserat (msrsync) exempel](#use-the-msrsync-utility)
* [Exempel på parallellkopia](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Mall för dataingestor virtuell dator

En Resource Manager-mall är tillgänglig på GitHub för att automatiskt skapa en virtuell dator med de parallella datainmatningsverktyg som nämns i den här artikeln.

![diagram som visar flera pilar vardera från bloblagring, maskinvarulagring och Azure-filkällor. Pilarna pekar på en "data ingestor vm" och därifrån pekar flera pilar på Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Dataingestor VM är en del av en självstudiekurs där den nyskapade virtuella datorn monterar Avere vFXT-klustret och hämtar sitt bootstrap-skript från klustret. Läs [Bootstrap en dataingestor VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) för mer information.

## <a name="strategic-planning"></a>Strategisk planering

När du utformar en strategi för att kopiera data parallellt bör du förstå kompromisserna i filstorlek, filantal och katalogdjup.

* När filerna är små är det mått av intresse filer per sekund.
* När filerna är stora (10MiBi eller mer) är måttet av intresse byte per sekund.

Varje kopieringsprocess har en dataflödeshastighet och en överförd filhastighet, som kan mätas med tidsinställning av kopieringskommandots längd och factoring av filstorlek och filantal. Att förklara hur du mäter priserna ligger utanför det här dokumentets omfattning, men det är viktigt att förstå om du kommer att hantera små eller stora filer.

## <a name="manual-copy-example"></a>Exempel på manuell kopia

Du kan manuellt skapa en kopia med flera trådar på en klient genom att köra mer än ett kopieringskommando samtidigt i bakgrunden mot fördefinierade uppsättningar med filer eller sökvägar.

Kommandot Linux/UNIX ``cp`` innehåller ``-p`` argumentet att bevara ägarskap och mtime-metadata. Det är valfritt att lägga till det här argumentet i kommandona nedan. (Om du lägger till argumentet ökar antalet filsystemanrop som skickas från klienten till målfilsystemet för metadataändring.)

I det här enkla exemplet kopieras två filer parallellt:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

När du har utfärdat `jobs` det här kommandot visar kommandot att två trådar körs.

### <a name="predictable-filename-structure"></a>Förutsägbar filnamnsstruktur

Om filnamnen är förutsägbara kan du använda uttryck för att skapa parallella kopieringstrådar.

Om katalogen till exempel innehåller 1 000 filer som numreras sekventiellt från `0001` till `1000`kan du använda följande uttryck för att skapa tio parallella trådar som varje kopia av 100 filer:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

### <a name="unknown-filename-structure"></a>Okänd filnamnsstruktur

Om filnamnsstrukturen inte är förutsägbar kan du gruppera filer efter katalognamn.

I det här exemplet samlas ``cp`` hela kataloger för att skicka till kommandon som körs som bakgrundsuppgifter:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

När filerna har samlats in kan du köra parallella kopieringskommandon för att rekursivt kopiera underkatalogerna och allt innehåll:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>När ska monteringspunkter läggas till

När du har tillräckligt med parallella trådar som går mot en enda målfilsystemmonteringspunkt, kommer det att finnas en punkt där lägga till fler trådar inte ger mer dataflöde. (Dataflödet mäts i filer/sekund eller byte/sekund, beroende på vilken typ av data du har.) Eller ännu värre, över-threading kan ibland orsaka en genomströmning nedbrytning.

När detta händer kan du lägga till fästpunkter på klientsidan till andra IP-adresser för vFXT-kluster med samma fjärrfilsystemsmonterväg:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Genom att lägga till monteringspunkter på klientsidan kan `/mnt/destination[1-3]` du punga bort ytterligare kopieringskommandon till de extra monteringspunkterna, vilket ger ytterligare parallellitet.

Om filerna till exempel är mycket stora kan du definiera kopieringskommandona för att använda olika målsökvägar och skicka ut fler kommandon parallellt från klienten som utför kopian.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

I exemplet ovan är alla tre målmonteringspunkterna inriktade på klientfilskopieringsprocesserna.

### <a name="when-to-add-clients"></a>När du ska lägga till klienter

Slutligen, när du har nått klientens funktioner, lägga till fler kopiera trådar eller ytterligare monteringspunkter kommer inte att ge några ytterligare filer / sek eller byte / sek ökar. I det fallet kan du distribuera en annan klient med samma uppsättning monteringspunkter som kör sina egna uppsättningar av filkopieringsprocesser.

Exempel:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

### <a name="create-file-manifests"></a>Skapa filmanifest

När du har förstått metoderna ovan (flera kopieringstrådar per mål, flera mål per klient, flera klienter per nätverkstillgängligt källfilsystem), bör du tänka på den här rekommendationen: Skapa filmanifest och använd dem sedan med kopia kommandon över flera klienter.

I det här ``find`` scenariot används KOMMANDOT UNIX för att skapa manifest av filer eller kataloger:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Omdirigera det här resultatet till en fil:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Sedan kan du iterera genom manifestet, använda BASH-kommandon för att räkna filer och bestämma storleken på underkatalogerna:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Slutligen måste du skapa de faktiska filkopieringskommandona till klienterna.

Om du har fyra klienter använder du det här kommandot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Om du har fem klienter, använd ungefär så här:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Och för sex...så är det bara att ta sig till Extrapolera efter behov.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Du kommer att få *N* resulterande filer, en för var och en av dina *N-klienter* `find` som har sökvägen namn till nivå fyra kataloger som erhållits som en del av utdata från kommandot.

Använd varje fil för att skapa kopieringskommandot:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Ovanstående ger dig *N* N-filer, var och en med en kopia kommando per rad, som kan köras som en BASH skript på klienten.

Målet är att köra flera trådar av dessa skript samtidigt per klient parallellt på flera klienter.

## <a name="use-a-two-phase-rsync-process"></a>Använda en tvåfasrsynkronprocess

Standardverktyget ``rsync`` fungerar inte bra för att fylla molnlagring via Avere vFXT för Azure-systemet eftersom det genererar ett stort antal filskapande och byta namn på åtgärder för att garantera dataintegritet. Du kan dock på ``--inplace`` ett ``rsync`` säkert sätt använda alternativet med att hoppa över den mer noggranna kopieringsproceduren om du följer det med en andra körning som kontrollerar filens integritet.

En ``rsync`` standardkopiering skapar en temporär fil och fyller den med data. Om dataöverföringen har slutförts har den temporära filen bytt namn till det ursprungliga filnamnet. Den här metoden garanterar konsekvens även om filerna används under kopieringen. Men den här metoden genererar fler skrivåtgärder, vilket saktar filrörelser genom cachen.

Alternativet ``--inplace`` skriver den nya filen direkt på sin slutliga plats. Filer är inte garanterade att vara konsekvent under överföringen, men det är inte viktigt om du priming ett lagringssystem för användning senare.

Den ``rsync`` andra åtgärden fungerar som en konsekvenskontroll av den första åtgärden. Eftersom filerna redan har kopierats är den andra fasen en snabb genomsökning för att säkerställa att filerna på målet matchar filerna på källan. Om några filer inte matchar, är de recopied.

Du kan utfärda båda faserna tillsammans i ett kommando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Den här metoden är en enkel och tidsförd effektiv metod för datauppsättningar upp till antalet filer som den interna kataloghanteraren kan hantera. (Det här är vanligtvis 200 miljoner filer för ett kluster med 3 nod, 500 miljoner filer för ett kluster med sex noder och så vidare.)

## <a name="use-the-msrsync-utility"></a>Använda verktyget msrsync

Verktyget ``msrsync`` kan också användas för att flytta data till en back-end core filer för Avere-klustret. Det här verktyget är utformat för ``rsync`` att optimera bandbreddsanvändningen genom att köra flera parallella processer. Den är tillgänglig från <https://github.com/jbd/msrsync>GitHub på .

``msrsync``delar upp källkatalogen i separata "buckets" och kör sedan enskilda ``rsync`` processer på varje bucket.

Preliminära tester med en virtuell dator med fyra kärnor visade bästa effektivitet när du använder 64 processer. Använd ``msrsync`` alternativet ``-p`` för att ställa in antalet processer till 64.

Du kan också ``--inplace`` använda ``msrsync`` argumentet med kommandon. Om du använder det här alternativet kan du överväga att köra ett andra kommando (som med [rsync](#use-a-two-phase-rsync-process), som beskrivs ovan) för att säkerställa dataintegritet.

``msrsync``kan bara skriva till och från lokala volymer. Källan och målet måste vara tillgängliga som lokala fästen i klustrets virtuella nätverk.

Så ``msrsync`` här använder du för att fylla i en Azure-molnvolym med ett Avere-kluster:

1. Installera ``msrsync`` och dess förutsättningar (rsync och Python 2.6 eller senare)
1. Bestäm det totala antalet filer och kataloger som ska kopieras.

   Använd till exempel ``prime.py`` avere-verktyget ```prime.py --directory /path/to/some/directory``` med argument (tillgängligt genom att hämta url). <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>

   Om du ``prime.py``inte använder kan du beräkna antalet ``find`` artiklar med GNU-verktyget enligt följande:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet artiklar med 64 för att bestämma antalet artiklar per process. Använd det här ``-f`` numret med alternativet att ange storleken på buckets när du kör kommandot.

1. Utfärda ``msrsync`` kommandot för att kopiera filer:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Om ``--inplace``du använder lägger du till en andra körning utan alternativet för att kontrollera att data kopieras korrekt:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformat för att flytta 11 000 filer i 64 processer från /test/source-repository till /mnt/vfxt/repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Använda parallellkopieringsskriptet

Skriptet ``parallelcp`` kan också vara användbart för att flytta data till vFXT-klustrets backend-lagring.

Skriptet nedan kommer att `parallelcp`lägga till den körbara . (Det här skriptet är utformat för Ubuntu, ``parallel`` om du använder en annan distribution måste du installera separat.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Exempel på parallellkopia

I det här exemplet används ``glibc`` det parallella kopieringsskriptet för kompilering med hjälp av källfiler från Avere-klustret.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Källfilerna lagras på Avere-klustrets monteringspunkt och objektfilerna lagras på den lokala hårddisken.

Det här skriptet använder parallellkopieringsskript ovan. Alternativet ``-j`` används med ``parallelcp`` ``make`` och för att få parallellisering.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
