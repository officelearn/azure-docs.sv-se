---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1ae4e2a1e0d67a0a09c19b517245ffc6d92d17df
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629928"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

Om du vill prenumerera på viktig information tittar du på versioner på [den här GitHub-lagringsplatsen](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11092020"></a>Utgivnings datum: 11/09/2020

Den här versionen gäller både HDInsight 3,6 och HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>HDInsight Identity Broker (HIB) är nu GA
HDInsight Identity Broker (HIB) som aktiverar OAuth-autentisering för ESP-kluster är nu allmänt tillgänglig i den här versionen. HIB-kluster som skapats efter den här versionen kommer att ha de senaste HIB-funktionerna:
- Hög tillgänglighet (HA)
- Stöd för Multi-Factor Authentication (MFA)
- Federerade användare logga in utan hash-synkronisering av lösen ord till AAD-DS mer information finns i [Hib-dokumentationen](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Från och med den här versionen migrerar tjänsten gradvis till [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Hela processen kan ta månader. När dina regioner och prenumerationer migreras, kommer nyligen skapade HDInsight-kluster att köras på virtuella datorers skalnings uppsättningar utan kund åtgärder. Ingen avbrytande ändring förväntas.

## <a name="deprecation"></a>Utfasning
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Utfasning av HDInsight 3,6 ML Services-kluster
HDInsight 3,6 ML-kluster typ upphör att fungera i december 31 2020. Kunder kan inte skapa nya 3,6 ML-tjänster i kluster efter 31 2020 december. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Kontrol lera att support upphör för HDInsight-versioner och kluster typer [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

### <a name="disabled-vm-sizes"></a>Inaktiverade VM-storlekar
Från och med november 16 2020 kommer HDInsight att blockera nya kunder som skapar kluster med hjälp av standand_A8, standand_A9 standand_A10 och standand_A11 VM-storlekar. Befintliga kunder som har använt de här VM-storlekarna under de senaste tre månaderna påverkas inte. Från och med den 9 2021 januari kommer HDInsight att blockera alla kunder som skapar kluster med hjälp av standand_A8, standand_A9 standand_A10 och standand_A11 VM-storlekar. Befintliga kluster kommer att köras som de är. Överväg att flytta till HDInsight 4,0 för att undvika eventuellt system-och support avbrott.

## <a name="behavior-changes"></a>Beteende ändringar
Ingen beteende ändring för den här versionen.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Möjlighet att välja olika Zookeeper-storlekar för virtuella datorer för Spark-, Hadoop-och ML-tjänster
HDInsight idag stöder inte anpassning av Zookeeper Node-storlek för Spark-, Hadoop-och ML-tjänster, kluster typer. Standardvärdet för A2_v2/a2 virtuella dator storlekar som tillhandahålls kostnads fritt. I den kommande versionen kan du välja en storlek på den virtuella Zookeeper-datorn som passar bäst för ditt scenario. Zookeeper-noder med en annan virtuell dator storlek än A2_v2/a2 kommer att debiteras. A2_v2-och a2-virtuella datorer tillhandahålls fortfarande utan kostnad.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Standard kluster versionen kommer att ändras till 4,0
Från och med 2021 februari kommer standard versionen av HDInsight-klustret att ändras från 3,6 till 4,0. Mer information om tillgängliga versioner finns i [tillgängliga versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions). Läs mer om vad som är nytt i [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-slut för support den 30 2021 juni
HDInsight 3,6 är slut på support. Från och med juni 30 2021 kan kunder inte skapa nya HDInsight 3,6-kluster. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till HDInsight 4,0 för att undvika eventuellt system-och support avbrott.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>Åtgärda problem med att starta om virtuella datorer i kluster
Problemet med att starta om virtuella datorer i klustret har åtgärd ATS, du kan använda [PowerShell eller REST API för att starta om noder i klustret](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm) igen.

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](./hdinsight-component-versioning.md).
