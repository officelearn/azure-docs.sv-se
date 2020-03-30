---
title: 'Fördelar: Migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig motivationen och fördelarna med att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267329"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – motivation och fördelar

Den här artikeln är den första i en serie om bästa praxis för att migrera lokala Apache Hadoop-distributioner till Azure HDInsight. Den här artikelserien är för personer som är ansvariga för design, distribution och migrering av Apache Hadoop-lösningar i Azure HDInsight. Rollerna som kan dra nytta av dessa artiklar inkluderar molnarkitekter, Hadoop-administratörer och DevOps-tekniker. Programutvecklare, datatekniker och datavetare bör också dra nytta av förklaringen av hur olika typer av kluster fungerar i molnet.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Varför migrera till Azure HDInsight

Azure HDInsight är en molndistribution av Hadoop-komponenter. Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. HDInsight innehåller de mest populära ramverken med öppen källkod, till exempel:

- Apache Hadoop
- Apache Spark
- Apache Hive med LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight fördelar jämfört med lokala Hadoop

- **Låg kostnad** - Kostnaderna kan minskas genom [att skapa kluster på begäran](../hdinsight-hadoop-create-linux-clusters-adf.md) och betala endast för det du använder. Frikopplad beräkning och lagring ger flexibilitet genom att hålla datavolymen oberoende av klustrets storlek.

- **Automatisk klustergenerering** – Automatisk klustergenerering kräver minimal installation och konfiguration. Automatisering kan användas för kluster på begäran.

- **Hanterad maskinvara och konfiguration** – Du behöver inte oroa dig för den fysiska maskinvaran eller infrastrukturen med ett HDInsight-kluster. Ange bara konfigurationen av klustret och Azure konfigurerar det.

- **Enkelt skalbar** - HDInsight gör att du kan [skala](../hdinsight-administer-use-portal-linux.md) arbetsbelastningar upp eller ned. Azure tar hand om omfördelning av data och ombalansering av arbetsbelastningen utan att avbryta databearbetningsjobb.

