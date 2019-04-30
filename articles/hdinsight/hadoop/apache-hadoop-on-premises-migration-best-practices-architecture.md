---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - arkitektur Metodtips
description: Lär dig Metodtips för arkitektur för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: d1f2b79ff3ae33adb0b6e3ce5a6d96ad38fb1562
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129339"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - arkitektur Metodtips

Den här artikeln innehåller rekommendationer för arkitektur för Azure HDInsight-system. Det är en del i en serie som ger bästa praxis för att hjälpa migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Använda flera arbetsbelastning-optimerade kluster

Många lokala Apache Hadoop-distributioner som består av ett stort kluster som har stöd för många arbetsbelastningar. Den här enda kluster kan vara komplexa och kan kräva kompromisser till enskilda tjänster för att få allt att fungera tillsammans. Migrera lokala Hadoop-kluster till Azure HDInsight kräver en ändring i metoden.

Azure HDInsight-kluster har utformats för en viss typ av beräkning användning. Eftersom lagring kan delas mellan flera kluster, är det möjligt att skapa flera arbetsbelastning-optimerade beräkningskluster för att uppfylla behoven hos olika jobb. Varje typ av kluster har den bästa konfigurationen för den specifika arbetsbelastningen. I följande tabell visas stöds klustertyper i HDInsight och motsvarande arbetsbelastningar.

|**Arbetsbelastning**|**Typ av HDInsight-kluster**|
|---|---|
|Batchbearbetning (ETL / ELT)|Hadoop, Spark|
|Datalagerhantering|Hadoop, Spark, Interactive Query|
|IoT / Streaming|Kafka, Storm, Spark|
|NoSQL transaktionsbearbetning|HBase|
|Interaktiv och snabbare frågor med cachelagring i minnet|Interaktiv fråga|
|Datavetenskap|ML-tjänster, Spark|

I följande tabell visas olika metoder som kan användas för att skapa ett HDInsight-kluster.

|**Verktyg**|**Webbläsarbaserade**|**Från kommandoraden**|**REST-API**|**SDK**|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (version 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Mer information finns i artikeln [Klustertyper i HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Använda kluster för tillfälliga på begäran

HDInsight-kluster kan gå oanvända under långa tidsperioder. För att spara på resurskostnader, stöder HDInsight på begäran tillfälliga kluster, som kan tas bort när arbetsbelastningen har slutförts.

När du tar bort ett kluster är det associerade lagringskontot och externa metadata inte bort. Klustret skapas senare igen med hjälp av samma storage-konton och meta-butiker.

Azure Data Factory kan användas för att schemalägga skapandet av på begäran HDInsight-kluster. Mer information finns i artikeln [skapa på begäran Apache Hadoop-kluster i HDInsight med Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Frikoppla lagring från beräkning

Typiska lokala Hadoop-distributioner kan du använda samma uppsättning datorer för datalagring och bearbetning av data. Eftersom de är samordnade måste beräkning och lagring skalas tillsammans.

Storage inte behöver inte samplaceras med beräkning på HDInsight-kluster och kan antingen vara i Azure storage, Azure Data Lake Storage eller båda. Frikoppling lagring från beräkning har följande fördelar:

- Datadelning mellan kluster.
- Användning av tillfälliga kluster eftersom data inte är beroende av kluster.
- Minskar kostnaden för lagring.
- Skala lagring och beräkning separat.
- Replikering av data mellan regioner.

Compute beräkningsklustren skapas nära lagringskontoresurserna i en Azure-region att minska kostnaden för att avgränsa beräkning och lagring. Snabba nätverk kan du effektivt för beräkningsnoderna kan komma åt data i Azure storage.

## <a name="use-external-metadata-stores"></a>Använda extern metadatalagring


Det finns två huvudsakliga metastores som fungerar med HDInsight-kluster: [Apache Hive](https://hive.apache.org/) och [Apache Oozie](https://oozie.apache.org/). Hive-metaarkiv finns centrala schemadatabasen som kan användas av databearbetning-motorer, inklusive Hadoop, Spark, LLAP, Presto och Apache Pig. Oozie-metaarkiv lagrar information om att schemalägga och status för pågående och slutförda Hadoop-jobb.


HDInsight använder Azure SQL Database för metastores för Hive och Oozie. Det finns två sätt att ställa in ett metaarkiv i HDInsight-kluster:

1. Standardmetaarkiv

    - Utan extra kostnad.
    - Metaarkiv tas bort när klustret tas bort.
    - Metaarkiv kan inte delas mellan olika kluster.
    - Använder grundläggande Azure SQL DB, som har en fem DTU-gräns.

1. Anpassade externt metaarkiv

    - Ange en extern Azure SQL-databas som metaarkiv.
    - Kluster kan skapas och tas bort utan att förlora metadata, inklusive Hive schemat Oozie jobbinformation.
    - Enkel metaarkiv db kan delas med olika typer av kluster.
    - Metaarkiv kan skalas upp efter behov.
    - Mer information finns i [använda extern metadatalagring i Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Metodtips för Hive-Metaarkiv

Några Metodtips för HDInsight Hive-metaarkiv är följande:

- Använd en anpassad externt metaarkiv separat beräkningsresurser och metadata.
- Börja med en S2-nivån Azure SQL-instansen, som ger 50 DTU och 250 GB lagring. Om du ser en flaskhals kan skala du databasen upp.
- Dela inte metaarkiv som skapats för ett HDInsight-kluster av version med kluster i en annan version. Olika versioner av Hive använda olika scheman. Till exempel kan ett metaarkiv inte delas med både Hive 1.2 och Hive 2.1-kluster.
- Säkerhetskopiera anpassade metastore med jämna mellanrum.
- Behåll metaarkiv och HDInsight-kluster i samma region.
- Övervaka metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database-övervakning verktyg som Azure-portalen eller Azure Monitor-loggar.
- Kör den **analysera tabell** kommandot som krävs för att skapa statistik för tabeller och kolumner. Till exempel `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Metodtips för olika arbetsbelastningar

- Överväg att använda LLAP kluster för interaktiva Hive-frågor med förbättrad svarstid [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) är en ny funktion i Hive 2.0 som gör att cachelagra i minnet för frågor. LLAP gör Hive-frågor som är mycket snabbare, upp till [26 x snabbare än Hive 1.x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Överväg att använda Spark-jobb i stället för Hive-jobb.
- Överväg att ersätta impala-baserade frågor med LLAP frågor.
- Överväg att ersätta MapReduce-jobb med Spark-jobb.
- Överväg att ersätta med låg latens Spark batch-jobb med hjälp av Spark Structured Streaming jobb.
- Överväg att använda Azure Data Factory (ADF) 2.0 för datadirigering.
- Överväg att Ambari för klusterhantering.
- Ändra lagring av data från lokala HDFS WASB eller ADLS eller AD FS för bearbetning av skript.
- Överväg att använda Ranger RBAC på Hive-tabeller och granskning.
- Överväg att använda CosmosDB i stället för MongoDB eller Cassandra.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metodtips för infrastruktur för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
