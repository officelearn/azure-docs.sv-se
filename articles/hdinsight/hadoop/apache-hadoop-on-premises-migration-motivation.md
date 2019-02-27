---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - motivation och fördelar
description: Lär dig motivation och förmåner för migrera lokala Hadoop-kluster till Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 73a2f0754cafaa5da09ebd437ecd62813296ffd9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890087"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - motivation och fördelar

Den här artikeln är den första i en serie på Metodtips för att migrera lokala distributioner av Apache Hadoop-ekosystemet till Azure HDInsight. Den här artikelserien är för personer som ansvarar för design, distributionen och migreringen av Apache Hadoop-lösningar i Azure HDInsight. De roller som kan ha nytta av de här artiklarna innehåller molnarkitekter, Hadoop-administratörer och DevOps-tekniker. Programutvecklare, dataingenjörer och datatekniker bör också dra nytta av förklaring av hur olika typer av kluster arbete i molnet.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Varför ska man migrera till Azure HDInsight

Azure HDInsight är en molndistribution av Hadoop-komponenterna från den [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. HDInsight omfattar de populäraste ramverken med öppen källkod som:

- Apache Hadoop
- Apache Spark
- Apache Hive med LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight fördelar jämfört med lokala Hadoop-

- **Låg kostnad** -kan minska kostnaderna genom att [Skapa kluster på begäran](../hdinsight-hadoop-create-linux-clusters-adf.md) och endast betala för det du använder. Fristående beräkning och lagring ger flexibilitet genom att hålla datavolymen oberoende av klusterstorleken.

- **Automatiserad klustergenerering** – automatisk klustergenerering kräver minimal installation och konfiguration. Automation kan användas för kluster på begäran.

- **Hanterade maskinvara och konfiguration** -behöver inte bekymra dig om fysisk maskinvara eller infrastruktur med ett HDInsight-kluster. Ange bara konfigurationen för klustret och Azure ställer den in.

- **Lätt att skala** -HDInsight kan du [skala](../hdinsight-administer-use-portal-linux.md) upp eller ned arbetsbelastningar. Azure hand tar om vidaredistribution av data och arbetsbelastningen ombalansering utan att avbryta jobb för databehandling.

- **Global tillgänglighet** -HDInsight är tillgängligt i fler [regioner](https://azure.microsoft.com/regions/services/) än alla andra erbjudande för stordataanalys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden.

- **Säker och kompatibel** -HDInsight kan du skydda företagets datatillgångar med [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [kryptering](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md), och integrering med [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight uppfyller också de populäraste bransch- och myndighetsstandarderna [efterlevnadsstandarder](https://azure.microsoft.com/overview/trusted-cloud).

- **Förenklad versionshantering** -Azure HDInsight hanterar versionen av Hadoop-ekosystemet komponenter och håller dem uppdaterade. Programuppdateringar är vanligtvis en komplicerad process för lokala distributioner.

- **Mindre kluster som optimerats för specifika arbetsbelastningar med färre beroenden mellan komponenter** – en typisk lokala Hadoop-installationen använder ett enda kluster som har många användningsområden. Med Azure HDInsight kan du skapa belastningsspecifikt kluster. Skapa kluster för specifika arbetsbelastningar tar bort komplexiteten i att underhålla ett enda kluster med växande komplexitet.

- **Produktivitet** – du kan använda olika verktyg för Hadoop och Spark i din utvecklingsmiljö du föredrar.

- **Utökningsbarhet med anpassade verktyg eller program från tredje part** -HDInsight-kluster kan utökas genom att installera komponenter och kan även integreras med andra lösningar för stordata med hjälp av [klick](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  distributioner från Azure Marketplace.

- **Enkel hantering, administration och övervakning** -Azure HDInsight kan integreras med [Azure Monitor loggar](../hdinsight-hadoop-oms-log-analytics-tutorial.md) som ett enda gränssnitt som du kan övervaka alla dina kluster.

- **Integrering med andra Azure-tjänster** -HDInsight kan enkelt integreras med andra populära Azure-tjänster, till exempel följande:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Självåterställning processer och komponenter** -HDInsight ständigt kontrollerar de infrastruktur och öppen källkod-komponenter med hjälp av en egen övervakade infrastrukturen. Den återställer automatiskt allvarliga fel, till exempel otillgänglig komponenter med öppen källkod och noder. Aviseringar har utlösts i Ambari om någon komponent OSS misslyckades.

Mer information finns i artikeln [vad är Azure HDInsight och Apache Hadoop-teknikstacken](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Planeringsprocess för migrering

Följande steg rekommenderas för att planera en migrering av lokala Hadoop-kluster till Azure HDInsight:

1. Förstå aktuella lokal distribution och topologier.
2. Förstå aktuella projektets omfattning, tidslinjer och team expertis.
3. Förstå Azure-kraven.
4. Bygga ut en detaljerad plan baserat på bästa praxis.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Samlar in information om att förbereda för migrering

Det här avsnittet innehåller mallen enkäter för att samla in viktig information om:

- Lokal distribution
- Projektinformation
- Krav för Azure

### <a name="on-premises-deployment-questionnaire"></a>En lokal distribution enkäten

| **Fråga** | **Exempel** | **Svar** |
|---|---|---|
|**avsnittet**: **Miljö**|||
|Typ av kluster-Distribution|Hortonworks, Cloudera, MapR| |
|Klusterversion för Distribution|HDP 2.6.5, CDH 5.7|
|Big Data-ekosystemet-komponenter|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Klustertyper|Hadoop, Spark, konfluenta Kafka, Storm, Solr|
|Antalet kluster|4|
|Antalet noder för Master|2|
|Antalet Arbetsnoder|100|
|Antal Kantnoder| 5|
|Totalt diskutrymme|100 TB|
|Master nodkonfiguration|m/y, cpu, disk, osv.|
|Konfiguration för noder|m/y, cpu, disk, osv.|
|Edge-noder konfiguration|m/y, cpu, disk, osv.|
|HDFS kryptering?|Ja|
|Hög tillgänglighet|HDFS hög tillgänglighet, Metaarkiv hög tillgänglighet|
|Katastrofåterställning / säkerhetskopiera|Backup-kluster?|  
|System som är beroende av kluster|SQL Server, Teradata, Power BI, MongoDB|
|Tredjeparts-integreringar|Tableau GridGain, Qubole, Informatica, Splunk|
|**avsnittet**: **Säkerhet**|||
|Perimetersäkerhet|Brandväggar|
|Kluster-autentisering och auktorisering|Active Directory, Ambari, Cloudera-hanteraren, ingen autentisering|
|HDFS-åtkomstkontroll|  Manuell, ssh användare|
|Hive-autentisering och auktorisering|Sentry, LDAP, AD med Kerberos, Ranger|
|Granskning|Ambari, Cloudera Navigator, Ranger|
|Övervakning|Grafit, insamlade, statsd, Telegraf, InfluxDB|
|Aviseringar|Kapacitor, Prometheus, Datadog|
|Data Retention duration| 3 år, 5 år|
|Klusteradministratörer|En administratör kan flera administratörer|

### <a name="project-details-questionnaire"></a>Projektet information enkäten

|**Fråga**|**Exempel**|**Svar**|
|---|---|---|
|**avsnittet**: **Arbetsbelastningar och frekvens**|||
|MapReduce-jobb|10 jobb – två gånger per dag||
|Hive-jobb|100 jobb – varje timme||
|Spark batch-jobb|50 jobb – var 15: e minut||
|Spark Streaming jobb|5 jobb – var 3: e minut||
|Strukturerad direktuppspelning jobb|5 jobb – varje minut||
|ML-modell upplärningsjobb|2 jobb – en gång under en vecka||
|Programmeringsspråk|Python, Scala, Java||
|Skript|Shell, Python||
|**avsnittet**: **Data**|||
|Datakällor|Flat files, Json, Kafka, RDBMS||
|Dataorkestrering|Oozie arbetsflöden, luftflöde||
|I minnet sökningar|Apache Ignite, Redis||
|Datamål|HDFS, RDBMS, Kafka, MPP ||
|**avsnittet**: **Metadata**|||
|Hive DB-typ|Mysql, Postgres||
|Nej. av metastores för Hive|2||
|Nej. Hive-tabeller|100||
|Nej. för Ranger-principer|20||
|Nej. för Oozie-arbetsflöden|100||
|**avsnittet**: **Skalning**|||
|Datavolym, inklusive replikering|100 TB||
|Daglig inmatning volym|50 GB||
|Tillväxttakt för data|10% per år||
|Tillväxttakt för kluster-noder|5% per år
|**avsnittet**: **Klusteranvändning**|||
|Den genomsnittliga CPU % använt|60 %||
|Genomsnittligt minne-% använt|75%||
|Använt diskutrymme|75%||
|Den genomsnittliga nätverk % använt|25 %
|**avsnittet**: **Staff**|||
|Nej. för administratörer|2||
|Nej. för utvecklare|10||
|Nej. för slutanvändare|100||
|Färdigheter|Hadoop, Spark||
|Nej. tillgängliga resurser för migrering arbete|2||
|**avsnittet**: **Begränsningar**|||
|Aktuella begränsningar|Svarstiden är hög||
|Aktuella utmaningar|Samtidighet problemet||

### <a name="azure-requirements-questionnaire"></a>Krav för Azure enkäten

|**avsnittet**: **Infrastruktur** |||
|---|---|---|
|**Fråga**|**Exempel**|**Svar**|
| Önskad region|USA, Östra||
|Virtuellt nätverk rekommenderas?|Ja||
|HA / DR behövs?|Ja||
|Integrering med andra molntjänster?|ADF, CosmosDB||
|**avsnittet**:   **Dataförflyttning**  |||
|Första inställningar|DistCp, Data går du till rutan ADF, WANDisco||
|Delta för överföring av data|DistCp, AzCopy||
|Pågående inkrementella dataöverföring|DistCp, Sqoop||
|**avsnittet**:   **Övervakning och avisering** |||
|Använda Azure övervakning och avisering Vs integrera övervakning från tredje part|Använd Azure övervakning och avisering||
|**avsnittet**:   **Skyddsinställningar** |||
|Privata och skyddade datapipeline?|Ja||
|Domänen anslutits kluster (ESP)?|     Ja||
|Den lokala AD Sync till molnet?|     Ja||
|Nej. AD-användare att synkronisera?|          100||
|OK för att synkronisera lösenord till molnet?|    Ja||
|Endast användare i molnet?|                 Ja||
|MFA krävs?|                       Nej|| 
|Krav för auktorisering av data?|  Ja||
|Rollbaserad åtkomstkontroll?|        Ja||
|Granskning behövs?|                  Ja||
|Datakryptering i vila?|          Ja||
|Datakryptering under överföring?|       Ja||
|**avsnittet**:   **Inställningar för RE-arkitektur** |||
|Kluster vs specifika klustertyper|Specifika klustertyper||
|Samordnade lagring jämfört med Fjärrlagring?|Fjärrlagring||
|Mindre klusterstorlek som data lagras via fjärranslutning?|Mindre klusterstorlek||
|Använda flera mindre kluster i stället för ett stort kluster?|Använda flera mindre kluster||
|Använda en fjärransluten metaarkiv?|Ja||
|Dela metastores mellan olika kluster?|Ja||
|Bryta arbetsbelastningar?|Ersätt Hive-jobb med Spark-jobb||
|Använda ADF för datadirigering?|Nej||
|HDInsight vs Hortonworks Data Platform på IaaS?|HDInsight||

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Arkitektur för bästa praxis för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-architecture.md)
