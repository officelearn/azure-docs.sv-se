---
title: Flytta data till Avere vFXT för Azure
description: Hur du lägger till data till en ny lagringsvolymen för användning med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a3d6cb745c782d2a7166208f2a8dd1202a330b15
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050497"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Flytta data till vFXT-kluster – parallella mata in 

När du har skapat ett nytt kluster vFXT, kanske din första uppgift att flytta data till dess nya lagringsvolym. Men om din vanliga metod för att flytta data utfärdar ett kommando för enkel kopia från en klient, visas sannolikt en långsam kopieringen bättre prestanda. Single-threaded kopiera är inte ett bra alternativ för att kopiera data till Avere vFXT klustrets serverdelslagring.

Eftersom Avere vFXT klustret är en skalbar flera cache, är det snabbaste och mest effektiva sättet att kopiera data till den med flera klienter. Den här tekniken parallelizes inmatning av filer och objekt.

![Diagram över flera klienten och flertrådiga dataförflyttning: En ikon för lagring för maskinvara på plats har flera pilar som kommer från den längst ned till vänster. Pilarna pekar på fyra klientdatorer. Från varje klientdator tre pilar som pekar mot Avere vFXT. Från Avere-vFXT flera pilar som pekar till Blob storage.](media/avere-vfxt-parallel-ingest.png) 

Den ``cp`` eller ``copy`` kommandon som används ofta för att överföra data från en lagringssystemet till en annan med är single-threaded processer som kopiera endast en fil i taget. Det innebär att filservern är mata in endast en fil i taget – vilket är slöseri med resurser för det klustret.

Den här artikeln förklarar strategier för att skapa en fil för flera klient, flertrådiga kopierar system för att flytta data till Avere vFXT klustret. Den förklarar filen överföring begrepp och beslutspunkter som kan användas för effektiv Datakopieringen med flera klienter och enkel copy-kommandon.

Den förklarar också vissa verktyg som kan hjälpa. Den ``msrsync`` verktyg kan användas för att automatisera processen med att dela upp en datauppsättning i buckets och kommandona rsync delvis. Den ``parallelcp`` skriptet är ett annat verktyg som läser källkatalogen och problem kopiera kommandon automatiskt.  

Klicka på länken för att hoppa till ett avsnitt:

