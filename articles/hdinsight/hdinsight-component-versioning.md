---
title: Apache Hadoop-komponenter och -versioner – Azure HDInsight
description: Lär dig Apache Hadoop-komponenterna och versionerna i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2321918e9eae63a71d136753657bd7259862c2d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272661"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Vilka är Apache Hadoop-komponenterna och versionerna med HDInsight?

Lär dig mer om [Apache Hadoop-ekosystemkomponenterna](https://hadoop.apache.org/) och -versionerna i Microsoft Azure HDInsight samt Enterprise Security Package. Lär dig också hur du kontrollerar Hadoop-komponentversioner i HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop-komponenter finns med olika HDInsight-versioner

Azure HDInsight stöder flera Hadoop-klusterversioner som kan distribueras när som helst. Från och med den 4 april 2017 är standardklusterversionen som används av Azure HDInsight 3.6.

De komponentversioner som är associerade med HDInsight-klusterversioner visas i följande tabell:

> [!NOTE]  
> Standardversionen för HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett versionsberoende anger du HDInsight-versionen när du skapar dina kluster med .NET SDK med Azure PowerShell och Azure Classic CLI.

| Komponent              | HDInsight 4.0 | HDInsight 3.6 (standard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop och GARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache gris             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0 (kluster som inte är ESP), 1.2.1 (ESP-kluster)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (se anmärkning nedan) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> På grund av systemprestandaöverväganden upphörde stödet för Kafka version 0.10 ut i mars 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Sök efter aktuell versionsinformation för Hadoop-komponent

Hadoop-ekosystemkomponentversionerna som är associerade med HDInsight-klusterversioner kan ändras med uppdateringar av HDInsight. Om du vill kontrollera Hadoop-komponenterna och kontrollera vilka versioner som används för ett kluster använder du Ambari REST API. Kommandot **GetComponentInformation** hämtar information om tjänstkomponenter. Mer information finns i [Apache Ambari-dokumentationen](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Viktig information

Se [HDInsight-versionsanteckningar](hdinsight-release-notes.md) för ytterligare viktig information om de senaste versionerna av HDInsight.

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Support utgångsdatum och pensionering för HDInsight-versioner

**Support förfaller** innebär att Microsoft inte längre kommer att ge stöd för den angivna HDInsight-versionen och det kommer inte längre att vara tillgänglig via Azure-portalen för att skapa kluster. Dessa versioner kan dock fortfarande skapas med Hjälp av Azure CLI eller de olika SDK:erna. 

**Pensionering** av en HDInsight-version innebär att befintliga kluster fortsätter att köras som de är. Nya kluster av den här versionen kan dock inte skapas på något sätt (inklusive CLI och SDK: er). Andra kontrollplansfunktioner (till exempel manuell skalning och automatisk skalning) kanske inte heller fungerar efter att versionen har pensionerats. Support är inte tillgänglig för pensionerade versioner.

I följande tabeller visas versionerna av HDInsight. Supportens utgångsdatum och pensionsdatum tillhandahålls också när de är kända.

### <a name="available-versions"></a>Tillgängliga versioner

I följande tabell visas de versioner av HDInsight som är tillgängliga i Azure-portalen samt andra distributionsmetoder som PowerShell och .NET SDK.

| HDInsight-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum | Utgångsdatum för support | Pensioneringsdatum | Hög tillgänglighet |  Tillgänglighet i Azure-portalen |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |September 24, 2018 | | |Ja |Ja |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |den 4 april 2017 | den 31 december 2020 |den 31 december 2020 |Ja |Ja |

Spark 2.1, 2.2 & Kafka 1.0-supporten upphör att gälla den 30 juni 2020.

> [!NOTE]  
> När supporten för en version har upphört att gälla kanske den inte är tillgänglig via Microsoft Azure-portalen. Klusterversioner fortsätter dock att `Version` vara tillgängliga med parametern i kommandot Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) och .NET SDK fram till versionsrekreningsdatumet.

### <a name="retired-versions"></a>Pensionerade versioner

I följande tabell visas de versioner av HDInsight som **inte** är tillgängliga i Azure-portalen.

| HDInsight-version | HDP-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum | Utgångsdatum för support | Pensioneringsdatum | Hög tillgänglighet |  Tillgänglighet på Azure-portalen |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2.5 |Ubuntu 16.0.4 LTS |den 30 september 2016 |den 5 september 2017 |den 28 juni 2018 |Ja |Inga |
| HDInsight 3,4 |HDP 2.4 |Ubuntu 14.0.4 LTS |den 29 mars 2016 |den 29 december 2016 |9 januari 2018 |Ja |Inga |
| HDInsight 3,3 |HDP 2.3 |Windows Server 2012 R2 |den 2 december 2015 |den 27 juni 2016 |31 juli 2018 |Ja |Inga |
| HDInsight 3,3 |HDP 2.3 |Ubuntu 14.0.4 LTS |den 2 december 2015 |den 27 juni 2016 |den 31 juli 2017 |Ja |Inga |
| HDInsight 3,2 |HDP 2.2 |Ubuntu 12.04 LTS eller Windows Server 2012 R2 |den 18 februari 2015 |den 1 mars 2016 |1 april 2017 |Ja |Inga |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |den 24 juni 2014 |den 18 maj 2015 |den 30 juni 2016 |Ja |Inga |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |den 11 februari 2014 |den 17 september 2014 |30 juni 2015 |Ja |Inga |
| HDInsight 2,1 |HDP 1.3 |Windows Server 2012 R2 |den 28 oktober 2013 |den 12 maj 2014 |den 31 maj 2015 |Ja |Inga |
| HDInsight 1,6 |HDP 1.1 | |den 28 oktober 2013 |den 26 april 2014 |den 31 maj 2015 |Inga |Inga |

> [!NOTE]  
> Kluster med högtillgänge med två huvudnoder distribueras som standard för HDInsight version 2.1 och senare. De är inte tillgängliga för HDInsight version 1.6-kluster.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package för HDInsight

Enterprise Security är ett valfritt paket som du kan lägga till i HDInsight-klustret som en del av arbetsflödet för kluster. Enterprise Security Package stöder:

- Integrering med Active Directory för autentisering.

    Tidigare kan du bara skapa HDInsight-kluster med en lokal administratörsanvändare och en lokal SSH-användare. Den lokala administratörsanvändaren kan komma åt alla filer, mappar, tabeller och kolumner.  Med Enterprise Security Package kan du aktivera rollbaserad åtkomstkontroll genom att integrera HDInsight-kluster med din egen Active Directory, som innehåller lokala Active Directory, Azure Active Directory Domain Services eller Active Directory på IaaS virtuell dator. Domänadministratör i klustret kan ge användare att använda sitt eget användarnamn och lösenord för företag (domän) för att komma åt klustret.

    Mer information finns i:

    - [En introduktion till Apache Hadoop-säkerhet med domänanslutna HDInsight-kluster](./domain-joined/hdinsight-security-overview.md)
    - [Planera Azure-domänanstjada Apache Hadoop-kluster i HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurera en domänansluten testmiljö](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Auktorisering av data

  - Integrering med Apache Ranger för auktorisering för Hive-, Spark SQL- och Yarn-köer.
  - Du kan ställa in åtkomstkontroll för filer och mappar.

    Mer information finns i:

  - [Konfigurera Apache Hive-principer i domänanslutna HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- Visa granskningsloggarna för att övervaka åtkomster och konfigurerade principer.

### <a name="supported-cluster-types"></a>Klustertyper som stöds

För närvarande stöder endast följande klustertyper Enterprise Security Package:

- Hadoop (endast HDInsight 3.6)
- Spark
- Kafka
- HBase
- Interaktiv fråga

### <a name="support-for-azure-data-lake-storage"></a>Stöd för Azure Data Lake Storage

Enterprise Security Package stöder användning av Azure Data Lake Storage som både primär lagring och tilläggslagring.

### <a name="pricing-and-service-level-agreement"></a>Pris- och servicenivåavtal

Information om priser och serviceavtal för Enterprise Security Package finns i [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Servicenivåavtal för HDInsight-klusterversioner

Servicenivåavtalet (SLA) definieras i termer av ett _supportfönster_. Supportfönstret är den tidsperiod som en HDInsight-klusterversion stöds av Microsofts kundtjänst och support. Om versionen har ett _förfallodatum_ för support som har passerat ligger HDInsight-klustret utanför supportfönstret. Supportens utgångsdatum för en angiven HDInsight version X (efter att en nyare X+1-version är tillgänglig) beräknas som den senare av:  

- Formel 1: Lägg till 180 dagar till det datum då HDInsight-klusterversionen X släpptes.
- Formel 2: Lägg till 90 dagar till det datum då HDInsight-klusterversionen X+1 görs tillgänglig i Azure-portalen.

_Återskapningsdatumet_ är det datum efter vilket klusterversionen inte kan skapas på HDInsight. Från och med den 31 juli 2017 kan du inte ändra storlek på ett HDInsight-kluster efter dess pensioneringsdatum.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks viktig information i samband med HDInsight versioner

Avsnittet innehåller länkar till viktig information för Hortonworks Data Platform-distributioner och Apache-komponenter som används med HDInsight.
* HDInsight-klusterversion 4.0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* HDInsight-klusterversion 3.6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* HDInsight-klusterversion 3.5 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). HDInsight-klusterversion 3.5 är _det förvalta_ Hadoop-klustret som skapas i Azure-portalen.
* HDInsight-klusterversion 3.4 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-klusterversion 3.3 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Apache Storm-utgivningsanteckningar](https://storm.apache.org/2015/11/05/storm0100-released.html) finns på Apaches webbplats.
  * [Apache Hive-versionsanteckningar](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) finns på Apaches webbplats.
* HDInsight-klusterversion 3.2 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.2][hdp-2-2].

  * Viktig information för specifika Apache-komponenter finns på följande sätt: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [GARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Gemensam](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), och [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-klusterversion 3.1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. HDInsight 3.1 kluster som skapats före november, 7, 2014, är baserade på [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight-klusterversion 3.0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.0][hdp-2-0-8].
* HDInsight-klusterversion 2.1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1.3][hdp-1-3-0].
* HDInsight-klusterversion 1.6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standardnodkonfiguration och storlekar för virtuella datorer för kluster

Mer information om vilka SKU:er för virtuella datorer som ska väljas för klustret finns i [Azure HDInsight-klusterkonfigurationsinformation](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Nästa steg

- [Klusterinstallation för Apache Hadoop, Spark med mera på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
