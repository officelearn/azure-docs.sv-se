---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974574"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-10082020"></a>Utgivnings datum: 10/08/2020

Den här versionen gäller både HDInsight 3,6 och HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Privata HDInsight-kluster utan offentlig IP och privat länk (förhands granskning)
HDInsight har nu stöd för att skapa kluster utan offentlig IP och privat länk till kluster i för hands versionen. Kunder kan använda de nya avancerade nätverks inställningarna för att skapa ett fullständigt isolerat kluster utan offentlig IP och använda sina egna privata slut punkter för att få åtkomst till klustret. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Från och med den här versionen migrerar tjänsten gradvis till [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Hela processen kan ta månader. När dina regioner och prenumerationer migreras, kommer nyligen skapade HDInsight-kluster att köras på virtuella datorers skalnings uppsättningar utan kund åtgärder. Ingen avbrytande ändring förväntas.

## <a name="deprecation"></a>Utfasning
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Utfasning av HDInsight 3,6 ML Services-kluster
HDInsight 3,6 ML-kluster typ upphör att fungera med dec 31 2020. Kunder skapar inga nya 3,6 ML-kluster efter detta. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Kontrol lera att support upphör för HDInsight-versioner och kluster typer [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

## <a name="behavior-changes"></a>Beteende ändringar
Ingen beteende ändring för den här versionen.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Möjlighet att välja olika Zookeeper-storlekar för virtuella datorer för Spark-, Hadoop-och ML-tjänster
HDInsight idag stöder inte anpassning av Zookeeper Node-storlek för Spark-, Hadoop-och ML-tjänster, kluster typer. Standardvärdet för A2_v2/a2 virtuella dator storlekar som tillhandahålls kostnads fritt. I den kommande versionen kan du välja en storlek på den virtuella Zookeeper-datorn som passar bäst för ditt scenario. Zookeeper-noder med en annan virtuell dator storlek än A2_v2/a2 kommer att debiteras. A2_v2-och a2-virtuella datorer tillhandahålls fortfarande utan kostnad.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).