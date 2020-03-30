---
title: Azure HPC-cachedata intas - manuell kopia
description: Så här använder du cp-kommandon för att flytta data till ett Blob-lagringsmål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168477"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC-cachedata intas - manuell kopieringsmetod

Den här artikeln innehåller detaljerade instruktioner för manuell kopiering av data till en Blob-lagringsbehållare för användning med Azure HPC-cache. Den använder parallella åtgärder med flera trådar för att optimera kopieringshastigheten.

Mer information om hur du flyttar data till Blob-lagring för Din Azure HPC-cache läser du [Flytta data till Azure Blob storage](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Exempel på enkel kopia

Du kan manuellt skapa en kopia med flera trådar på en klient genom att köra mer än ett kopieringskommando samtidigt i bakgrunden mot fördefinierade uppsättningar med filer eller sökvägar.

Kommandot Linux/UNIX ``cp`` innehåller ``-p`` argumentet att bevara ägarskap och mtime-metadata. Det är valfritt att lägga till det här argumentet i kommandona nedan. (Om du lägger till argumentet ökar antalet filsystemanrop som skickas från klienten till målfilsystemet för metadataändring.)

I det här enkla exemplet kopieras två filer parallellt:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

När du har utfärdat `jobs` det här kommandot visar kommandot att två trådar körs.

## <a name="copy-data-with-predictable-file-names"></a>Kopiera data med förutsägbara filnamn

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

## <a name="copy-data-with-unstructured-file-names"></a>Kopiera data med ostrukturerade filnamn

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

## <a name="when-to-add-mount-points"></a>När ska monteringspunkter läggas till

När du har tillräckligt med parallella trådar som går mot en enda målfilsystemmonteringspunkt, kommer det att finnas en punkt där lägga till fler trådar inte ger mer dataflöde. (Dataflödet mäts i filer/sekund eller byte/sekund, beroende på vilken typ av data du har.) Eller ännu värre, över-threading kan ibland orsaka en genomströmning nedbrytning.  

När detta inträffar kan du lägga till fästpunkter på klientsidan på andra Azure HPC-cachemonterade adresser med samma sökväg för fjärrfilsystem:

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

## <a name="when-to-add-clients"></a>När du ska lägga till klienter

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

## <a name="create-file-manifests"></a>Skapa filmanifest

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
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
