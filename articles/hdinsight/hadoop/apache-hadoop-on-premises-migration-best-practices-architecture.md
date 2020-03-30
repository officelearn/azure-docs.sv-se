---
title: 'Arkitektur: Lokala Apache Hadoop till Azure HDInsight'
description: Lär dig metodtips för arkitektur för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934691"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – metodtips för arkitektur

Den här artikeln innehåller rekommendationer för arkitekturen i Azure HDInsight-system. Det är en del av en serie som innehåller metodtips för att hjälpa till med att migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Använda flera arbetsbelastningsoptimerade kluster

Många lokala Apache Hadoop-distributioner består av ett enda stort kluster som stöder många arbetsbelastningar. Det här enskilda klustret kan vara komplext och kan kräva kompromisser med de enskilda tjänsterna för att få allt att fungera tillsammans. Migrera lokala Hadoop-kluster till Azure HDInsight kräver en metodändring.

Azure HDInsight-kluster är utformade för en viss typ av beräkningsanvändning. Eftersom lagring kan delas mellan flera kluster är det möjligt att skapa flera arbetsbelastningsoptimerade beräkningskluster för att uppfylla behoven för olika jobb. Varje klustertyp har den optimala konfigurationen för den specifika arbetsbelastningen. I följande tabell visas klustertyperna som stöds i HDInsight och motsvarande arbetsbelastningar.

|Arbetsbelastning|HdInsight-klustertyp|
|---|---|
|Batchbearbetning (ETL / ELT)|Hadoop, Gnista|
|Datalagerhantering|Hadoop, Spark, Interaktiv fråga|
|IoT / Streaming|Kafka, Storm, Gnista|
|Bearbetning av NoSQL-transaktion|HBase|
|Interaktiva och snabbare frågor med cachelagring i minnet|Interaktiv fråga|
|Data Vetenskap|ML-tjänster, Gnista|

I följande tabell visas de olika metoder som kan användas för att skapa ett HDInsight-kluster.

|Verktyg|Webbläsarbaserad|Kommandorad|REST API|SDK|
|---|---|---|---|---|
|[Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Curl](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Mer information finns i artikeln [Klustertyper i HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Använda tillfälliga kluster på begäran

HDInsight-kluster kan gå oanvända under en längre tid. För att spara på resurskostnader stöder HDInsight tillfälliga kluster på begäran, som kan tas bort när arbetsbelastningen har slutförts.

När du tar bort ett kluster tas inte det associerade lagringskontot och externa metadata bort. Klustret kan senare återskapas med samma lagringskonton och metalager.

Azure Data Factory kan användas för att schemalägga skapandet av HDInsight-kluster på begäran. Mer information finns i artikeln [Skapa Apache Hadoop-kluster på begäran i HDInsight med Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Frikoppla lagring från beräkning

Typiska lokala Hadoop-distributioner använder samma uppsättning datorer för datalagring och databehandling. Eftersom de är samlokaliseras måste beräkning och lagring skalas tillsammans.

På HDInsight-kluster behöver lagring inte samlokaliseras med beräkning och kan antingen vara i Azure-lagring, Azure Data Lake Storage eller båda. Frikoppling lagring från beräkning har följande fördelar:

- Datadelning mellan kluster.
- Användning av tillfälliga kluster eftersom data inte är beroende av klustret.
- Minskad lagringskostnad.
- Skala lagring och beräkning separat.
- Datareplikering mellan regioner.

Beräkningskluster skapas nära lagringskontoresurser i en Azure-region för att minska prestandakostnaden för att separera beräkning och lagring. Höghastighetsnätverk gör det effektivt för beräkningsnoderna att komma åt data i Azure-lagring.

## <a name="use-external-metadata-stores"></a>Använda extern metadatalagring

Det finns två huvudsakliga metabutiker som fungerar med HDInsight kluster: [Apache Hive](https://hive.apache.org/) och [Apache Oozie](https://oozie.apache.org/). Hive-metabutiken är den centrala schemalagringsplatsen som kan användas av databehandlingsmotorer, inklusive Hadoop, Spark, LLAP, Presto och Apache Pig. Oozie metastore lagrar information om schemaläggning och status för pågående och avslutade Hadoop jobb.

HDInsight använder Azure SQL Database för Hive- och Oozie-metabutiker. Det finns två sätt att skapa en metabutik i HDInsight-kluster:

1. Standardmetastore

    - Ingen extra kostnad.
    - Metastore tas bort när klustret tas bort.
    - Metastore kan inte delas mellan olika kluster.
    - Använder grundläggande Azure SQL DB, som har en fem DTU-gräns.

1. Anpassad extern metabutik

    - Ange en extern Azure SQL-databas som metabutik.
    - Kluster kan skapas och tas bort utan att förlora metadata, inklusive Hive-schema Oozie-jobbinformation.
    - Enstaka metastore db kan delas med olika typer av kluster.
    - Metastore kan skalas upp efter behov.
    - Mer information finns [i Använda externa metadatalager i Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Bästa praxis för Hive Metastore

Vissa HDInsight Hive metastore bästa praxis är följande:

- Använd en anpassad extern metabutik för att separera beräkningsresurser och metadata.
- Börja med en Azure SQL-instans på S2-nivå, som ger 50 DTU och 250 GB lagringsutrymme. Om du ser en flaskhals kan du skala upp databasen.
- Dela inte metabutiken som skapats för en HDInsight-klusterversion med kluster av en annan version. Olika Hive-versioner använder olika scheman. En metabutik kan till exempel inte delas med både Hive 1.2- och Hive 2.1-kluster.
- Säkerhetskopiera den anpassade metabutiken med jämna mellanrum.
- Behåll metabutiken och HDInsight-klustret i samma region.
- Övervaka metabutiken för prestanda och tillgänglighet med hjälp av Azure SQL Database Monitoring-verktyg, till exempel Azure Portal- eller Azure Monitor-loggar.
- Kör `ANALYZE TABLE` kommandot efter behov för att generera statistik för tabeller och kolumner. Till exempel `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Metodtips för olika arbetsbelastningar

- Överväg att använda LLAP-kluster för interaktiva Hive-frågor med förbättrad svarstid [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) är en ny funktion i Hive 2.0 som gör att minnescachelagring av frågor. LLAP gör Hive-frågor mycket snabbare, upp till [26x snabbare än Hive 1.x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Överväg att använda Spark-jobb i stället för Hive-jobb.
- Överväg att ersätta impala-baserade frågor med LLAP-frågor.
- Överväg att ersätta MapReduce-jobb med Spark-jobb.
- Överväg att ersätta spark-batchjobb med låg latens med Spark Structured Streaming-jobb.
- Överväg att använda Azure Data Factory (ADF) 2.0 för dataorkestrering.
- Överväg Ambari för klusterhantering.
- Ändra datalagring från lokala HDFS till WASB eller ADLS eller ADFS för bearbetning av skript.
- Överväg att använda Ranger RBAC på Hive-tabeller och granskning.
- Överväg att använda CosmosDB i stället för MongoDB eller Cassandra.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metodtips för infrastruktur för lokal till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