- **Global tillgänglighet** – HDInsight är tillgängligt i fler [regioner](https://azure.microsoft.com/regions/services/) än något annat erbjudande om stordataanalys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden.

- **Säker och kompatibel** – HDInsight gör att du kan skydda dina företagsdatatillgångar med [Azure Virtual Network,](../hdinsight-plan-virtual-network-deployment.md) [kryptering](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)och integrering med Azure [Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight uppfyller också de mest populära bransch- och [myndighetsstandarderna.](https://azure.microsoft.com/overview/trusted-cloud)

- **Förenklad versionshantering** – Azure HDInsight hanterar versionen av Hadoop eco-system-komponenter och håller dem uppdaterade. Programuppdateringar är vanligtvis en komplicerad process för lokala distributioner.

- **Mindre kluster som är optimerade för specifika arbetsbelastningar med färre beroenden mellan komponenter** – En typisk lokal Hadoop-konfiguration använder ett enda kluster som betjänar många syften. Med Azure HDInsight kan arbetsbelastningsspecifika kluster skapas. Om du skapar kluster för specifika arbetsbelastningar tas komplexiteten i att underhålla ett enda kluster med växande komplexitet.

- **Produktivitet** - Du kan använda olika verktyg för Hadoop och Spark i din önskade utvecklingsmiljö.

- **Utökningsbarhet med anpassade verktyg eller program från tredje part** - HDInsight-kluster kan utökas med installerade komponenter och kan även integreras med de andra stordatalösningarna med hjälp av distributioner med ett klick från Azure [Market-platsen.](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)

- **Enkel hantering, administration och övervakning** – Azure HDInsight integreras med [Azure Monitor-loggar](../hdinsight-hadoop-oms-log-analytics-tutorial.md) för att tillhandahålla ett enda gränssnitt som du kan övervaka alla dina kluster med.

- **Integrering med andra Azure-tjänster** – HDInsight kan enkelt integreras med andra populära Azure-tjänster, till exempel följande:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Självläkande processer och komponenter** - HDInsight kontrollerar ständigt infrastrukturen och komponenter med öppen källkod med hjälp av sin egen övervakningsinfrastruktur. Det återställer också automatiskt kritiska fel som otillgänglighet av komponenter med öppen källkod och noder. Aviseringar utlöses i Ambari om någon OSS-komponent har misslyckats.

Mer information finns i artikeln [Vad är Azure HDInsight och Apache Hadoop-teknikstacken](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Process för migreringsplanering

Följande steg rekommenderas för att planera en migrering av lokala Hadoop-kluster till Azure HDInsight:

1. Förstå den aktuella lokala distributionen och topologierna.
2. Förstå det aktuella projektets omfattning, tidslinjer och teamexpertis.
3. Förstå Azure-kraven.
4. Bygg fram en detaljerad plan baserat på bästa praxis.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Samla in information för att förbereda en migrering

Det här avsnittet innehåller mallenkäter som hjälper dig att samla in viktig information om:

- Den lokala distributionen
- Projektinformation
- Krav för Azure

### <a name="on-premises-deployment-questionnaire"></a>Lokal distributionsfrågeformulär

| **Fråga** | **Exempel** | **Svar** |
|---|---|---|
|**Ämne**: **Miljö**|||
|Klusterdistributionsversion|HDP 2.6.5, CDH 5.7|
|Komponenter i Big Data-ekosystemet|HDFS, Garn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Klustertyper|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Antal kluster|4|
|Antal huvudnoder|2|
|Antal arbetsnoder|100|
|Antal kantnoder| 5|
|Totalt diskutrymme|100 TB|
|Konfiguration av huvudnod|m /y, cpu, disk, etc.|
|Konfiguration av datanoder|m /y, cpu, disk, etc.|
|Konfiguration av edgenoder|m /y, cpu, disk, etc.|
|HDFS-kryptering?|Ja|
|Hög tillgänglighet|HDFS HA, Metastore HA|
|Katastrofåterställning / Säkerhetskopiering|Backup kluster?|  
|System som är beroende av kluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrationer från tredje part|Tablå, GridGain, Qubole, Informatica, Splunk|
|**Ämne**: **Säkerhet**|||
|Säkerhetsskydd för perimeter|Brandväggar|
|Klusterautentisering & auktorisering|Active Directory, Ambari, Cloudera Manager, Ingen autentisering|
|HDFS-åtkomstkontroll|  Manuell, ssh användare|
|Hive-autentisering & auktorisering|Vaktpost, LDAP, AD med Kerberos, Ranger|
|Granskning|Ambari, Cloudera Navigator, Ranger|
|Övervakning|Grafit, insamlad, statsd, Telegraf, InfluxDB|
|Aviseringar|Kapacitor, Prometheus, Datadog|
|Varaktighet för datalagring| 3 år, 5 år|
|Klusteradministratörer|En enda administratör, flera administratörer|

### <a name="project-details-questionnaire"></a>Enkät om projektinformation

|**Fråga**|**Exempel**|**Svar**|
|---|---|---|
|**Ämne**: **Arbetsbelastningar och frekvens**|||
|MapReduce jobb|10 jobb - två gånger dagligen||
|Hive jobb|100 jobb - varje timme||
|Spark batch jobb|50 jobb - var 15:e minut||
|Spark Streaming jobb|5 jobb - var 3:e minut||
|Strukturerade streamingjobb|5 jobb - varje minut||
|ML Modell utbildning jobb|2 jobb - en gång i veckan||
|Programmeringsspråk|Python, Scala, Java||
|Skriptning|Skal, Python||
|**Ämne**: **Data**|||
|Datakällor|Platta filer, Json, Kafka, RDBMS||
|Data orkestrering|Oozie arbetsflöden, Airflow||
|I minnesuppslag|Apache Antända, Redis||
|Destinationer för data|HDFS, RDBMS, Kafka, MPP ||
|**Ämne**: **Metadata**|||
|Hive DB-typ|Mysql, Postgres||
|Antal Hive-metabutiker|2||
|Antal Tabeller för Hive|100||
|Antal rangerprinciper|20||
|Antal Oozie-arbetsflöden|100||
|**Ämne**: **Skala**|||
|Datavolym inklusive replikering|100 TB||
|Daglig intagsvolym|50 GB||
|Datatillväxt|10% per år||
|Tillväxthastighet för klusternoder|5% per år
|**Ämne**: **Klusterutnyttjande**|||
|Genomsnittlig CPU % som används|60 %||
|Genomsnittligt minne % använt|75 %||
|Diskutrymme som används|75 %||
|Genomsnittligt nätverk % använt|25 %
|**Ämne**: **Personal**|||
|Antal administratörer|2||
|Antal utvecklare|10||
|Antal slutanvändare|100||
|Färdigheter|Hadoop, Gnista||
|Antal tillgängliga resurser för migrationsinsatser|2||
|**Ämne**: **Begränsningar**|||
|Aktuella begränsningar|Latensen är hög||
|Aktuella utmaningar|Samtidighetsemission||

### <a name="azure-requirements-questionnaire"></a>Azure-kravfrågekomnad

|**Ämne**: **Infrastruktur** |||
|---|---|---|
|**Fråga**|**Exempel**|**Svar**|
| Önskad region|USA, Östra||
|VNet föredras?|Ja||
|HA / DR behövs?|Ja||
|Integration med andra molntjänster?|ADF, KosmosDB||
|**Ämne**: **Data Movement**  |||
|Inledande inläsningsinställning|DistCp, Data box, ADF, WANDisco||
|Delta för dataöverföring|DistCp, AzCopy||
|Pågående inkrementell dataöverföring|DistCp, Sqoop||
|**Ämne**: **Övervakning & avisering** |||
|Använd Azure Monitoring & avisering vs integrera övervakning från tredje part|Använda Azure Monitoring &-aviseringar||
|**Ämne**: **Säkerhetsinställningar** |||
|Privat och skyddad datapipeline?|Ja||
|Domänanst anslutna kluster (ESP)?|     Ja||
|Lokal AD-synkronisering till molnet?|     Ja||
|Antal AD-användare som ska synkroniseras?|          100||
|Ok att synkronisera lösenord till molnet?|    Ja||
|Endast molnanvändare?|                 Ja||
|MFA behövs?|                       Inga|| 
|Krav på dataauktorisering?|  Ja||
|Rollbaserad åtkomstkontroll?|        Ja||
|Behövs revision?|                  Ja||
|Datakryptering i vila?|          Ja||
|Datakryptering under överföring?|       Ja||
|**Ämne**: **Inställningar för omarkitektur** |||
|Ett kluster jämfört med specifika klustertyper|Specifika klustertyper||
|Colocated Lagring Vs Fjärrlagring?|Fjärrlagring||
|Mindre klusterstorlek när data lagras på distans?|Mindre klusterstorlek||
|Vill du använda flera mindre kluster i stället för ett enda stort kluster?|Använda flera mindre kluster||
|Använda en fjärrmetastore?|Ja||
|Vill du dela metabutiker mellan olika kluster?|Ja||
|Vill du förstöra arbetsbelastningar?|Ersätta Hive-jobb med Spark-jobb||
|Vill du använda ADF för dataorkestrering?|Inga||

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metodtips för arkitektur för lokal till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-architecture.md)
