---
title: Azure HDInsight-accelererade skrivningar för Apache HBase
description: Ger en översikt över Azure HDInsight Accelerated Writes-funktionen, som använder premiumhanterade diskar för att förbättra prestanda för Apache HBase Write Ahead Log.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764593"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight-accelererade skrivningar för Apache HBase

Den här artikeln innehåller bakgrund om funktionen **Accelererade skrivningar** för Apache HBase i Azure HDInsight och hur den kan användas effektivt för att förbättra skrivprestanda. **Accelererade skrivningar** använder [Azure premium SSD-hanterade diskar](../../virtual-machines/linux/disks-types.md#premium-ssd) för att förbättra prestanda för Apache HBase Write Ahead Log (WAL). Mer information om Apache HBase finns [i Vad är Apache HBase i HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Översikt över HBase-arkitektur

I HBase består en **rad** av en eller flera **kolumner** och identifieras med en **radnyckel**. Flera rader utgör en **tabell**. Kolumner innehåller **celler**, som är tidsstämplade versioner av värdet i den kolumnen. Kolumner grupperas i **kolumnfamiljer**och alla kolumner i en kolumnfamilj lagras tillsammans i lagringsfiler som kallas **HFiles**.

**Regioner** i HBase används för att balansera databehandlingsbelastningen. HBase lagrar först raderna i en tabell i en enda region. Raderna är spridda över flera regioner när mängden data i tabellen ökar. **Regionservrar** kan hantera begäranden för flera regioner.

## <a name="write-ahead-log-for-apache-hbase"></a>Skriv framåt logg för Apache HBase

HBase skriver först datauppdateringar till en typ av commit-logg som kallas write ahead log (WAL). När uppdateringen har lagrats i WAL skrivs den till **MemStore**i minnet . När data i minnet når sin maximala kapacitet skrivs de till disken som en **HFile**.

Om en **RegionServer** kraschar eller blir otillgänglig innan MemStore rensas kan Write Ahead Log användas för att spela upp uppdateringar igen. Utan WAL, om en **RegionServer** kraschar innan tömning uppdateringar till en **HFile**, alla dessa uppdateringar går förlorade.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funktionen Accelererade skrivningar i Azure HDInsight för Apache HBase

Funktionen Accelererade skrivningar löser problemet med högre skriv-latencies som orsakas av att använda Write Ahead Logs som finns i molnlagring.  Funktionen Accelererade skrivningar för HDInsight Apache HBase-kluster, kopplar premium-SSD-hanterade diskar till varje RegionServer (arbetarnod). Skriv framåt loggar skrivs sedan till Hadoop File System (HDFS) monterad på dessa premium hanterade diskar istället för molnlagring.  Premium-hanterade diskar använder SSD -diskar (Solid State Disks) och erbjuder utmärkt I/O-prestanda med feltolerans.  Till skillnad från ohanterat diskar, om en lagringsenhet går ner, kommer det inte att påverka andra lagringsenheter i samma tillgänglighetsuppsättning.  Som ett resultat ger hanterade diskar låg skrivfördröjning och bättre återhämtning för dina program. Mer information om Azure-hanterade diskar finns i [Introduktion till Azure-hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Så här aktiverar du accelererade skrivningar för HBase i HDInsight

Om du vill skapa ett nytt HBase-kluster med funktionen Accelererade skrivningar följer du stegen i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) tills du når **steg 3, Lagring**. Markera kryssrutan **bredvid Aktivera HBase-accelererade skrivningar**under **Metastore-inställningar**. Fortsätt sedan med de återstående stegen för att skapa kluster.

![Aktivera alternativet Snabbare skrivningar för HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Andra överväganden

Om du vill bevara datahållbarheten skapar du ett kluster med minst tre arbetsnoder. När du har skapat det kan du inte skala ned klustret till mindre än tre arbetsnoder.

Rensa eller inaktivera dina HBase-tabeller innan du tar bort klustret, så att du inte förlorar skriv framåt-loggdata.

```
flush 'mytable'
```

```
disable 'mytable'
```

Följ liknande steg när du skalar ned klustret: rensa tabellerna och inaktivera tabellerna för att stoppa inkommande data. Du kan inte skala ned klustret till färre än tre noder.

Efter dessa steg kommer att säkerställa en lyckad nedskalning och undvika möjligheten att en namnnod går in i felsäkert läge på grund av under-replikerade eller temporära filer.

Om din namnnod går in i safemode efter en skala ner, använd hdfs kommandon för att åter replikera under-replikerade block och få hdfs ur felsäkert läge. Med den här återreplikeringen kan du starta om HBase.

## <a name="next-steps"></a>Nästa steg

* Officiell Apache HBase-dokumentation om [funktionen Skriv framåt logg](https://hbase.apache.org/book.html#wal)
* Om du vill uppgradera ditt HDInsight Apache HBase-kluster för att använda Accelererade skrivningar läser [du Migrera ett Apache HBase-kluster till en ny version](apache-hbase-migrate-new-version.md).
