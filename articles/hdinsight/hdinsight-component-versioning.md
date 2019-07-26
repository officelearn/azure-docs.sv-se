---
title: Apache Hadoop komponenter och versioner – Azure HDInsight
description: Lär dig Apache Hadoop-komponenter och-versioner i HDInsight och tjänste nivåer som är tillgängliga i den här moln distributionen av Hortonworks Data Platform.
keywords: Hadoop-versioner, Hadoop eko system komponenter, Hadoop-komponenter, så här kontrollerar du Hadoop-version
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: a9de7c75ef2bd29b2e401ba387ca16a5dfda34fb
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442036"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Vilka Apache Hadoops komponenter och versioner är tillgängliga med HDInsight?

Lär dig mer om komponenter och versioner av [Apache Hadoop](https://hadoop.apache.org/) eko system i Microsoft Azure HDInsight, samt Enterprise Security Package. Lär dig också hur du kontrollerar Hadoop-komponentens versioner i HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoops komponenter som är tillgängliga med olika HDInsight-versioner

Azure HDInsight stöder flera Hadoop-kluster som kan distribueras när som helst. Varje versions val skapar en speciell version av HDP-distributionen och en uppsättning komponenter som ingår i distributionen. Från och med den 4 april 2017 är standard kluster versionen som används av Azure HDInsight 3,6 och baseras på HDP 2,6.

Komponent versioner som är associerade med HDInsight-kluster versioner visas i följande tabell: 

> [!NOTE]  
> Standard versionen för HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett versions beroende anger du HDInsight-versionen när du skapar klustren med .NET SDK med Azure PowerShell och klassisk Azure-CLI.

| Komponent | HDInsight 4.0 | HDInsight 3,6 (standard) | HDInsight 3,5 | HDInsight 3,4 | HDInsight 3,3 | HDInsight 3,2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Hortonworks Data Platform | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop och garn | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive och HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez-Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.1, 2,4 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2,0 | 1.6.0 | 1.5.2 | 1.3.1 (endast Windows) |
| Apache Livy | 0,5 | 0,4, 0,4, 0,3 | 0,3 | 0,3 | 0.2 | - |
| Apache Kafka | 1.1.1, 2,1 | 1,1, 1,0 * (se anmärkning nedan) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Vitt | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> På grund av system prestanda överväganden har support för Kafka version 0,10 gått ut i mars 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Kontrol lera den aktuella versions informationen för Hadoop-komponenten

De Hadoop-eko systemets komponent versioner som är associerade med HDInsight-kluster versioner kan ändras med uppdateringar till HDInsight. Om du vill kontrol lera Hadoop-komponenterna och kontrol lera vilka versioner som används för ett kluster använder du Ambari-REST API. **GetComponentInformation** -kommandot hämtar information om tjänst komponenter. Mer information finns i [Apache Ambari-dokumentationen](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Viktig information

Mer information om de senaste versionerna av HDInsight finns i avsnittet om [versions information för HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds

I följande tabeller visas versioner av HDInsight. De HDP-versioner som motsvarar varje HDInsight-version visas tillsammans med produkt lanserings datum. Datumen för support och upphör ande anges också när de är kända.

### <a name="available-versions"></a>Tillgängliga versioner

I följande tabell visas de versioner av HDInsight som är tillgängliga i Azure Portal samt andra distributions metoder som PowerShell och .NET SDK.

| HDInsight-version | HDP-version | VM-OPERATIVSYSTEM | Utgivningsdatum | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |  Tillgänglighet på Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4-LTS |24 september 2018 | | |Ja |Ja |
| HDInsight 3,6 |HDP 2,6 |Ubuntu 16.0.4-LTS |4 april 2017 | 30 juni 2020 |31 december 2020 |Ja |Ja |


> [!NOTE]  
> När supporten för en version har gått ut kanske den inte är tillgänglig via Microsoft Azure-portalen. Kluster versioner fortsätter dock att vara tillgängliga med hjälp `Version` av parametern i Windows PowerShell [New-AzHDInsightCluster-](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) kommandot och .NET SDK fram till versionens indragnings datum.
>

### <a name="retired-versions"></a>Återkallade versioner

I följande tabell visas de versioner av HDInsight som **inte** är tillgängliga i Azure Portal.

| HDInsight-version | HDP-version | VM-OPERATIVSYSTEM | Utgivningsdatum | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |  Tillgänglighet på Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 <br> (Inte Spark) |HDP 2.5 |Ubuntu 16.0.4-LTS |30 september 2016 |5 september 2017 |28 juni 2018 |Ja |Nej |
| HDInsight 3,4 |HDP 2.4 |Ubuntu 14.0.4-LTS |Den 29 mars 2016 |29 december 2016 |9 januari 2018 |Ja |Nej |
| HDInsight 3,3 |HDP 2.3 |Windows Server 2012 R2 |2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Nej |
| HDInsight 3,3 |HDP 2.3 |Ubuntu 14.0.4-LTS |2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Nej |
| HDInsight 3,2 |HDP 2.2 |Ubuntu 12,04 LTS eller Windows Server 2012 R2 |18 februari 2015 |1 mars 2016 |Den 1 april 2017 |Ja |Nej |
| HDInsight 3,1 |HDP 2.1 |Windows Server 2012 R2 |24 juni 2014 |18 maj 2015 |Den 30 juni 2016 |Ja |Nej |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |30 juni 2015 |Ja |Nej |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28 oktober 2013 |12 maj 2014 |31 maj 2015 |Ja |Nej |
| HDInsight 1,6 |HDP 1.1 | |28 oktober 2013 |26 april 2014 |31 maj 2015 |Nej |Nej |

> [!NOTE]  
> Kluster med hög tillgänglighet med två head-noder distribueras som standard för HDInsight version 2,1 och senare. De är inte tillgängliga för HDInsight version 1,6-kluster.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package för HDInsight

Enterprise Security är ett valfritt paket som du kan lägga till i ditt HDInsight-kluster som en del av arbets flödet skapa kluster. Enterprise Security Package stöder:

- Integrering med Active Directory för autentisering.

    Tidigare kan du bara skapa HDInsight-kluster med en lokal administratörs användare och en lokal SSH-användare. Den lokala administratörs användaren har åtkomst till alla filer, mappar, tabeller och kolumner.  Med Enterprise Security Package kan du aktivera rollbaserad åtkomst kontroll genom att integrera HDInsight-kluster med dina egna Active Directory, inklusive lokala Active Directory, Azure Active Directory Domain Services eller Active Directory på IaaS virtuell dator. Domän administratören i klustret kan ge användarna behörighet att använda sina egna företags användar namn och lösen ord för att komma åt klustret. 

    Mer information finns i:

    - [En introduktion till Apache Hadoop säkerhet med domänanslutna HDInsight-kluster](./domain-joined/hdinsight-security-overview.md)
    - [Planera Azure-domänanslutna Apache Hadoop kluster i HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurera domänanslutna sand Box miljö](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurera domänanslutna HDInsight-kluster med hjälp av Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Auktorisering för data

  - Integrering med Apache Ranger för auktorisering för Hive-, Spark SQL-och garn köer.
  - Du kan ange åtkomst kontroll för filer och mappar.

    Mer information finns i:

  - [Konfigurera Apache Hive principer i domänanslutna HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- Visa gransknings loggarna för att övervaka åtkomst och de konfigurerade principerna. 

### <a name="supported-cluster-types"></a>Kluster typer som stöds

För närvarande stöder endast följande kluster typer Enterprise Security Package:

- Hadoop (endast HDInsight 3,6)
- Spark
- Interaktiv fråga

### <a name="support-for-azure-data-lake-storage"></a>Stöd för Azure Data Lake Storage

Enterprise Security Package har stöd för att använda Azure Data Lake Storage som både den primära lagringen och lagringen för tillägg.

### <a name="pricing-and-service-level-agreement"></a>Pris-och service nivå avtal

Information om priser och service avtal för Enterprise Security Package finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Service nivå avtal för HDInsight-kluster versioner

Service nivå avtalet (SLA) definieras i ett _support fönster_. Support fönstret är den tids period som en HDInsight-kluster version stöds av Microsofts kund tjänst och support. Om versionen har ett _stöd för förfallo datum_ som har passerat, är HDInsight-klustret utanför support fönstret. Supportens förfallo datum för en angiven HDInsight-version X (efter att en nyare X + 1-version är tillgänglig) beräknas som senare av:  

* Formel 1: Lägg till 180 dagar till det datum då HDInsight-klustrets version X släpptes.
* Formel 2: Lägg till 90 dagar till det datum då HDInsight-klustrets version X + 1 görs tillgänglig i Azure Portal.

_Datumet_ för indragningen är det datum då kluster versionen inte kan skapas i HDInsight. Från och med den 31 juli 2017 kan du inte ändra storlek på ett HDInsight-kluster efter indragnings datumet. 

> [!NOTE]  
> HDInsight Windows-kluster (inklusive version 2,1, 3,0, 3,1, 3,2 och 3,3) körs på Azure gäst operativ system familj version 4, som använder 64-bitars versionen av Windows Server 2012 R2. Azure gäst operativ system familj version 4 stöder .NET Framework versionerna 4,0, 4,5, 4.5.1 och 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks viktig information som är associerad med HDInsight-versioner

Avsnittet innehåller länkar till viktig information för Hortonworks Data Platform-distributioner och Apache-komponenter som används med HDInsight.
* HDInsight-kluster version 4,0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* HDInsight-kluster version 3,6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* HDInsight-kluster version 3,5 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). HDInsight-kluster version 3,5 är det _standard_ -Hadoop-kluster som skapas i Azure Portal.
* HDInsight-kluster version 3,4 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-kluster version 3,3 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Apache Storm viktig information](https://storm.apache.org/2015/11/05/storm0100-released.html) finns på webbplatsen Apache.
  * [Apache Hive viktig information](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) finns på webbplatsen Apache.
* HDInsight-kluster version 3,2 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,2][hdp-2-2].

  * Viktig information för vissa Apache-komponenter finns på följande sätt: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [gris 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [garn 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)och [Oozie 4.1.0 ](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-kluster version 3,1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. HDInsight 3.1 clusters created before November, 7, 2014, are based on [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight-kluster version 3,0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2,0][hdp-2-0-8].
* HDInsight-kluster version 2,1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1,3][hdp-1-3-0].
* HDInsight-kluster version 1,6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standardkonfiguration av noder och storlekar för virtuella datorer för kluster

I följande tabeller visas standard storlekarna för virtuella datorer för HDInsight-kluster.  Det här diagrammet är nödvändigt för att förstå de VM-storlekar som ska användas när du skapar PowerShell-eller Azure CLI-skript för att distribuera HDInsight-kluster.

> [!IMPORTANT]  
> Om du behöver fler än 32 arbetsnoder i ett kluster måste du välja en huvudnods storlek med minst 8 kärnor och 14 GB RAM.

* Alla regioner som stöds förutom Brasilien, södra och Japan, väst:

|Klustertyp|Hadoop|HBase|Interaktiv fråga|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Huvud: storlek på virtuell dator|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|Head: rekommenderade storlekar för virtuella datorer|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Anställd: standard storlek för virtuell dator|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 med 2 S30-diskar per Broker|
|Arbetare: Rekommenderad VM-storlek|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper: standard storlek för virtuell dator||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper: rekommenderade storlekar för virtuella datorer||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|ML-tjänster: standard storlek för virtuell dator||||||D4 v2||
|ML-tjänster: Rekommenderad VM-storlek||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Södra Brasilien och västra Japan (inga v2-storlekar):

  | Klustertyp | Hadoop | HBase | Interaktiv fråga |Storm | Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Huvud: storlek på virtuell dator |D12 |D12  | D13 |A3 |D12 |D12 |
  | Head: rekommenderade storlekar för virtuella datorer |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |A3,<br/> A4,<br/> A5 |D12,<br/> D13,<br/> D14 |D12,<br/> D13,<br/> D14 |
  | Anställd: standard storlek för virtuell dator |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Arbetare: Rekommenderad VM-storlek |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12,<br/> D13,<br/> D14 | D4,<br/> D12,<br/> D13,<br/> D14 |
  | ZooKeeper: standard storlek för virtuell dator | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper: rekommenderade storlekar för virtuella datorer | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | ML-tjänster: standard storlekar för virtuella datorer | | | | | |D4 |
  | ML-tjänster: rekommenderade storlekar för virtuella datorer | | | | | |D4,<br/> D12,<br/> D13,<br/> D14 |

> [!NOTE]
> - Head är känt som *Nimbus* för Storm-kluster typen.
> - Arbetaren är känd som *administratör* för Storm-klustret.
> - Worker är känt som *region* för kluster typen HBase.

## <a name="next-steps"></a>Nästa steg
- [Kluster konfiguration för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
