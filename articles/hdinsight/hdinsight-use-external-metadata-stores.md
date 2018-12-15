---
title: Använda extern metadatalagring - Azure HDInsight
description: Använda extern metadatalagring med HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 9c35a4a811925abaf8dcb64d3e7060bbb1f91cce
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408331"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använda extern metadatalagring i Azure HDInsight

Apache Hive-metaarkiv i HDInsight är en viktig del av Apache Hadoop-arkitektur. Ett metaarkiv finns centrala schemadatabasen som kan användas av andra åtkomst stordataverktyg, till exempel Apache Spark, Interactive Query (LLAP), Presto eller Apache Pig. HDInsight använder en Azure SQL Database som Hive-metaarkiv.

![HDInsight Hive-Metadata Store-arkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt som du kan ställa in ett metaarkiv för dina HDInsight-kluster:

* [Standardmetaarkiv](#default-metastore)
* [Anpassat metaarkiv](#custom-metastore)

## <a name="default-metastore"></a>Standardmetaarkiv

Som standard skapar ett metaarkiv med varje typ av kluster i HDInsight. Du kan i stället ange en anpassad metaarkiv. Standardmetaarkiv innefattar följande överväganden:
- Utan extra kostnad. HDInsight skapar ett metaarkiv med varje typ av kluster utan extra kostnad för dig.
- Varje standardmetaarkiv är en del av klustrets livscykel. När du tar bort ett kluster, raderas också motsvarande metaarkiv och metadata.
- Du kan inte dela standardmetaarkiv med andra kluster.
- Standardmetaarkiv använder grundläggande Azure SQL DB, som har en gräns för fem DTU (database transaction unit).
Den här standardmetaarkiv används vanligtvis för relativt enkla arbetsbelastningar som inte kräver flera kluster och behöver inte metadata bevaras utöver klustrets livscykel.


## <a name="custom-metastore"></a>Anpassat metaarkiv

HDInsight har också stöd för anpassade metastores som rekommenderas för produktionskluster:
- Du anger din egen Azure SQL-databas som metaarkiv.
- Livscykeln för metaarkiv är inte kopplad till en kluster-livscykel, så att du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel dina Hive-scheman behålls även när du tar bort och återskapa HDInsight-klustret.
- Ett anpassat metaarkiv kan du koppla flera kluster och klustertyper till den metaarkiv. Till exempel kan ett enda metaarkiv delas mellan interaktiv fråga, Hive och Spark-kluster i HDInsight.
- Du betalar för kostnaden för metaarkiv (Azure SQL DB) enligt den prestandanivå som du väljer.
- Du kan skala upp metaarkiv efter behov.

![HDInsight Hive-Metadata Store användningsfall](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metaarkiv när klustret skapas

Du kan peka ditt kluster till ett tidigare skapade Azure SQL Database när klustret skapas eller när klustret har skapats kan du konfigurera SQL-databasen. Det här alternativet anges med Storage > Inställningar för Metaarkiv när du skapar en ny Hadoop, Spark eller interaktiva Hive-kluster från Azure-portalen.

![HDInsight Hive-Metadata Store Azure-portalen](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Du kan också lägga till ytterligare kluster till ett anpassat metaarkiv från Azure-portalen eller från Ambari-konfigurationer (Hive-> Avancerat)

![HDInsight Hive-Metadata Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive-metaarkiv Metodtips

Här är några allmänna HDInsight Hive metastore metodtips:

- Använd ett anpassat metaarkiv när det är möjligt att separat beräkningsresurser (som körs klustret) och metadata (lagras i metaarkiv).
- Börja med en S2-nivå som ger 50 DTU och 250 GB lagring. Om du ser en flaskhals kan skala du databasen upp.
- Om du planerar flera HDInsight-kluster på separata dataåtkomst, kan du använda en separat databas för metaarkiv på varje kluster. Om du delar ett metaarkiv över flera HDInsight-kluster, innebär det att klustren använder samma metadata och filer för underliggande användardata.
- Säkerhetskopiera dina anpassade metastore med jämna mellanrum. Säkerhetskopior genererar automatiskt i Azure SQL-databas, men en mer specifik tidsram för kvarhållning av säkerhetskopior varierar. Mer information finns i [Lär dig mer om automatisk SQL Database-säkerhetskopior](../sql-database/sql-database-automated-backups.md).
- Leta upp din metaarkiv och HDInsight-kluster i samma region för högsta prestanda och kostnader för utgående trafik för lägsta nätverk.
- Övervaka din metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database-övervakning verktyg, till exempel Azure portal eller Azure Log Analytics.
- När en ny, högre version av Azure HDInsight skapas mot en befintlig anpassad metaarkiv-databas, uppgraderar systemet schemat för metaarkiv, vilket är oåterkallelig utan att återställa databasen från en säkerhetskopia.
- Om du delar ett metaarkiv över flera kluster kan du kontrollera att alla kluster är samma HDInsight-version. Olika Hive versioner använder olika metaarkiv databasscheman. Du kan inte till exempel dela ett metaarkiv för Hive 1.2 och Hive 2.1 version kluster. 

##  <a name="apache-oozie-metastore"></a>Apache Oozie-Metaarkiv

Apache Oozie är ett system för samordning av arbetsflöden som hanterar Hadoop-jobb.  Oozie stöder Hadoop-jobb för Apache MapReduce, Pig, Hive och andra.  Oozie använder ett metaarkiv för att lagra information om aktuella och slutförda arbetsflöden. Du kan använda Azure SQL Database som en anpassad metaarkiv för att öka prestandan när du använder Oozie. Metaarkiv kan också ge åtkomst till Oozie jobbdata när du har tagit bort ditt kluster.

Anvisningar om hur du skapar en Oozie metastore med Azure SQL Database finns i [Använd Apache Oozie för arbetsflöden](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md)
