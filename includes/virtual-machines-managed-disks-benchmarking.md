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
ms.openlocfilehash: 358e92d8e43473c168e24be9f4af504e6ffcc37a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026102"
---
*Uppvärmning av cacheminnet*  
Disken med ReadOnly-värd-cachelagring kan ge högre IOPS än disk gränsen. För att få den här maximala Läs prestandan från värd-cachen måste du först värma upp cacheminnet för den här disken. Detta säkerställer att den Läs IOs som benchmarking-verktyget kommer att köra på CacheReads volym, träffar i själva cacheminnet och inte på disken direkt. Cacheträffar resulterar i ytterligare IOPS från den enkla cache-aktiverade disken.

> [!IMPORTANT]
> Du måste värma upp cachen innan du kör benchmarking, varje gång en virtuell dator startas om.

## <a name="iometer"></a>Iometer

[Hämta verktyget IOMeter](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) på den virtuella datorn.

### <a name="test-file"></a>Testa fil

IOMeter använder en test fil som är lagrad på den volym där du kör benchmarking-testet. IT-enheter läser och skriver på den här test filen för att mäta disken IOPS och data flödet. IOMeter skapar den här test filen om du inte har angett någon. Skapa en test fil på 200 GB med namnet iobw. TST på CacheReads-och NoCacheWrites-volymerna.

### <a name="access-specifications"></a>Åtkomst specifikation

Specifikationer, begär IO-storlek,% Läs/skriv,% slumpmässig/sekventiella konfigureras med hjälp av fliken "åtkomst specifikationer" i IOMeter. Skapa en åtkomst specifikation för var och en av de scenarier som beskrivs nedan. Skapa åtkomst specifikationen och spara med ett lämpligt namn som – RandomWrites \_ 8K, RandomReads \_ 8K. Välj motsvarande specifikation när du kör test scenariot.

