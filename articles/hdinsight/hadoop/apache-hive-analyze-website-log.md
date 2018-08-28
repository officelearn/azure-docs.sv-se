---
title: Använda Hive med Hadoop för webbplatslogganalys - Azure HDInsight
description: Lär dig använda Hive med HDInsight för att analysera webbplatsloggar. Du använder en loggfil som indata till ett HDInsight-tabell och Använd HiveQL för att fråga efter data.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 47175ed4bc01a43d1f21cbfc84b8a788f11b7338
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048032"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Använda Hive med Windows-baserade HDInsight för att analysera loggar från webbplatser
Lär dig använda HiveQL med HDInsight för att analysera loggar från en webbplats. Webbplatslogganalys kan användas för att segmentera målgruppen utifrån liknande aktiviteter, kategorisera besökarna genom demografi och ta reda på innehållet de vy, de kommer från webbplatser och så vidare.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar bara med Windows-baserade HDInsight-kluster. HDInsight är endast tillgänglig på Windows för versioner lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

I det här exemplet använder du ett HDInsight-kluster för att analysera webbplatsloggfiler för att få förståelse för Besöksfrekvensen till webbplatsen från externa webbplatser under en dag. Du kan också skapa en sammanfattning av webbplatsfel som användarna upplever. Lär dig att:

* Anslut till Azure Blob storage, som innehåller webbplatsloggfiler.
* Skapa HIVE-tabeller för att fråga efter dessa loggar.
* Skapa HIVE-frågor för att analysera data.
* Använd Microsoft Excel för att ansluta till HDInsight (med hjälp av open database connectivity (ODBC) för att hämta data som analyseras.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Förutsättningar
* Du måste har etablerat ett Hadoop-kluster på Azure HDInsight. Anvisningar finns i [etablera HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).
* Du måste ha Microsoft Excel 2013 eller Excel 2010 installerat.
* Du måste ha [Microsoft Hive ODBC-drivrutin](http://www.microsoft.com/download/details.aspx?id=40886) att importera data från Hive till Excel.

## <a name="to-run-the-sample"></a>Att köra exemplet
1. Från den [Azure-portalen](https://portal.azure.com/), från startsidan (om du har fäst klustret det), klickar du på klusterikonen som du vill köra exemplet.
2. Från klusterbladet under **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och sedan från den **Klusterinstrumentpanel** bladet klickar du på **HDInsight-kluster Instrumentpanelen**. Alternativt kan du direkt öppna instrumentpanelen genom att använda följande URL:

         https://<clustername>.azurehdinsight.net

    När du uppmanas, autentisera genom att använda administratörens användarnamn och lösenord som du använde när du etablerar klustret.
3. På sidan som öppnas, klickar du på den **Getting Started galleriet** fliken och sedan under den **lösningar med exempeldata** kategori, klickar du på den **Webbplatslogganalys** exemplet.
4. Följ instruktionerna på sidan för att slutföra exemplet.

## <a name="next-steps"></a>Nästa steg
Prova följande exempel: [analysera sensordata med Hive med HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
