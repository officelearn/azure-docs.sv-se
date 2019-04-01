---
title: Azure HDInsight förbättrad skriver för Apache HBase (förhandsversion)
description: Ger en översikt över funktionen Azure HDInsight förbättrad skriver som använder premium-hanterade diskar för att förbättra prestanda för Apache HBase skriva vidare loggen.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: 37e6f316a5202429396ddd2a31cb14fe61341e89
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759606"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight förbättrad skriver för Apache HBase (förhandsversion)

Den här artikeln innehåller bakgrund om den **förbättrad skriver** funktionen för Apache HBase i Azure HDInsight och hur den kan användas effektivt att förbättra skrivprestanda. **Förbättrad skrivningar** använder [Azure premium SSD hanterade diskar](../../virtual-machines/linux/disks-types.md#premium-ssd) att förbättra prestanda för den Apache HBase skriva vidare Log (WAL). Läs mer om Apache HBase i [vad är Apache HBase i HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Översikt över HBase-arkitektur

I HBase, en **rad** består av en eller flera **kolumner** och identifieras med en **radnyckel**. Flera rader som utgör en **tabell**. Kolumnerna innehåller **celler**, som är tidsstämplad versioner av värdet i kolumnen. Kolumner som är grupperade i **kolumnserier**, och alla kolumner i en kolumnserie lagras tillsammans i storage-filer som heter **HFiles**.

**Regioner** i HBase används för att utjämna belastningen för databearbetning. HBase först lagrar raderna i en tabell i en enda region och sedan sprids raderna i flera regioner som mängden data i tabellen ökar. **Regionservrar** kan hantera begäranden för flera regioner.

## <a name="write-ahead-log-for-apache-hbase"></a>Skriva Ahead Log för Apache HBase

Uppdateringar i HBase skrivs först till en typ av genomförandelogg kallas en skriva vidare Log (WAL). När uppdateringen har lagrats i WAL, de skrivs till minnesinterna **Memstores**. När data i minnet når sin maximala kapacitet, de skrivs till disk som en **HFile**.

Om en **RegionServer** kraschar eller blir otillgänglig innan Memstores skickas skriva vidare loggen kan användas för att spela upp uppdateringar. Utan WAL, om en **RegionServer** kraschat innan uppdateringar till en **HFile**, alla förlorad.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Förbättrad skrivningar funktion i Azure HDInsight för Apache HBase

Funktionen Förbättrad skriver löser problemet med högre-skrivfördröjning på grund av att använda skriva vidare loggar som finns i molnet.  Funktionen Förbättrad skriver HDInsight Apache HBase-kluster, bifogar premium SSD hanterade diskar till varje RegionServer (arbetsnod). Skriva vidare loggarna skrivs sedan till Hadoop File System (HDFS) monteras på dessa premium-hanterade diskar i stället för molnlagring.  Premium managed disks använder Solid-State-diskar (SSD) och erbjuder utmärkta i/o-prestanda med feltolerans.  Till skillnad från ohanterade diskar om en lagringsenhet kraschar påverkas inte andra lagringsenheter i samma tillgänglighetsuppsättning.  Därför kan ange hanterade diskar med låg skrivfördröjningen och bättre flexibilitet för dina program. Mer information om Azure-hanterade diskar, se [introduktion till Azure hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>Hur du aktiverar förbättrad skriver för HBase i HDInsight

Om du vill skapa en ny HBase-kluster med funktionen Förbättrad skriver, följer du stegen i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) tills du når **steg3, lagring**. Under **Metaarkivsinställningar**, klickar du på kryssrutan bredvid **aktivera förbättrad skriver (förhandsversion)**. Fortsätt sedan med de återstående stegen för att skapa kluster.

![Aktivera förbättrad skrivningar alternativet för Apache HBase i HDInsight](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>Annat att tänka på

Skapa ett kluster med minst tre arbetsnoder för att bevara tålighet. När du skapat kan du inte skala ned klustret till mindre än tre arbetsnoder. 

Tömma eller inaktivera HBase-tabellerna innan du tar bort klustret, så att du inte förlorar skriva vidare loggdata.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Nästa steg

* Officiella dokumentationen för Apache HBase på den [skriva vidare Log funktionen](https://hbase.apache.org/book.html#wal).