* [Manuell kopiering exempel](#manual-copy-example) – en omfattande förklaring med copy-kommandon
* [Delvis automatiserad (msrsync)-exempel](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Parallell kopiera](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Mall för data-lösning

En Resource Manager-mallen finns på GitHub för att automatiskt skapa en virtuell dator med de parallella verktyg för datainhämtning nämns i den här artikeln. 

![diagram som visar flera pilar varje från blob storage, maskinvara storage och Azure file-källor. Pilarna för att kunna en ”data lösning vm” och därifrån kan flera pilar som pekar på Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Data-lösning VM är en del av en självstudiekurs där den nya virtuella datorn monterar Avere vFXT klustret och hämtar dess bootstrap skript från klustret. Läs [Bootstrap data-lösning VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) mer information.

## <a name="strategic-planning"></a>Strategisk planering

När du skapar en strategi för att kopiera data parallellt, bör du förstå kompromisser i filstorleken och antalet filer directory djup.

* När filerna är små, är mått-filer per sekund.
* När filerna är stora (10MiBi eller senare), mått av intresse är byte per sekund.

Varje kopieringsprocessen har en dataflödeshastighet och ett överförda filer pris, vilket kan mätas utifrån tidsgränsen längden på kopieringskommandot och ta hänsyn till filstorleken och antalet filer. Förklarar hur du mäter hastighet som inte omfattas av det här dokumentet, men det är viktigt att förstå om du kommer ta itu med små eller stora filer.

## <a name="manual-copy-example"></a>Manuella kopierings-exempel 

Du kan manuellt skapa en flertrådiga kopia på en klient genom att köra flera kopiera kommandon på en gång i bakgrunden mot fördefinierade uppsättningar av filer och mappar.

Linux/UNIX ``cp`` kommandot innefattar argumentet ``-p`` att bevara ägarskap och mtime metadata. Det är valfritt att lägga till det här argumentet i kommandona nedan. (Om du lägger till argumentet ökar antalet filsystem-anrop som skickats från klienten till mål-filsystem för ändring av metadata.)

Det här enkla exemplet kopierar två filer parallellt:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Efter det här kommandot den `jobs` kommandot visar att två trådar körs.

### <a name="predictable-filename-structure"></a>Förutsägbar filename struktur 

Om din filnamn är förutsägbara, kan du använda uttryck för att skapa parallella kopia trådar. 

Exempel: om din katalog innehåller 1000-filer som är numrerade från `0001` till `1000`, du kan använda följande uttryck för att skapa tio parallella trådar som var och en kopiera 100 filer:

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

### <a name="unknown-filename-structure"></a>Okänd filename struktur

Om din filnamn struktur inte är förutsägbar kan du gruppera filer efter katalognamn. 

Det här exemplet samlar in hela kataloger att skicka till ``cp`` kommandon körs som bakgrundsaktiviteter:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

När filerna har samlats in, kan du köra parallella kopiera kommandon för att rekursivt kopiera underkatalogerna och deras innehåll:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>När du ska lägga till monteringspunkter

När du har tillräckligt många parallella trådar går mot en enda mål filsystem monteringspunkt blir det en punkt där att lägga till fler trådar inte ger större dataflöde. (Dataflöde ska mätas i filer per sekund eller byte per sekund, beroende på vilken typ av data.) Eller ännu värre över threading kan ibland orsaka försämrade dataflöde.  

Då kan du kan lägga till klientsidan monteringspunkter andra vFXT klustrets IP-adresser, med hjälp av samma remote filsystem monteringssökväg:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Att lägga till klientsidan montera pekar kan du Förgrena av ytterligare copy-kommandon till de ytterligare `/mnt/destination[1-3]` monteringspunkter, uppnå ytterligare parallellitet.  

Till exempel om dina filer är mycket stora, kan du definiera copy-kommandon för att använda olika mål sökvägar, skicka ut fler kommandon parallellt från klienten utföra kopieringen.

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

I exemplet ovan som monteringspunkter för alla tre mål omfattas av kopieringsstegen för klient-fil.

### <a name="when-to-add-clients"></a>När du ska lägga till klienter

Slutligen, när du har nått klientens funktioner att lägga till fler kopia trådar eller ytterligare monteringspunkter ger inte några eventuella ytterligare filer/sek eller byte/sek ökar. I så fall kan distribuera du en annan klient med samma uppsättning monteringspunkter som ska köra egna uppsättningar av filen kopieringsstegen. 

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

### <a name="create-file-manifests"></a>Skapa filen manifest

När du har förståelse Överväg metoderna ovan (flera kopia-trådar per mål, flera mål per klient, flera klienter per nätverkstillgänglig källa filsystem), den här rekommendationen: Skapa filen manifest och använda dem med copy-kommandon till flera klienter.

Det här scenariot använder UNIX ``find`` kommando för att skapa manifest av filer och kataloger:

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

Omdirigera resultatet till en fil: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Sedan kan du gå igenom manifestet, med BASH-kommandon för att räkna antalet filer och fastställa storleken på underkatalogerna:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
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

Slutligen måste du skapa de faktiska filkopieringskommandon till klienterna.  

Om du har fyra klienter kan använda det här kommandot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Om du har fem klienter kan använda ungefär så här:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Och för sex... Extrapolera efter behov.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Du får *N* resulterande filer, en för var och en av dina *N* klienter med sökvägar till nivå fyra-kataloger som erhålls som en del av utdata från den `find` kommando. 

Du kan använda varje fil för att skapa kopia-kommando:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Ovanstående ger dig *N* filer, var och en med en kopia kommando per rad, som kan köras som ett BASH-skript på klienten. 

Målet är att köra flera trådar för dessa skript samtidigt per klient parallellt på flera klienter.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>Använda verktyget msrsync för att fylla i molnet volymer

Den ``msrsync`` verktyget också kan användas för att flytta data till en serverdel core filer för Avere klustret. Det här verktyget är utformad för att optimera användningen av bandbredden genom att köra flera parallella ``rsync`` processer. Den är tillgänglig från GitHub vid https://github.com/jbd/msrsync.

``msrsync`` delar upp källkatalogen i separata ”bucketar” och sedan kör enskilda ``rsync`` processer på varje bucket.

Preliminär tester med en VM med fyra kärnor visade mest effektivt när du använder 64 processer. Använd den ``msrsync`` alternativet ``-p`` att ställa in antal processer på 64.

Observera att ``msrsync`` kan bara skriva till och från lokala volymer. Källan och målet måste vara tillgänglig som lokal monterar i klustrets virtuella nätverk.

Om du vill fylla en Azure-molnet-volym med ett Avere-kluster med hjälp av msrsync, följer du dessa instruktioner:

1. Installera msrsync och dess krav (rsync och Python 2.6 eller senare)
1. Fastställa det totala antalet filer och kataloger som ska kopieras.

   Till exempel använda verktyget Avere ``prime.py`` med argument ```prime.py --directory /path/to/some/directory``` (tillgängligt genom att hämta URL: en https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py).

   Om du inte använder ``prime.py``, du kan beräkna hur många objekt med Gnu ``find`` verktyget på följande sätt:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet objekt med 64 att avgöra hur många objekt per process. Använd det här talet med den ``-f`` möjlighet att ange storleken på bucketarna när du kör kommandot.

1. Utfärda kommandot msrsync att kopiera filer:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformats för att flytta 11 000 filer i 64 processer från /test/source-repository till /mnt/vfxt/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Använd skript för parallell kopia

Den ``parallelcp`` skriptet också kan användas för att flytta data till ditt kluster vFXT serverdelslagring. 

Skriptet nedan lägger till den körbara filen `parallelcp`. (Det här skriptet är utformat för Ubuntu; om du använder en annan distribution, måste du installera ``parallel`` separat.)

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

### <a name="parallel-copy-example"></a>Parallell kopiera

Det här exemplet används parallella kopiera skriptet för att kompilera ``glibc`` använda källfiler från Avere-klustret. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Källfiler lagras på monteringspunkten Avere kluster och Objektfiler lagras på den lokala hårddisken.

Det här skriptet använder parallella kopiera skriptet ovan. Alternativet ``-j`` används med ``parallelcp`` och ``make`` att få parallellisering.

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