Ett exempel på åtkomst till specifikationer för maximalt Skriv-IOPS-scenario visas nedan.  
    ![Exempel på åtkomst specifikation för maximalt Skriv-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Högsta antal IOPS-testspecifikationer

Använd mindre storlek för begäran för att demonstrera högsta IOPs. Använd 8K begär storlek och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Åtkomst specifikation | Begär ande storlek | Slumpmässiga | Läs |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8K |100 |0 |
| RandomReads \_ 8K |8K |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Maximala krav för data flödes test

Använd större storlek för begäran för att demonstrera maximalt data flöde. Använd 64 K-storlek för begäran och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Åtkomst specifikation | Begär ande storlek | Slumpmässiga | Läs |
| --- | --- | --- | --- |
| RandomWrites \_ 64 KB |64 KB |100 |0 |
| RandomReads \_ 64 KB |64 KB |100 |100 |

### <a name="run-the-iometer-test"></a>Kör IOMeter-testet

Utför stegen nedan för att värma upp cachen

1. Skapa två åtkomst-specifikationer med värden som visas nedan,

   | Name | Begär ande storlek | Slumpmässiga | Läs |
   | --- | --- | --- | --- |
   | RandomWrites \_ 1 MB |1 MB |100 |0 |
   | RandomReads \_ 1 MB |1 MB |100 |100 |
1. Kör IOMeter-testet för att initiera cache-disken med följande parametrar. Använd tre arbets trådar för mål volymen och ett ködjup på 128. Ange körnings tiden för testet till 2 timmar på fliken test konfiguration.

   | Scenario | Mål volym | Name | Varaktighet |
   | --- | --- | --- | --- |
   | Initiera cache-disk |CacheReads |RandomWrites \_ 1 MB |2 timmar |
1. Kör IOMeter-testet för att värma upp cache-disken med följande parametrar. Använd tre arbets trådar för mål volymen och ett ködjup på 128. Ange körnings tiden för testet till 2 timmar på fliken test konfiguration.

   | Scenario | Mål volym | Name | Varaktighet |
   | --- | --- | --- | --- |
   | Värm upp cache-disk |CacheReads |RandomReads \_ 1 MB |2 timmar |

När du har värmt upp cache-disken fortsätter du med de test scenarier som anges nedan. Om du vill köra IOMeter-testet använder du minst tre arbets trådar för **varje** mål volym. För varje arbets tråd väljer du mål volymen, ställer in ködjup och väljer en av de sparade test specifikationerna, som du ser i tabellen nedan, för att köra motsvarande test scenario. Tabellen visar även förväntade resultat för IOPS och data flöde när du kör de här testerna. I alla scenarier används en liten IO-storlek på 8 KB och ett högt ködjup på 128.

| Test scenario | Mål volym | Name | Resultat |
| --- | --- | --- | --- |
| Max. Läs IOPS |CacheReads |RandomWrites \_ 8K |50 000 IOPS |
| Max. Skriv IOPS |NoCacheWrites |RandomReads \_ 8K |64 000 IOPS |
| Max. Kombinerad IOPS |CacheReads |RandomWrites \_ 8K |100 000 IOPS |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Max. Läsning MB/SEK |CacheReads |RandomWrites \_ 64 KB |524 MB/SEK |
| Max. Skriv MB/SEK |NoCacheWrites |RandomReads \_ 64 KB |524 MB/SEK |
| Kombinerade MB/SEK |CacheReads |RandomWrites \_ 64 KB |1000 MB/SEK |
| NoCacheWrites |RandomReads \_ 64 KB | &nbsp; | &nbsp; |

Nedan visas skärm bilder av IOMeter test resultat för kombinerade IOPS-och data flödes scenarier.

### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinerade läsningar och skrivningar högsta IOPS

![Kombinerade läsningar och skrivningar högsta IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinerade läsningar och skrivningar maximalt data flöde

![Kombinerade läsningar och skrivningar maximalt data flöde](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO är ett populärt verktyg för benchmark-lagring på virtuella Linux-datorer. Det har flexibiliteten att välja olika IO-storlekar, sekventiella eller slumpmässiga läsningar och skrivningar. Den skapar arbets trådar eller processer för att utföra angivna I/O-åtgärder. Du kan ange vilken typ av I/O-åtgärder som varje arbets tråd måste utföra med hjälp av projektfiler. Vi har skapat en jobb fil per scenario som illustreras i exemplen nedan. Du kan ändra specifikationerna i dessa projektfiler till att mäta olika arbets belastningar som körs på Premium Storage. I exemplen använder vi en standard VM 14-baserad virtuell dator som kör **Ubuntu**. Använd samma installations program som beskrivs i början av avsnittet benchmarking och Värm upp cacheminnet innan du kör testerna för benchmarking.

Innan du börjar kan du [Hämta FIO](https://github.com/axboe/fio) och installera det på den virtuella datorn.

Kör följande kommando för Ubuntu,

```
apt-get install fio
```

Vi använder fyra arbets trådar för att köra Skriv åtgärder och fyra arbets trådar för att köra Läs åtgärder på diskarna. Skriv personalen driver trafik på volymen "NoCache", som har 10 diskar med cache satt till "ingen". Läs arbetare driver trafik på "readcache"-volymen, som har en disk med cache satt till "ReadOnly".

### <a name="maximum-write-iops"></a>Maximalt Skriv-IOPS

Skapa jobb filen med följande specifikationer för att få maximal Skriv-IOPS. Ge den namnet "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.  

* Ett högt ködjup på 256.  
* En liten block storlek på 8 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder  

```
sudo fio --runtime 30 fiowrite.ini
```

Medan testet körs kan du se antalet skrivnings-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levereras den virtuella DS14-datorn till den maximala Skriv-IOPS-gränsen på 50 000 IOPS.  
    ![Antalet skriv IOPS VM och Premium-diskar levereras.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>Högsta Läs-IOPS

Skapa jobb filen med följande specifikationer för att få maximal Läs-IOPS. Ge den namnet "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.

* Ett högt ködjup på 256.  
* En liten block storlek på 8 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder

```
sudo fio --runtime 30 fioread.ini
```

Medan testet körs kan du se antalet Läs-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levererar den virtuella DS14-datorn mer än 64 000 Läs-IOPS. Detta är en kombination av disk-och cache-prestanda.  
    ![Skärm bild av antalet skriv IOPS VM och Premium-diskar som levereras.](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Maximal Läs-och skriv-IOPS

Skapa jobb filen med följande specifikationer för att få maximalt kombinerat Läs-och skriv-IOPS. Ge den namnet "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.

* Ett högt ködjup på 128.  
* En liten block storlek på 4 KB.  
* Flera trådar utför slumpmässiga läsningar och skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder

```
sudo fio --runtime 30 fioreadwrite.ini
```

Medan testet körs kan du se antalet kombinerade Läs-och skriv-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levererar den virtuella DS14-datorn mer än 100 000 kombinerat Läs-och skriv-IOPS. Detta är en kombination av disk-och cache-prestanda.  
    ![Kombinerad läsning och skrivning av IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Maximalt kombinerat data flöde

Om du vill hämta maximalt kombinerat Läs-och Skriv data flöde använder du en större block storlek och ett stort ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en block storlek på 64 KB och ködjup på 128.
