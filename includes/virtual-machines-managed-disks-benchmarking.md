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
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673534"
---
*Uppvärmning av cacheminnet*  
Disken med ReadOnly värdcache kan ge högre IOPS än diskgränsen. För att få den här maximala läsprestanda från värdcachen måste du först värma upp cacheminnet på den här disken. Detta säkerställer att Läs IOs att benchmarking verktyget kommer att köra på CacheReads volym, faktiskt träffar cachen, och inte disken direkt. Cacheträffarna resulterar i ytterligare IOPS från den enda cacheaktiverade disken.

> [!IMPORTANT]
> Du måste värma upp cachen innan du kör benchmarking, varje gång vm startas om.

## <a name="tools"></a>Verktyg

### <a name="iometer"></a>Iometer (Iometer)

[Ladda ner Iometerverktyget](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) på den virtuella datorn.

#### <a name="test-file"></a>Testfil

Iometer använder en testfil som lagras på den volym som du kör benchmarkingtestet på. Den driver läser och skriver på den här testfilen för att mäta disken IOPS och Dataflöde. Iometer skapar den här testfilen om du inte har angett en. Skapa en 200 GB-testfil som heter iobw.tst på volymerna CacheReads och NoCacheWrites.

#### <a name="access-specifications"></a>Specifikationer för åtkomst

Specifikationerna, begäran IO-storlek, % läs/skrivning, % slumpmässigt/sekventiellt konfigureras med hjälp av fliken "Åtkomstspecifikationer" i Iometer. Skapa en åtkomstspecifikation för vart och ett av de scenarier som beskrivs nedan. Skapa åtkomstspecifikationer och "Spara" med ett lämpligt\_namn som -\_RandomWrites 8K, RandomReads 8K. Välj motsvarande specifikation när testscenariot körs.

