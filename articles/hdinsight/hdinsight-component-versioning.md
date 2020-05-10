---
title: Apache Hadoop komponenter och versioner – Azure HDInsight
description: Lär dig Apache Hadoop-komponenter och-versioner i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 98bb643e1a780754fcb3b9f4eb91d8f04dc4e90b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996391"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Apache-komponenter och versioner som är tillgängliga för Azure HDInsight

Lär dig mer om komponenter och versioner i [Apache Hadoop](https://hadoop.apache.org/) -miljön i Microsoft Azure HDInsight och Enterprise Security Package. Lär dig också hur du kontrollerar Hadoop-komponentens versioner i HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Apache-komponenter som är tillgängliga med olika HDInsight-versioner

Azure HDInsight stöder flera Hadoop-kluster som kan distribueras när som helst. Den 4 april 2017 är den standard kluster version som används av Azure HDInsight 3,6.

Komponent versioner som är associerade med HDInsight-kluster versioner visas i följande tabell:

> [!NOTE]  
> Standard versionen för HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett versions beroende anger du HDInsight-versionen när du skapar klustren med .NET SDK med Azure PowerShell och klassisk Azure-CLI.

| Komponent              | HDInsight 4.0 | HDInsight 3,6 (standard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop och garn | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache gris             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 på en interaktiv ESP-fråga) |
| Apache Tez-Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (se anmärkning nedan) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> På grund av system prestanda överväganden har support för Kafka version 0,10 gått ut i mars 2019.

## <a name="check-for-current-apache-component-version-information"></a>Sök efter aktuell versions information för Apache-komponenten

Komponent versioner av Hadoop-miljön som är associerade med HDInsight-kluster versioner kan ändra med uppdateringar till HDInsight. Om du vill kontrol lera Hadoop-komponenterna och kontrol lera vilka versioner som används för ett kluster använder du Ambari-REST API. **GetComponentInformation** -kommandot hämtar information om tjänst komponenter. Mer information finns i [Apache Ambari-dokumentationen](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Viktig information

Mer information om de senaste versionerna av HDInsight finns i avsnittet om [versions information för HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Stöd för förfallo datum och pensionering för HDInsight-versioner

**Support upphör** ande innebär att Microsoft inte längre har stöd för den angivna HDInsight-versionen. Och den kommer inte längre att vara tillgänglig via Azure Portal för skapande av kluster. Dessa versioner kan dock fortfarande skapas med hjälp av Azure CLI eller de olika SDK: erna.

När en HDInsight-version dras **tillbaka** kommer befintliga kluster fortsätta att köras i befintligt skick. Nya kluster i den här versionen kan dock inte skapas på något sätt (inklusive CLI och SDK: er). Andra kontroll Plans funktioner (t. ex. manuell skalning och autoskalning) kanske inte heller fungerar när versionen upphör att fungera. Support är inte tillgängligt för invecklade versioner.

I följande tabeller visas versioner av HDInsight. Datumen för support och upphör ande anges också när de är kända.

### <a name="available-versions"></a>Tillgängliga versioner

I följande tabell visas de versioner av HDInsight som är tillgängliga i Azure Portal och andra distributions metoder som PowerShell och .NET SDK.

| HDInsight-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |  Tillgänglighet i Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4-LTS |September 24, 2018 | | |Ja |Ja |
| HDInsight 3,6 |Ubuntu 16.0.4-LTS |4 april 2017 | 31 december 2020 |31 december 2020 |Ja |Ja |

Spark 2,1, 2,2 & Kafka 1,0-support upphör att gälla den 30 juni 2020.

> [!NOTE]  
> När supporten för en version har gått ut kanske den inte är tillgänglig via Microsoft Azure-portalen. Kluster versioner fortsätter dock att vara tillgängliga med hjälp av `Version` parametern i Windows PowerShell [New-AzHDInsightCluster-](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) kommandot och .NET SDK fram till versionens indragnings datum.

### <a name="retired-versions"></a>Återkallade versioner

I följande tabell visas de versioner av HDInsight som **inte** är tillgängliga i Azure Portal.

| HDInsight-version | HDP-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |  Tillgänglighet på Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4-LTS |30 september 2016 |5 september 2017 |28 juni 2018 |Ja |Inga |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4-LTS |29 mars 2016 |29 december 2016 |9 januari 2018 |Ja |Inga |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Inga |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4-LTS |2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Inga |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS eller Windows Server 2012 R2 |18 februari 2015 |1 mars 2016 |1 april 2017 |Ja |Inga |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |24 juni 2014 |18 maj 2015 |30 juni 2016 |Ja |Inga |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |30 juni 2015 |Ja |Inga |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28 oktober 2013 |12 maj 2014 |31 maj 2015 |Ja |Inga |
| HDInsight 1,6 |HDP 1,1 | |28 oktober 2013 |26 april 2014 |31 maj 2015 |Inga |Inga |

> [!NOTE]  
> Kluster med hög tillgänglighet med två head-noder distribueras som standard för HDInsight version 2,1 och senare. De är inte tillgängliga för HDInsight version 1,6-kluster.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Service nivå avtal för HDInsight-kluster versioner

Service nivå avtalet (SLA) definieras som ett _support fönster_. Support fönstret är den tids period som en HDInsight-version stöds `Microsoft Customer Service and Support`av. Om versionen har ett _utgångs datum som_har gått ut är HDInsight-klustret utanför support fönstret. Stöd för förfallo datum för HDInsight version X (när en senare version av X + 1 är tillgänglig) är senare:  

- Formel 1: Lägg till 180 dagar till det datum då HDInsight-klustrets version X släpptes.
- Formel 2: Lägg till 90 dagar till det datum då HDInsight-klustrets version X + 1 görs tillgänglig i Azure Portal.

_Indragnings datumet_ är det datum då kluster versionen inte kan skapas i HDInsight. Från och med den 31 juli 2017 kan du inte ändra storlek på ett HDInsight-kluster efter indragnings datumet.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standardkonfiguration av noder och storlekar för virtuella datorer för kluster

Mer information om vilka virtuella datorer SKU: er som ska väljas för klustret finns i [konfigurations information för Azure HDInsight-kluster](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Nästa steg

- [Kluster konfiguration för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
- [Hortonworks versions anteckningar som är associerade med Azure HDInsight-versioner](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)