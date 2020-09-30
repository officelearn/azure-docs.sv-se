---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535618"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-09282020"></a>Utgivnings datum: 09/28/2020

Den här versionen gäller både HDInsight 3,6 och HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Autoskalning för interaktiv fråga med HDInsight 4,0 är nu allmänt tillgänglig
Den automatiska skalningen för kluster typen interaktiv fråga är nu allmänt tillgänglig (GA) för HDInsight 4,0. Alla interaktiva frågor 4,0-kluster som skapats efter den 27 augusti 2020 har stöd för automatisk skalning.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>HBase-kluster har stöd för Premium ADLS Gen2
HDInsight stöder nu Premium ADLS Gen2 som primärt lagrings konto för HDInsight HBase 3,6 och 4,0-kluster. Tillsammans med [accelererade skrivningar](./hbase/apache-hbase-accelerated-writes.md)kan du få bättre prestanda för dina HBase-kluster.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Kafka partition distribution på Azures fel domäner
En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. Innan HDInsight-Kafka kan lagra alla partitions repliker i samma feldomän. Från och med den här versionen stöder HDInsight nu automatisk distribution av Kafka-partitioner baserat på Azures fel domäner. 

### <a name="encryption-in-transit"></a>Kryptering under överföring
Kunder kan aktivera kryptering under överföring mellan klusternoder med IPSec-kryptering med plattforms hanterade nycklar. Det här alternativet kan aktive ras när klustret skapas. Se mer information om [hur du aktiverar kryptering under överföring](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Kryptering på värden
När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. I den här versionen kan du **Aktivera kryptering på värden på Temp-datadisken** när du skapar klustret. Kryptering på värden stöds bara på [vissa VM SKU: er i begränsade regioner](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). HDInsight stöder [följande konfiguration och SKU: er för noden](./hdinsight-supported-node-configuration.md). Se mer information om [hur du aktiverar kryptering på värden](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Från och med den här versionen migrerar tjänsten gradvis till [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Hela processen kan ta månader. När dina regioner och prenumerationer migreras, kommer nyligen skapade HDInsight-kluster att köras på virtuella datorers skalnings uppsättningar utan kund åtgärder. Ingen avbrytande ändring förväntas.

## <a name="deprecation"></a>Utfasning
Ingen utfasning för den här versionen.

## <a name="behavior-changes"></a>Beteende ändringar
Ingen beteende ändring för den här versionen.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Möjlighet att välja olika Zookeeper SKU: er för Spark-, Hadoop-och ML-tjänster
HDInsight idag stöder inte ändring av Zookeeper SKU för kluster typerna Spark, Hadoop och ML. Den använder A2_v2/a2 SKU för Zookeeper-noder och kunder debiteras inte för dem. I den kommande versionen kan kunderna ändra Zookeeper SKU för Spark-, Hadoop-och ML-tjänster vid behov. Zookeeper-noder med andra SKU än A2_v2/a2 kommer att debiteras. Standard-SKU: n är fortfarande A2_V2/a2 och kostnads fritt.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).