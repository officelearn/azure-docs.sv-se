---
title: Använda externa metadata lagrar - Azure HDInsight | Microsoft Docs
description: Använda externa metadata lagras med HDInsight-kluster.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: 26f71b8efb7d31192ceecf03d9c0ed904196faf1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använda externa metadata lagras i Azure HDInsight

Hive metastore i HDInsight är en viktig del av Hadoop-arkitektur. En metastore är central schemadatabasen som kan användas av andra verktyg för åtkomst av stordata som Spark, interaktiva fråga (LLAP), Presto eller Pig. HDInsight använder Azure SQL-databas som Hive metastore.

![HDInsight Hive-Metadata Store-arkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt som du kan ställa in en metastore för HDInsight-kluster:

* [Standard metastore](#default-metastore)
* [Anpassade metastore](#custom-metastore)

## <a name="default-metastore"></a>Standard metastore

Som standard etablerar HDInsight en metastore med varje typ av kluster. I stället kan du ange en anpassad metastore. Standard-metastore innefattar följande överväganden:
- Utan extra kostnad. HDInsight etablerar en metastore med varje typ av kluster utan någon extra kostnad för dig.
- Varje standard metastore ingår i livscykeln för klustret. När du tar bort ett kluster att raderas även metastore och metadata.
- Du kan inte dela standard metastore med andra kluster.
- Standard-metastore använder grundläggande Azure SQL DB, som har en gräns för 5 DTU (database transaction unit).
Den här standard metastore används vanligtvis för relativt enkla arbetsbelastningar som inte kräver flera kluster och behöver inte metadata bevaras utöver klustrets livscykel.


## <a name="custom-metastore"></a>Anpassade metastore

HDInsight stöder även anpassade metastores som rekommenderas för driftskluster:
- Du kan ange egna Azure SQL Database som metastore.
- Livscykeln för metastore är inte kopplat till ett kluster livscykel, så att du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel Hive-scheman behålls när du tar bort och återskapa HDInsight-klustret.
- En anpassad metastore kan du bifoga flera kluster och klustertyper som metastore. Exempelvis kan en enda metastore delas mellan interaktiva frågan, Hive och Spark-kluster i HDInsight.
- Du betalar för kostnaden för metastore (Azure SQL DB) enligt prestandanivå du väljer.
- Du kan skala upp metastore efter behov.


![HDInsight Hive-Metadata Store användningsfall](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metastore när klustret skapas

Du kan ange ditt kluster till ett tidigare skapade Azure SQL Database när klustret skapas eller när klustret har skapats kan du konfigurera SQL-databasen. Det här alternativet anges med lagringen > Metastore inställningar när du skapar en ny Hadoop, Spark eller interaktiv Hive kluster från Azure-portalen.

![HDInsight Hive-Metadata Store Azure-portalen](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Du kan också lägga till ytterligare kluster anpassade metastore från Azure-portalen eller Ambari konfigurationer (Hive > Avancerat)

![HDInsight Hive-Metadata Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive metastore bästa praxis

Här är några allmänna HDInsight Hive metastore metoder:

- Använda en anpassad metastore när det är möjligt eftersom detta hjälper separat beräkningsresurser (körs klustret) och metadata (som lagras i metastore).
- Börja med en S2-nivå som ger 50 DTU och 250 GB lagring. Om du ser en flaskhals kan du skala databasen upp.
- Se till att metastore som skapats för ett HDInsight-kluster av version inte delas mellan olika versioner av HDInsight-kluster. Olika scheman används för olika versioner av Hive. Exempelvis kan du dela en metastore med både Hive 1.2 och Hive 2.1.
- Säkerhetskopiera dina anpassade metastore med jämna mellanrum.
- Hålla dina metastore och HDInsight-kluster i samma region.
- Övervaka dina metastore för prestanda och tillgänglighet med hjälp av Azure SQL Database-övervakning verktyg, till exempel Azure-portalen eller Azure logganalys.

## <a name="oozie-metastore"></a>Oozie Metastore

Apache Oozie är ett system för samordning av arbetsflödet som hanterar Hadoop-jobb.  Oozie stöder Hadoop-jobb för Apache MapReduce, Pig, Hive och andra.  Oozie använder en metastore för att lagra information om aktuella och slutförda arbetsflöden. Du kan använda Azure SQL Database som en anpassad metastore för att öka prestandan när du använder Oozie. Metastore kan också ge åtkomst till Oozie jobbdata när du tar bort klustret.

Anvisningar om hur du skapar en Oozie metastore med Azure SQL Database finns [Använd Oozie för arbetsflöden](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

- [Ställ in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](./hdinsight-hadoop-provision-linux-clusters.md)
