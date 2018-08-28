---
title: Analysera sensordata med Hive och Hadoop - Azure HDInsight
description: Lär dig hur du analyserar sensordata med hjälp av Frågekonsolen Hive med HDInsight (Hadoop) och sedan visualiserar dina data i Microsoft Excel med PowerView.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: f82bac1b478183cad41e1bb9f7dce3fed8b5417b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048906"
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analysera sensordata med Frågekonsolen Hive med Hadoop i HDInsight

Lär dig hur du analyserar sensordata med hjälp av Frågekonsolen Hive med HDInsight (Hadoop) och visualisera data i Microsoft Excel med hjälp av Power View.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar bara med Windows-baserade HDInsight-kluster. HDInsight är endast tillgänglig på Windows för versioner lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


I det här exemplet använder du Hive för att bearbeta historiska data och identifiera problem med uppvärmning och luftkonditionering system. Mer specifikt kan du identifiera system går inte att bibehålla en inställd temperatur på ett tillförlitligt sätt genom att utföra följande uppgifter:

* Skapa HIVE-tabeller för att köra frågor mot data som lagras i filer med kommaavgränsade värden (CSV).
* Skapa HIVE-frågor för att analysera data.
* Hämta analyserade data genom att använda Microsoft Excel för att ansluta till HDInsight.
* Använd Power View om du vill visualisera data.

![Ett diagram över lösningsarkitekturen](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett kluster i HDInsight (Hadoop): se [skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) information om hur du skapar ett kluster.
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel används för datavisualisering med [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC-drivrutin](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Att köra exemplet

1. Från din webbläsare, navigerar du till följande URL: 

         https://<clustername>.azurehdinsight.net

    Ersätt `<clustername>` med namnet på HDInsight-klustret.

    När du uppmanas, autentisera genom att använda administratörens användarnamn och lösenord som du använde när du etablerar den här kluster.

2. På sidan som öppnas, klickar du på den **Getting Started galleriet** fliken och sedan under den **lösningar med exempeldata** kategori, klickar du på den **Sensordataanalys** exemplet.

    ![Komma igång galleriet bilden](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Följ instruktionerna på sidan för att slutföra exemplet.
