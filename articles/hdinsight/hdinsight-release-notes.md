---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006542"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-07132020"></a>Utgivnings datum: 07/13/2020

Den här versionen gäller både för HDInsight 3,6 och 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Stöd för Customer Lockbox för Microsoft Azure
Azure HDInsight har nu stöd för Azure Customer Lockbox. Det ger ett gränssnitt för kunder att granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Den används när Microsoft-teknikern behöver åtkomst till kund information under en support förfrågan. Mer information finns i [Customer lockbox för Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Tjänst slut punkts principer för lagring
Kunder kan nu använda tjänst slut punkts principer (SEP) i HDInsight-klustrets undernät. Läs mer om [Azure-tjänstens slut punkts princip](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Utfasning
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Utfasning av Spark 2.1 och 2.2 i HDInsight 3.6 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,1 och 2,2 på HDInsight 3,6. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att gå vidare till Spark 2,3 i HDInsight 3,6 och 30 2020 för att undvika potentiell system/support-avbrott.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Utfasning av Spark 2.3 i HDInsight 4.0 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,3 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Spark 2.4 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Utfasning av Kafka 1.1 i HDInsight 4.0 Kafka-kluster
Från och med juli 1 2020 kommer kunderna inte att kunna skapa nya Kafka-kluster med Kafka 1,1 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Kafka 2.1 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.

## <a name="behavior-changes"></a>Beteende ändringar
Inga beteende ändringar du behöver åtgärda.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Möjlighet att välja olika Zookeeper SKU: er för Spark-, Hadoop-och ML-tjänster
HDInsight idag stöder inte ändring av Zookeeper SKU för kluster typerna Spark, Hadoop och ML. Den använder A2_v2/a2 SKU för Zookeeper-noder och kunder debiteras inte för dem. I den kommande versionen kommer kunderna att kunna ändra Zookeeper SKU för Spark-, Hadoop-och ML-tjänster vid behov. Zookeeper-noder med andra SKU än A2_v2/a2 kommer att debiteras. Standard-SKU: n är fortfarande A2_V2/a2 och kostnads fritt.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Problem med fast Hive-dist. lager koppling
Det uppstod ett problem för Hive-kopplingens användbarhet i den tidigare versionen. Problemet har åtgärd ATS. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Fast Zeppelin Notebook trunkerar problem med inledande nollor
Zeppelin trunkerades felaktigt inledande nollor i tabellens utdata för sträng format. Vi har åtgärdat det här problemet i den här versionen.

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