Ett exempel på åtkomstspecifikationer för maximalt Skriv IOPS-scenario visas nedan,  
    ![Exempel på åtkomstspecifikationer för maximal skrivning iOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Högsta IOPS-testspecifikationer

Använd mindre begärandens storlek om du vill visa maximal IOPs. Använd 8K-begärandestorlek och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Specifikation för åtkomst | Begär storlek | Slumpmässigt % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maximala testspecifikationer för dataflöde

Använd större begärandens storlek för att visa maximalt dataflöde. Använd 64 K begäran storlek och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Specifikation för åtkomst | Begär storlek | Slumpmässigt % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Kör Iometer-testet

Utför stegen nedan för att värma upp cache

1. Skapa två åtkomstspecifikationer med värden som visas nedan,

   | Namn | Begär storlek | Slumpmässigt % | Läs % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Kör Iometertestet för att initiera cachedisk med följande parametrar. Använd tre arbetstrådar för målvolymen och ett ködjup på 128. Ställ in testets varaktighet "Körtid" till 2 timmar på fliken "Test setup".

   | Scenario | Målvolym | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Initiera cachedisk |CacheReads |RandomWrites\_1MB |2 timmar |
1. Kör Iometer-testet för uppvärmning av cachedisk med följande parametrar. Använd tre arbetstrådar för målvolymen och ett ködjup på 128. Ställ in testets varaktighet "Körtid" till 2 timmar på fliken "Test setup".

   | Scenario | Målvolym | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Värm upp cachedisk |CacheReads |RandomReads\_1MB |2 timmar |

När cachedisken har värmts upp fortsätter du med testscenarierna nedan. Om du vill köra Iometertestet använder du minst tre arbetstrådar för **varje** målvolym. För varje arbetstråd väljer du målvolymen, anger ködjup och väljer en av de sparade testspecifikationerna, som visas i tabellen nedan, för att köra motsvarande testscenario. Tabellen visar också förväntade resultat för IOPS och Dataflöde när du kör dessa tester. För alla scenarier används en liten IO-storlek på 8 kB och ett högt ködjup på 128.

| Testscenario | Målvolym | Namn | Resultat |
| --- | --- | --- | --- |
| Max. Läs IOPS |CacheReads |RandomWrites\_8K |50 000 IOPS |
| Max. Skriv IOPS |NoCacheSkriver |RandomReads\_8K |64 000 IOPS |
| Max. Kombinerade IOPS |CacheReads |RandomWrites\_8K |100 000 IOPS |
| NoCacheSkriver |RandomReads\_8K | &nbsp; | &nbsp; |
| Max. Läs MB/sek |CacheReads |RandomWrites\_64K |524 MB/sek |
| Max. Skriv MB/sek |NoCacheSkriver |RandomReads\_64K |524 MB/sek |
| Kombinerad MB/sek |CacheReads |RandomWrites\_64K |1000 MB/sek |
| NoCacheSkriver |RandomReads\_64K | &nbsp; | &nbsp; |

Nedan finns skärmdumpar av Iometer testresultat för kombinerade IOPS och Dataflöde scenarier.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinerade läsningar och skriver maximal IOPS

![Kombinerade läsningar och skriver maximal IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinerade läsningar och skriver maximalt dataflöde

![Kombinerade läsningar och skrivningar maximalt dataflöde](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO är ett populärt verktyg för att jämföra lagring på Linux virtuella datorer. Den har flexibiliteten att välja olika IO-storlekar, sekventiella eller slumpmässiga läsningar och skrivningar. Det ger upphov till arbetstrådar eller processer för att utföra de angivna I/O-åtgärderna. Du kan ange vilken typ av I/O-åtgärder varje arbetstråd måste utföra med hjälp av jobbfiler. Vi skapade en jobbfil per scenario som illustreras i exemplen nedan. Du kan ändra specifikationerna i dessa jobbfiler för att jämföra olika arbetsbelastningar som körs på Premium Storage. I exemplen använder vi en standard DS 14 VM som kör **Ubuntu**. Använd samma inställning som beskrivs i början av avsnittet Benchmarking och värm upp cachen innan du kör benchmarkingtesterna.

Innan du börjar [laddar du ned FIO](https://github.com/axboe/fio) och installerar det på din virtuella dator.

Kör följande kommando för Ubuntu,

```
apt-get install fio
```

Vi använder fyra arbetstrådar för körning Avskrivningsåtgärder och fyra arbetstrådar för körning av Läs-åtgärder på diskarna. Skrivarbetarna kör trafik på volymen "nocache", som har 10 diskar med cache inställd på "Ingen". Läsarbetarna kör trafik på "readcache"-volymen, som har en disk med cache inställd på "ReadOnly".

#### <a name="maximum-write-iops"></a>Maximal skrivning IOPS

Skapa jobbfilen med följande specifikationer för att få maximal Skriv IOPS. Namnge det "fiowrite.ini".

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

Observera följande viktiga saker som är i linje med de designriktlinjer som beskrivs i tidigare avsnitt. Dessa specifikationer är nödvändiga för att driva maximal IOPS,  

* Ett högt ködjup på 256.  
* En liten blockstorlek på 8 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder,  

```
sudo fio --runtime 30 fiowrite.ini
```

Medan testet körs kan du se hur många skriv-IOPS vm- och Premium-diskarna levererar. Som visas i exemplet nedan levererar DS14-virtuella datorn sin maximala skriv-IOPS-gräns på 50 000 IOPS.  
    ![Antal IOPS VM- och Premium-diskar levereras](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximalt läst IOPS

Skapa jobbfilen med följande specifikationer för att få maximal Läs IOPS. Nämn det "fioread.ini".

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

Observera följande viktiga saker som är i linje med de designriktlinjer som beskrivs i tidigare avsnitt. Dessa specifikationer är nödvändiga för att driva maximal IOPS,

* Ett högt ködjup på 256.  
* En liten blockstorlek på 8 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder,

```
sudo fio --runtime 30 fioread.ini
```

Medan testet körs kan du se hur många lästa IOPS vm- och Premium-diskarna levererar. Som visas i exemplet nedan levererar DS14-virtuella datorn mer än 64 000 Läs IOPS. Detta är en kombination av disken och cacheprestanda.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximalt läst och skriv IOPS

Skapa jobbfilen med följande specifikationer för att få maximalt kombinerat Läs och skriv IOPS. Namnge det "fioreadwrite.ini".

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

Observera följande viktiga saker som är i linje med de designriktlinjer som beskrivs i tidigare avsnitt. Dessa specifikationer är nödvändiga för att driva maximal IOPS,

* Ett kickködjup av 128.  
* En liten blockstorlek på 4 KB.  
* Flera trådar som utför slumpmässiga läsningar och skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Medan testet körs kan du se hur många kombinerade läsa och skriva IOPS vm- och Premium-diskarna levererar. Som visas i exemplet nedan levererar DS14-virtuella datorn mer än 100 000 kombinerade Read and Write IOPS. Detta är en kombination av disken och cacheprestanda.  
    ![Kombinerad läsa och skriva IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximalt kombinerat dataflöde

Om du vill få maximalt kombinerat läs- och skrivflöde använder du en större blockstorlek och stort ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en blockstorlek på 64 kB och ködjup på 128.
