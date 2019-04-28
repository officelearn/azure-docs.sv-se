---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679831"
---
*Värmer upp cachen*  
Disken med cachelagring av ReadOnly-värden kan ge högre IOPS än gränsen på disk. Om du vill ha den här högsta läsprestanda från värd-cachen, måste först du värmt upp cachen för den här disken. Detta säkerställer att läsa IOs att benchmarking verktyget kommer att öka på CacheReads volym faktiskt når cachen och inte disken direkt. Cacheträffar resulterar i ytterligare IOPS från enkel cachen aktiverad disk.

> [!IMPORTANT]
> Du måste värmt upp cachen innan du kör prestandamätningar, varje gång virtuella datorn startas om.

## <a name="tools"></a>Verktyg

### <a name="iometer"></a>Iometer

[Hämta verktyget Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) på den virtuella datorn.

#### <a name="test-file"></a>Testa fil

Iometer används en testfil som lagras på volymen där du kör testet benchmarking. Den styr läsningar och skrivningar på den här Testfilen för att mäta disken IOPS och dataflöden. Iometer skapar den här Testfilen om du inte har angett något. Skapa en testfil i 200 GB med namnet iobw.tst på CacheReads och NoCacheWrites volymer.

#### <a name="access-specifications"></a>Specifikationer för åtkomst

Specifikationerna för begäran i/o-storlek, % Läs/Skriv, % slumpmässiga/sekventiella konfigureras på fliken ”åtkomst specifikationer” i Iometer. Skapa en access-specifikation för var och en av de scenarier som beskrivs nedan. Skapa åtkomst-specifikationer och ”spara” med ett lämpligt namn som – RandomWrites\_8K, RandomReads\_8 kB. Välj den motsvarande specifikationen när du kör Testscenario.

