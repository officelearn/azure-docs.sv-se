---
title: Använda externa metadatalager - Azure HDInsight
description: Använd externa metadataarkiv med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657109"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använda extern metadatalagring i Azure HDInsight

HDInsight kan du ta kontroll över dina data och metadata med externa datalager. Den här funktionen är tillgänglig för [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore)och [Apache Ambari databas](#custom-ambari-db).

Apache Hive-metabutiken i HDInsight är en viktig del av Apache Hadoop-arkitekturen. En metabutik är den centrala schemadatabasen. Metabutiken används av andra big data-åtkomstverktyg som Apache Spark, Interactive Query (LLAP), Presto eller Apache Pig. HDInsight använder en Azure SQL-databas som Hive-metabutik.

![HdInsight Hive-metadatabutiksarkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt att skapa en metabutik för dina HDInsight-kluster:

* [Standardmetastore](#default-metastore)
* [Anpassad metabutik](#custom-metastore)

## <a name="default-metastore"></a>Standardmetastore

Som standard skapar HDInsight en metabutik med alla klustertyper. Du kan i stället ange en anpassad metabutik. Standardmetastore innehåller följande överväganden:

* Ingen extra kostnad. HDInsight skapar en metabutik med varje klustertyp utan extra kostnad för dig.

* Varje standardmetastore är en del av klustrets livscykel. När du tar bort ett kluster tas även motsvarande metabutik och metadata bort.

* Du kan inte dela standardmetastore med andra kluster.

* Standardmetastore använder den grundläggande Azure SQL DB, som har en gräns för fem DTU (databastransaktionsenhet).
Den här standardmetastore används vanligtvis för relativt enkla arbetsbelastningar. Arbetsbelastningar som inte kräver flera kluster och inte behöver metadata bevarade utanför klustrets livscykel.

## <a name="custom-metastore"></a>Anpassad metabutik

HDInsight stöder även anpassade metabutiker, som rekommenderas för produktionskluster:

* Du anger din egen Azure SQL-databas som metabutik.

* Metabutikens livscykel är inte kopplad till en klustrets livscykel, så du kan skapa och ta bort kluster utan att förlora metadata. Metadata som Hive-scheman sparas även efter att du har tagit bort och återskapat HDInsight-klustret.

* Med en anpassad metabutik kan du koppla flera kluster och klustertyper till metabutiken. En enda metabutik kan till exempel delas i interaktiva fråge-, Hive- och Spark-kluster i HDInsight.

* Du betalar för kostnaden för en metabutik (Azure SQL DB) enligt den prestandanivå du väljer.

* Du kan skala upp metabutiken efter behov.

* Klustret och den externa metabutiken måste vara värd i samma region.

![HdInsight Hive Metadata Store Användningsfall](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Skapa och konfigurera Azure SQL Database för den anpassade metabutiken

Skapa eller har en befintlig Azure SQL-databas innan du konfigurerar en anpassad Hive-metabutik för ett HDInsight-kluster.  Mer information finns i [Snabbstart: Skapa en enda databas i Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Konfigurera Azure SQL Database-brandväggsregler så att Azure-tjänster och resurser kan komma åt servern. Aktivera det här alternativet i Azure-portalen genom att välja **Ange serverbrandvägg**. Välj sedan **PÅ** under **Tillåt Azure-tjänster och resurser för att komma åt** den här servern för Azure SQL Database-servern eller -databasen. Mer information finns i [Skapa och hantera IP-brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![ange knappen serverbrandvägg](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![tillåt åtkomst till azure-tjänster](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metabutik när klustret skapas

Du kan när som helst peka klustret på en tidigare skapad Azure SQL-databas. För klusterskapande via portalen anges alternativet från **inställningarna för lagring > Metastore**.

![Azure-portal för HDInsight Hive-metadata store](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Riktlinjer för Hive-metabutik

* Använd en anpassad metabutik när det är möjligt för att hjälpa till att separera beräkningsresurser (ditt kluster som körs) och metadata (lagras i metabutiken).

* Börja med en S2-nivå som ger 50 DTU och 250 GB lagringsutrymme. Om du ser en flaskhals kan du skala upp databasen.

* Om du tänker att flera HDInsight-kluster ska komma åt separata data använder du en separat databas för metabutiken i varje kluster. Om du delar en metabutik i flera HDInsight-kluster innebär det att klustren använder samma metadata och underliggande användardatafiler.

* Säkerhetskopiera din anpassade metabutik med jämna mellanrum. Azure SQL Database genererar säkerhetskopior automatiskt, men tidsramen för lagring av säkerhetskopiering varierar. Mer information finns i [Lär dig mer om automatiska säkerhetskopieringar av SQL Database](../sql-database/sql-database-automated-backups.md).

* Leta reda på ditt metabutiks- och HDInsight-kluster i samma region. Den här konfigurationen ger högsta prestanda och lägsta nätverksutgående avgifter.

* Övervaka din metabutik för prestanda och tillgänglighet med hjälp av Azure SQL Database Monitoring-verktyg eller Azure Monitor-loggar.

* När en ny, högre version av Azure HDInsight skapas mot en befintlig anpassad metabutiksdatabas uppgraderar systemet metabutikens schema. Uppgraderingen är oåterkallelig utan att återställa databasen från säkerhetskopiering.

* Om du delar en metabutik över flera kluster kontrollerar du att alla kluster är samma HDInsight-version. Olika Hive-versioner använder olika metabutiksdatabasscheman. Du kan till exempel inte dela en metabutik i grupper i Hive 2.1 och Hive 3.1.

* I HDInsight 4.0 använder Spark och Hive oberoende kataloger för åtkomst till SparkSQL- eller Hive-tabeller. En tabell som skapats av Spark finns i Spark-katalogen. En tabell som skapats av Hive finns i Hive-katalogen. Det här beteendet skiljer sig från HDInsight 3.6 där Hive och Spark har delat gemensam katalog. Hive- och Spark-integrering i HDInsight 4.0 är beroende av Hive Warehouse Connector (HWC). HWC fungerar som en bro mellan Spark och Hive. [Läs mer om Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metabutik

Apache Oozie är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Oozie stöder Hadoop jobb för Apache MapReduce, Pig, Hive, och andra.  Oozie använder en metabutik för att lagra information om arbetsflöden. Om du vill öka prestanda när du använder Oozie kan du använda Azure SQL Database som en anpassad metabutik. Metabutiken ger tillgång till Oozie-jobbdata när du har tagit bort klustret.

Instruktioner om hur du skapar en Oozie-metabutik med Azure SQL Database finns i [Använda Apache Oozie för arbetsflöden](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Anpassad Ambari-databas

Om du vill använda din egen externa databas med Apache Ambari på HDInsight läser du [Custom Apache Ambari-databasen](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md)
