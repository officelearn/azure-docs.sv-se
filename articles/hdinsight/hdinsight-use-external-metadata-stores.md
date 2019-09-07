---
title: Använd externa metadata-butiker – Azure HDInsight
description: Använd externa metadata butiker med Azure HDInsight-kluster och bästa praxis.
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 38e33c0e138b115010b22cc3510b93be98546b50
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735872"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använd externa metadata butiker i Azure HDInsight

Apache Hive metaarkiv i HDInsight är en viktig del av Apache Hadoop arkitekturen. En metaarkiv är den centrala schema lagrings platsen som kan användas av andra Big Data Access-verktyg som Apache Spark, Interactive Query (LLAP), Presto eller Apache gris. HDInsight använder en Azure SQL Database som Hive-metaarkiv.

![HDInsight Hive metadata lager arkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt att konfigurera en metaarkiv för dina HDInsight-kluster:

* [Standard metaarkiv](#default-metastore)
* [Anpassad metaarkiv](#custom-metastore)

## <a name="default-metastore"></a>Standard metaarkiv

Som standard skapar HDInsight en metaarkiv med varje kluster typ. I stället kan du ange en anpassad metaarkiv. Standard-metaarkiv innehåller följande överväganden:
- Ingen ytterligare kostnad. HDInsight skapar en metaarkiv med varje kluster typ utan ytterligare kostnad för dig.
- Varje standard-metaarkiv ingår i kluster livs cykeln. När du tar bort ett kluster raderas även motsvarande metaarkiv och metadata.
- Du kan inte dela standard-metaarkiv med andra kluster.
- Standard-metaarkiv använder Basic Azure SQL DB, som har fem DTU-gränser (Database Transaction Unit).
Den här standard metaarkiv används vanligt vis för relativt enkla arbets belastningar som inte kräver flera kluster och som inte behöver metadata som bevaras utanför klustrets livs cykel.


## <a name="custom-metastore"></a>Anpassad metaarkiv

HDInsight har också stöd för anpassade metastores, vilket rekommenderas för produktions kluster:
- Du anger din egen Azure SQL Database som metaarkiv.
- Livs cykeln för metaarkiv är inte kopplad till en kluster livs cykel, så du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel Hive-scheman, sparas även när du har tagit bort och återskapat HDInsight-klustret.
- Med en anpassad metaarkiv kan du koppla flera kluster och kluster typer till den metaarkiv. Till exempel kan en enda metaarkiv delas mellan interaktiva Query-, Hive-och Spark-kluster i HDInsight.
- Du betalar för kostnaden för en metaarkiv (Azure SQL DB) enligt den prestanda nivå som du väljer.
- Du kan skala upp metaarkiv efter behov.

![Användnings fall för HDInsight Hive-metadatalagret](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metaarkiv när klustret skapas

Du kan peka klustret till ett tidigare skapat Azure SQL Database när klustret skapas, eller så kan du konfigurera SQL Database när klustret har skapats. Det här alternativet anges med **inställningarna för lagrings > metaarkiv** när du skapar ett nytt Hadoop-, Spark-eller Interactive Hive-kluster från Azure Portal.

![HDInsight Hive-metadatalagret Azure Portal](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Du kan också lägga till ytterligare kluster i en anpassad metaarkiv från Azure Portal eller från Ambari-konfigurationer (Hive > Avancerat)

![HDInsight Hive metadata Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Metod tips för Hive-metaarkiv

Här följer några allmänna HDInsight-Hive-metaarkiv bästa praxis:

- Använd en anpassad metaarkiv när det är möjligt, för att hjälpa till att separera beräknings resurser (ditt kluster som körs) och metadata (lagrade i metaarkiv).
- Börja med en S2-nivå, som tillhandahåller 50 DTU och 250 GB lagrings utrymme. Om du ser en Flask hals kan du skala databasen uppåt.
- Om du har flera HDInsight-kluster för åtkomst till separata data använder du en separat databas för metaarkiv på varje kluster. Om du delar en metaarkiv över flera HDInsight-kluster innebär det att klustren använder samma metadata och underliggande användar data filer.
- Säkerhetskopiera dina anpassade metaarkiv med jämna mellanrum. Azure SQL Database skapar säkerhets kopior automatiskt, men tids perioden för säkerhets kopieringen varierar. Mer information finns i [Läs mer om automatisk SQL Database säkerhets kopiering](../sql-database/sql-database-automated-backups.md).
- Leta upp ditt metaarkiv-och HDInsight-kluster i samma region, för högsta prestanda och lägsta utgående kostnader för nätverket.
- Övervaka dina metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database övervaknings verktyg, till exempel Azure Portal eller Azure Monitor loggar.
- När en ny, högre version av Azure HDInsight skapas mot en befintlig anpassad metaarkiv-databas, uppgraderas schemat för metaarkiv, vilket inte kan återställas utan att databasen återställs från säkerhets kopian.
- Om du delar en metaarkiv över flera kluster ser du till att alla kluster har samma HDInsight-version. Olika Hive-versioner använder olika metaarkiv-databasschemat. Du kan till exempel inte dela en metaarkiv över Hive-kluster med Hive 1,2 och Hive 2,1-versioner. 

##  <a name="apache-oozie-metastore"></a>Apache Oozie-Metaarkiv

Apache Oozie är ett system för arbets flödes koordinering som hanterar Hadoop-jobb.  Oozie stöder Hadoop-jobb för Apache MapReduce, gris, Hive och andra.  Oozie använder en metaarkiv för att lagra information om aktuella och slutförda arbets flöden. Om du vill öka prestandan när du använder Oozie kan du använda Azure SQL Database som en anpassad metaarkiv. Metaarkiv kan även ge åtkomst till Oozie-jobb data när du har tagit bort klustret.

Anvisningar om hur du skapar en Oozie-metaarkiv med Azure SQL Database finns i [använda Apache Oozie för arbets flöden](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md)
