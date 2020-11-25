---
title: Stöd för program vara med öppen källkod i Azure HDInsight
description: Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fe9ccc50d6196997863fbf228fbb76913a0d0330
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013878"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Stöd för program vara med öppen källkod i Azure HDInsight

Microsoft Azure HDInsights tjänsten använder en miljö med tekniker med öppen källkod som är utformad runt Apache Hadoop. Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod. Mer information finns i avsnittet **support omfattning** i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare en nivå av stöd för inbyggda komponenter.

## <a name="components"></a>Komponenter

Det finns två typer av komponenter med öppen källkod i HDInsight-tjänsten:

### <a name="built-in-components"></a>Inbyggda komponenter

Dessa komponenter förinstalleras i HDInsight-kluster och tillhandahåller kärn funktioner i klustret. Följande komponenter tillhör den här kategorin:

* [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager.
* [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)för Hive-frågespråket.
* [Apache Mahout](https://mahout.apache.org/).

En fullständig lista över kluster komponenter finns i [vilka Apache Hadoop-komponenter och-versioner som är tillgängliga med HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Anpassade komponenter

Som användare av klustret kan du installera eller använda i din arbets belastning, vilken komponent som helst som är tillgänglig i communityn eller som du har skapat.

> [!WARNING]  
> Komponenter som ingår i HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet. Du kan också be dig att engagera tillgängliga kanaler för teknikerna med öppen källkod där djupgående expertis för tekniken hittas. Många Community-webbplatser kan användas. Exempel är [Microsoft Q&en fråge sida för HDInsight](/answers/topics/azure-hdinsight.html) och [Stack Overflow](https://stackoverflow.com).
>
> Apache-projekt har också projekt webbplatser på [Apache-webbplatsen](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Komponent användning

HDInsight-tjänsten tillhandahåller flera olika sätt att använda anpassade komponenter. Samma nivå av support gäller, oavsett hur en komponent används eller installeras i klustret. I följande tabell beskrivs de vanligaste sätten att använda anpassade komponenter i HDInsight-kluster:

|Användning |Description |
|---|---|
|Jobböverföring|Hadoop eller andra typer av jobb som kör eller använder anpassade komponenter kan skickas till klustret.|
|Kluster anpassning|När klustret skapas kan du ange ytterligare inställningar och anpassade komponenter som är installerade på klusternoderna.|
|Exempel|För populära anpassade komponenter kan Microsoft och andra tillhandahålla exempel på hur dessa komponenter kan användas i HDInsight-kluster. De här exemplen tillhandahålls utan support.|

## <a name="next-steps"></a>Nästa steg

* [Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder](./hdinsight-hadoop-customize-cluster-linux.md)
* [Utveckla skript åtgärds skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Hantera Python-miljön i Azure HDInsight på ett säkert sätt med skriptåtgärd](./spark/apache-spark-python-package-installation.md)