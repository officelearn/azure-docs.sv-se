---
title: Använda Hive med Hadoop för analys av webbplatsloggar - Azure HDInsight | Microsoft Docs
description: Lär dig använda Hive med HDInsight för att analysera webbplatsloggar. Du måste använda en loggfil som indata till ett HDInsight-tabell och använda HiveQL för att fråga efter data.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 53a0560d3bc5a52069d5829b9c3bd353e0c37ef3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398165"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Använda Hive med Windows-baserade HDInsight för att analysera loggar från webbplatser
Lär dig använda HiveQL med HDInsight för att analysera loggar från en webbplats. Webbplatslogganalys kan användas för att segmentera målgruppen utifrån liknande aktiviteter, kategorisera besökare av demografi och ta reda på vad de vy, de kommer från webbplatser och så vidare.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast med Windows-baserade HDInsight-kluster. HDInsight är endast tillgängligt i Windows för versioner som är lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

I det här exemplet använder du ett HDInsight-kluster för att analysera loggfiler för webbplatsen och få inblick i antalet besök till webbplatsen från externa webbplatser under en dag. Du kan också skapa en sammanfattning av de webbplatsfel som användarna råkat. Lär dig att:

* Ansluta till Azure Blob-lagring som innehåller loggfiler för webbplatsen.
* Skapa HIVE-tabeller och fråga efter dessa loggar.
* Skapa HIVE-frågor för att analysera data.
* Använda Microsoft Excel för att ansluta till HDInsight (med hjälp av öppna databasanslutning (ODBC) för att hämta analyserade data.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Förutsättningar
* Du måste har etablerat ett Hadoop-kluster i Azure HDInsight. Instruktioner finns i [etablera HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).
* Du måste ha Microsoft Excel 2013 eller Excel 2010 installerat.
* Du måste ha [Microsoft Hive ODBC-drivrutinen](http://www.microsoft.com/download/details.aspx?id=40886) att importera data från Hive till Excel.

## <a name="to-run-the-sample"></a>Att köra exemplet
1. Från den [Azure-portalen](https://portal.azure.com/), från startsidan (om du har Fäst det klustret), klickar på ikonen för kluster som du vill köra exemplet.
2. I klusterbladet under **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och sedan den **Klusterinstrumentpanel** bladet klickar du på **instrumentpanelen för HDInsight-klustret**. Alternativt kan du direkt öppna instrumentpanelen genom att använda följande URL:

         https://<clustername>.azurehdinsight.net

    När du uppmanas, autentisera med hjälp av administratörsanvändarnamn och lösenord som du använde vid etablering av klustret.
3. Från sidan som öppnas, klickar du på den **komma igång-galleriet** fliken och sedan under den **lösningar med exempeldata** kategori, klicka på den **Webbplatslogganalys** exempel.
4. Följ anvisningarna på sidan för att slutföra exemplet.

## <a name="next-steps"></a>Nästa steg
Försök i följande exempel: [analysera sensordata med Hive med HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
