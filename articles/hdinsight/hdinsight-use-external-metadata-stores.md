---
title: Använda extern metadatalagring - Azure HDInsight
description: Använda extern metadatalagring med HDInsight-kluster.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: a2c992a47e40a4f8764f5950c65bb90f1cd9e066
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045151"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använda extern metadatalagring i Azure HDInsight

Hive-metaarkiv i HDInsight är en viktig del av Hadoop-arkitektur. Ett metaarkiv finns centrala schemadatabasen som kan användas av andra stordata få tillgång till verktyg som Spark, Interactive Query (LLAP), Presto eller Pig. HDInsight använder en Azure SQL Database som Hive-metaarkiv.

![HDInsight Hive-Metadata Store-arkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt som du kan ställa in ett metaarkiv för dina HDInsight-kluster:

* [Standardmetaarkiv](#default-metastore)
* [Anpassat metaarkiv](#custom-metastore)

## <a name="default-metastore"></a>Standardmetaarkiv

Som standard etablerar ett metaarkiv med varje typ av kluster i HDInsight. Du kan i stället ange en anpassad metaarkiv. Standardmetaarkiv innefattar följande överväganden:
- Utan extra kostnad. HDInsight etablerar ett metaarkiv med varje typ av kluster utan extra kostnad för dig.
- Varje standardmetaarkiv är en del av klustrets livscykel. När du tar bort ett kluster att raderas även metaarkiv och metadata.
- Du kan inte dela standardmetaarkiv med andra kluster.
- Standardmetaarkiv använder grundläggande Azure SQL DB, som har en gräns för 5 DTU (database transaction unit).
Den här standardmetaarkiv används vanligtvis för relativt enkla arbetsbelastningar som inte kräver flera kluster och behöver inte metadata bevaras utöver klustrets livscykel.


## <a name="custom-metastore"></a>Anpassat metaarkiv

HDInsight har också stöd för anpassade metastores som rekommenderas för produktionskluster:
- Du anger din egen Azure SQL-databas som metaarkiv.
- Livscykeln för metaarkiv är inte kopplad till en kluster-livscykel, så att du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel dina Hive-scheman behålls även när du tar bort och återskapa HDInsight-klustret.
- Ett anpassat metaarkiv kan du koppla flera kluster och klustertyper till den metaarkiv. Till exempel kan ett enda metaarkiv delas mellan interaktiv fråga, Hive och Spark-kluster i HDInsight.
- Du betalar för kostnaden för metaarkiv (Azure SQL DB) enligt den prestandanivå som du väljer.
- Du kan skala upp metaarkiv efter behov.


![HDInsight Hive-Metadata Store användningsfall](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metaarkiv när klustret skapas

Du kan peka ditt kluster till ett tidigare skapade Azure SQL Database när klustret skapas eller när klustret har skapats kan du konfigurera SQL-databasen. Det här alternativet anges med Storage > Inställningar för Metaarkiv när du skapar en ny Hadoop, Spark eller interaktiva Hive-kluster från Azure-portalen.

![HDInsight Hive-Metadata Store Azure-portalen](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Du kan också lägga till ytterligare kluster till ett anpassat metaarkiv från Azure-portalen eller från Ambari-konfigurationer (Hive-> Avancerat)

![HDInsight Hive-Metadata Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive-metaarkiv Metodtips

Här är några allmänna HDInsight Hive metastore metodtips:

- Använd ett anpassat metaarkiv när det är möjligt på så sätt kan separat beräkningsresurser (som körs klustret) och metadata (lagras i metaarkiv).
- Börja med en S2-nivå som ger 50 DTU och 250 GB lagring. Om du ser en flaskhals kan skala du databasen upp.
- Se till att metaarkiv som skapats för ett HDInsight-kluster av version inte delas mellan olika versioner av HDInsight-kluster. Olika versioner av Hive använda olika scheman. Exempel: du kan inte dela ett metaarkiv med både Hive 1.2 och Hive 2.1-kluster.
- Säkerhetskopiera dina anpassade metastore med jämna mellanrum.
- Se metaarkiv och HDInsight-kluster i samma region.
- Övervaka din metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database-övervakning verktyg, till exempel Azure portal eller Azure Log Analytics.

## <a name="oozie-metastore"></a>Oozie-Metaarkiv

Apache Oozie är ett system för samordning av arbetsflöden som hanterar Hadoop-jobb.  Oozie stöder Hadoop-jobb för Apache MapReduce, Pig, Hive och andra.  Oozie använder ett metaarkiv för att lagra information om aktuella och slutförda arbetsflöden. Du kan använda Azure SQL Database som en anpassad metaarkiv för att öka prestandan när du använder Oozie. Metaarkiv kan också ge åtkomst till Oozie jobbdata när du har tagit bort ditt kluster.

Anvisningar om hur du skapar en Oozie metastore med Azure SQL Database finns i [använda Oozie för arbetsflöden](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md)