Ett exempel på åtkomst specifikationer för maximal skriva IOPS scenariot visas nedan,  
    ![Exempel på åtkomst specifikationerna för maximal skrivåtgärder (IOPS)](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Högsta IOPS testa specifikationer

Använd mindre begärandestorlek på för att demonstrera högsta IOPs. Använd 8K begärandestorlek och skapa specifikationerna för slumpmässiga skrivningar och läsningar.

| Åtkomst-specifikation | Begärandestorlek | Slumpmässig % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 KB |100 |0 |
| RandomReads\_8K |8 KB |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maximalt dataflöde test specifikationer

Använd större begärandestorlek för att demonstrera maximalt dataflöde. Använd 64 kB begärandestorlek och skapa specifikationerna för slumpmässiga skrivningar och läsningar.

| Åtkomst-specifikation | Begärandestorlek | Slumpmässig % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 KB |100 |0 |
| RandomReads\_64K |64 KB |100 |100 |

#### <a name="run-the-iometer-test"></a>Kör testet Iometer

Utför nedanstående steg värmt upp cache

1. Skapa två åtkomst specifikationer med värden som visas nedan,

   | Namn | Begärandestorlek | Slumpmässig % | Läs % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Köra Iometer test för att initiera disk i cachen med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange ”körningstiden” varaktigheten för testet till 2 timmar på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Initiera Disk i cachen |CacheReads |RandomWrites\_1 MB |2 timmar |
1. Kör testet Iometer för uppvärmning disk i cachen med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange ”körningstiden” varaktigheten för testet till 2 timmar på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Varma upp Cachedisk |CacheReads |RandomReads\_1 MB |2 timmar |

När cachedisk värmas upp, kan du fortsätta med test-scenarier som anges nedan. Använd minst tre trådar för för att köra testet Iometer, **varje** rikta volym. För varje arbetstråd välja målvolymen, ange ködjup och välj en av de sparade test-specifikationerna som visas i tabellen nedan, för att köra motsvarande test-scenariot. Förväntat resultat visas också för IOPS och dataflöde när du kör dessa tester. En små i/o-storlek på 8 KB och en hög ködjup på 128 används för alla scenarier.

| Testscenario | Målvolymen | Namn | Resultat |
| --- | --- | --- | --- |
| Max. Lästa IOPS |CacheReads |RandomWrites\_8K |50,000 IOPS |
| Max. Skriva IOPS |NoCacheWrites |RandomReads\_8K |64,000 IOPS |
| Max. Kombinerade IOPS |CacheReads |RandomWrites\_8K |100,000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Max. Lästa MB/s |CacheReads |RandomWrites\_64K |524 MB/sek |
| Max. Skriva MB/sek |NoCacheWrites |RandomReads\_64K |524 MB/sek |
| Kombinerade MB/sek |CacheReads |RandomWrites\_64K |1 000 MB per sekund |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Nedan visas skärmdumpar av Iometer testresultat för kombinerade scenarier för IOPS och dataflöden.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombineras läser och skriver högsta IOPS

![Kombinerade läsningar och skrivningar högsta IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombineras läser och skriver maximalt dataflöde

![Kombinerade läsningar och skrivningar maximalt dataflöde](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO är ett populärt verktyg till benchmark lagring på virtuella Linux-datorer. Det har flexibiliteten att välja olika i/o-storlek, sekventiella eller icke-sekventiell läsning och skriver. Den tio trådar eller processer för att utföra de angivna i/o-åtgärderna. Du kan ange vilken typ av i/o-åtgärder som varje arbetstråd måste utföra med hjälp av jobbfiler. Vi har skapat en jobbet fil per scenariot som illustreras i exemplen nedan. Du kan ändra specifikationerna i jobbfilerna för att jämföra olika arbetsbelastningar som körs på Premium-lagring. I exemplen är vi använder en Standard DS 14 virtuella datorer som kör **Ubuntu**. Använd samma inställningar som beskrivs i början av avsnittet Benchmarking och varma hela cachen innan du kör benchmark-tester.

Innan du börjar [hämta FIO](https://github.com/axboe/fio) och installera den på den virtuella datorn.

Kör följande kommando för Ubuntu

```
apt-get install fio
```

Vi använder fyra trådar för att driva skrivåtgärder och fyra arbetstrådar för utveckla läsåtgärder på diskarna. Write-arbetare som styr trafiken på volymen ”nocache”, som har 10 diskar med cache som ställts in på ”Ingen”. Läs-arbetare som styr trafiken på volymen ”readcache”, som har en disk med cache inställd på ”skrivskyddad”.

#### <a name="maximum-write-iops"></a>Maximal skrivåtgärder (IOPS)

Skapa jobb-fil med följande specifikationer att få maximal skriva IOPS. Ge den namnet ”fiowrite.ini”.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS  

* En hög ködjup på 256.  
* En liten blockstorlek på 8 kB.  
* Flera trådar som utför slumpmässiga skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder  

```
sudo fio --runtime 30 fiowrite.ini
```

När testet körs kan kan du se antalet skriva IOPS den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM leverera dess högsta skriva IOPS-gränsen på 50 000 IOPS.  
    ![Antal skrivbegäranden levererar IOPS VM- och Premium-diskar](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Högsta Läs IOPS

Skapa jobb-fil med följande specifikationer att få högsta Läs IOPS. Ge den namnet ”fioread.ini”.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS

* En hög ködjup på 256.  
* En liten blockstorlek på 8 kB.  
* Flera trådar som utför slumpmässiga skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder

```
sudo fio --runtime 30 fioread.ini
```

När testet körs kan kan du se antalet lästa IOPS den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM leverera fler än 64 000 IOPS för läsning. Det här är en kombination av disken och cache-prestanda.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximalt läsa och skriva IOPS

Skapa jobbfilen med följande specifikationer att få maximal kombineras läsa och skriva IOPS. Ge den namnet ”fioreadwrite.ini”.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS

* En hög ködjup på 128.  
* En liten storlek på 4 KB.  
* Flera trådar som utför slumpmässiga läsningar och skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder

```
sudo fio --runtime 30 fioreadwrite.ini
```

När testet körs, kan du se hur många kombinerade läsa och skriva IOPS den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM att leverera fler än 100 000 kombinerade läsa och skriva IOPS. Det här är en kombination av disken och cache-prestanda.  
    ![Kombinerade Läs- och skrivåtgärder (IOPS)](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximalt kombinerade dataflöde

För att få maximalt kombinerade läsa och skriva dataflöde, använda en större blockstorlek och stora ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en blockstorlek på 64 KB och ködjup på 128.
