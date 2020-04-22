---
title: Stöd för programvara med öppen källkod i Azure HDInsight
description: Microsoft Azure ger en allmän supportnivå för tekniker med öppen källkod.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772176"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Stöd för programvara med öppen källkod i Azure HDInsight

Microsoft Azure HDInsight-tjänsten använder en miljö med tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän supportnivå för tekniker med öppen källkod. Mer information finns i avsnittet **Supportscope i** vanliga frågor om [Azure Support](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare stöd för inbyggda komponenter.

## <a name="components"></a>Komponenter

Två typer av komponenter med öppen källkod finns i HDInsight-tjänsten:

### <a name="built-in-components"></a>Inbyggda komponenter

Dessa komponenter är förinstallerade på HDInsight-kluster och tillhandahåller kärnfunktioner i klustret. Följande komponenter tillhör den här kategorin:

* [Apache Hadoop GARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resurshanteraren.
* Hive-frågespråket [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

En fullständig lista över klusterkomponenter finns i [Vilka är Apache Hadoop-komponenterna och versionerna tillgängliga med HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Anpassade komponenter

Som användare av klustret kan du installera eller använda alla komponenter som är tillgängliga i communityn eller som skapats av dig i din arbetsbelastning.

> [!WARNING]  
> Komponenter som medföljer HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som är relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimligt stöd för att hjälpa dig att felsöka problemet ytterligare. Microsoft Support kanske kan lösa problemet. Eller så kan de be dig att engagera tillgängliga kanaler för öppen källkod teknik där djup expertis för den tekniken finns. Många community-webbplatser kan användas. Exempel är [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Överflöd](https://stackoverflow.com).
>
> Apache projekt har också projektwebbplatser på [Apache webbplats](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Användning av komponenter

HDInsight-tjänsten innehåller flera sätt att använda anpassade komponenter. Samma supportnivå gäller, oavsett hur en komponent används eller installeras i klustret. I följande tabell beskrivs de vanligaste sätten att anpassade komponenter används i HDInsight-kluster:

|Användning |Beskrivning |
|---|---|
|Inlämning av jobb|Hadoop eller andra typer av jobb som kör eller använder anpassade komponenter kan skickas till klustret.|
|Anpassning av kluster|När klustret skapas kan du ange ytterligare inställningar och anpassade komponenter som är installerade på klusternoderna.|
|Exempel|För populära anpassade komponenter kan Microsoft och andra ge exempel på hur dessa komponenter kan användas på HDInsight-kluster. Dessa prover tillhandahålls utan stöd.|

## <a name="next-steps"></a>Nästa steg

* [Anpassa Azure HDInsight-kluster med hjälp av skriptåtgärder](./hdinsight-hadoop-customize-cluster-linux.md)
* [Utveckla skript åtgärd skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Hantera Python-miljön i Azure HDInsight på ett säkert sätt med skriptåtgärd](./spark/apache-spark-python-package-installation.md)
