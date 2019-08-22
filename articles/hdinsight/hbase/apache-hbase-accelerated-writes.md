---
title: Azure HDInsight-accelererade skrivningar för Apache HBase
description: Ger en översikt över funktionen Azure HDInsight-accelererade skrivningar, som använder Premium-hanterade diskar för att förbättra prestanda för filen Apache HBase Write Ahead.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 8b24c7517402aa6f29c95c0cd0f58bb1d51e1082
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876475"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight-accelererade skrivningar för Apache HBase

Den här artikeln innehåller en bakgrund för den **accelererade skrivningar** funktionen för Apache HBase i Azure HDInsight och hur den kan användas effektivt för att förbättra skriv prestanda. **Accelererade skrivningar** använder [Azure Premium SSD-hanterade diskar](../../virtual-machines/linux/disks-types.md#premium-ssd) för att förbättra prestandan hos Apache HBase Write Ahead log (Wal). Mer information om Apache HBase finns i [Vad är Apache HBase i HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Översikt över HBase-arkitekturen

I HBase består en **rad** av en eller flera **kolumner** och identifieras av en **rad nyckel**. Flera rader utgör en **tabell**. Kolumner innehåller **celler**, som är tidsstämplade versioner av värdet i den kolumnen. Kolumner grupperas i **kolumn serier**och alla kolumner i en kolumn serie lagras tillsammans i lagringsfiler som kallas **HFiles**.

**Regioner** i HBase används för att balansera belastningen på data bearbetningen. HBase lagrar först raderna i en tabell i en enda region. Raderna sprids över flera regioner när mängden data i tabellen ökar. **Region servrar** kan hantera begär Anden för flera regioner.

## <a name="write-ahead-log-for-apache-hbase"></a>Skriv loggen i förväg för Apache HBase

HBase skriver först data uppdateringar till en typ av inchecknings logg som kallas för Skriv logg (WAL). När uppdateringen har lagrats i WAL skrivs den till InMemory- **memstores**. När data i minnet når sin maximala kapacitet, skrivs de till disk som en **HFile**.

Om en **RegionServer** kraschar eller blir otillgänglig innan memstores töms, kan Skriv loggen användas för att spela upp uppdateringar igen. Utan WAL, om en **RegionServer** kraschar innan uppdateringar av en **HFile**rensas, går alla dessa uppdateringar förlorade.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Accelererade skrivningar i Azure HDInsight för Apache HBase

Funktionen accelererade skrivningar löser problemet med högre Skriv-fördröjning som orsakas av att skriva loggar som finns i moln lagring.  Funktionen accelererade skrivningar för HDInsight Apache HBase-kluster, ansluter Premium SSD-hanterade diskar till varje RegionServer (arbetsnoden). Skriv loggar i förväg skrivs sedan till Hadoop-filsystemet (HDFS) som är monterat på dessa Premium-hanterade diskar i stället för till moln lagring.  Premium-hanterade – diskar använder Solid-State-diskar (SSD) och erbjuder utmärkta I/O-prestanda med fel tolerans.  Till skillnad från ohanterade diskar, kommer den inte att påverka andra lagrings enheter i samma tillgänglighets uppsättning om en lagrings enhet slutar fungera.  Det innebär att hanterade diskar ger låg Skriv fördröjning och bättre återhämtning för dina program. Mer information om Azure-hanterade diskar finns i [Introduktion till Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Så här aktiverar du påskyndade skrivningar för HBase i HDInsight

Om du vill skapa ett nytt HBase-kluster med funktionen för accelererade skrivningar följer du stegen i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) tills du når **steg 3, lagring**. Under **Inställningar för metaarkiv**klickar du på kryss rutan bredvid **Aktivera accelererade skrivningar (för hands version)** . Fortsätt sedan med de återstående stegen för att skapa kluster.

![Aktivera alternativ för påskyndad skrivning för HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Annat att tänka på

Skapa ett kluster med minst tre arbetsnoder för att bevara data hållbarhet. När du har skapat det går det inte att skala upp klustret till färre än tre arbetsnoder.

Töm eller inaktivera HBase-tabellerna innan du tar bort klustret så att du inte förlorar Skriv logg data.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Nästa steg

* Officiell Apache HBase-dokumentation om [funktionen Skriv logg](https://hbase.apache.org/book.html#wal)
* Om du vill uppgradera ditt HDInsight Apache HBase-kluster för att använda accelererade skrivningar, se [Migrera ett Apache HBase-kluster till en ny version](apache-hbase-migrate-new-version.md).
