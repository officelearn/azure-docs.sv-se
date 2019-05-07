---
title: Azure HDInsight Accelerated skrivningar för Apache HBase
description: Ger en översikt över funktionen Azure HDInsight Accelerated skriver som använder premium-hanterade diskar för att förbättra prestanda för Apache HBase skriva vidare loggen.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: f9314355d3dce8d96b794cdb81ab9c3d77faab45
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159307"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight Accelerated skrivningar för Apache HBase

Den här artikeln innehåller bakgrund om den **Accelererat skriver** funktionen för Apache HBase i Azure HDInsight och hur den kan användas effektivt att förbättra skrivprestanda. **Accelerated skrivningar** använder [Azure premium SSD hanterade diskar](../../virtual-machines/linux/disks-types.md#premium-ssd) att förbättra prestanda för den Apache HBase skriva vidare Log (WAL). Läs mer om Apache HBase i [vad är Apache HBase i HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Översikt över HBase-arkitektur

I HBase, en **rad** består av en eller flera **kolumner** och identifieras med en **radnyckel**. Flera rader som utgör en **tabell**. Kolumnerna innehåller **celler**, som är tidsstämplad versioner av värdet i kolumnen. Kolumner som är grupperade i **kolumnserier**, och alla kolumner i en kolumnserie lagras tillsammans i storage-filer som heter **HFiles**.

**Regioner** i HBase används för att utjämna belastningen för databearbetning. HBase lagrar första raderna i en tabell i en enda region. Rader är fördelade över flera regioner som mängden data i tabellen ökar. **Regionservrar** kan hantera begäranden för flera regioner.

## <a name="write-ahead-log-for-apache-hbase"></a>Skriva Ahead Log för Apache HBase

HBase först skriver data uppdateras till en typ av genomförandelogg kallas en skriva vidare Log (WAL). När uppdateringen har lagrats i WAL, de skrivs till minnesinterna **Memstores**. När data i minnet når sin maximala kapacitet, de skrivs till disk som en **HFile**.

Om en **RegionServer** kraschar eller blir otillgänglig innan Memstores skickas skriva vidare loggen kan användas för att spela upp uppdateringar. Utan WAL, om en **RegionServer** kraschar innan uppdateringar till en **HFile**, uppdateringarna försvinner.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Snabbare skrivningar funktion i Azure HDInsight för Apache HBase

Funktionen Accelerated skriver löser problemet med högre-skrivfördröjning på grund av att använda skriva vidare loggar som finns i molnet.  Funktionen Accelerated skriver för HDInsight Apache HBase-kluster, bifogar premium SSD-hanterade diskar till varje RegionServer (arbetsnod). Skriva vidare loggarna skrivs sedan till Hadoop File System (HDFS) monteras på premium managed-diskarna i stället för molnlagring.  Hanterade Premium-diskar använder Solid-State-diskar (SSD) och erbjuder utmärkta i/o-prestanda med feltolerans.  Till skillnad från ohanterade diskar om en lagringsenhet kraschar påverkas inte andra lagringsenheter i samma tillgänglighetsuppsättning.  Därför kan ange hanterade diskar med låg skrivfördröjningen och bättre flexibilitet för dina program. Läs mer om Azure-hanterade diskar i [introduktion till Azure hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Så här aktiverar du Accelerated skriver för HBase i HDInsight

Om du vill skapa en ny HBase-kluster med funktionen Accelerated skriver, följer du stegen i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) tills du når **steg3, lagring**. Under **Metaarkivsinställningar**, klickar du på kryssrutan bredvid **aktivera Accelerated skriver (förhandsversion)**. Fortsätt sedan med de återstående stegen för att skapa kluster.

![Aktivera snabbare skrivningar alternativet för Apache HBase i HDInsight](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

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

* Officiella dokumentationen för Apache HBase på den [skriva vidare Log-funktionen](https://hbase.apache.org/book.html#wal)
* Om du vill uppgradera ditt HDInsight Apache HBase-kluster om du vill använda Accelererat skriver [Migrera ett Apache HBase-kluster till en ny version](apache-hbase-migrate-new-version.md).
