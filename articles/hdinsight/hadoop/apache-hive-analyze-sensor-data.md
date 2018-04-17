---
title: Analysera sensordata med Hive och Hadoop - Azure HDInsight | Microsoft Docs
description: Lär dig att analysera sensordata med hjälp av konsolen Hive-fråga med HDInsight (Hadoop) och visualisera data i Microsoft Excel med PowerView.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb3dc93d7cb741a8a3099abe13d00f40c9639705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analysera sensordata med Hive-fråga konsolen på Hadoop i HDInsight

Lär dig att analysera sensordata med hjälp av konsolen Hive-fråga med HDInsight (Hadoop) och visualisera data i Microsoft Excel med hjälp av Power View.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast med Windows-baserade HDInsight-kluster. HDInsight är endast tillgängligt i Windows för versioner som är lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


I det här exemplet använder du Hive för att bearbeta historiska data och identifiera problem med uppvärmning och luftkonditionering system. Mer specifikt kan du identifiera datorer går inte att bibehålla en inställd temperatur på ett tillförlitligt sätt genom att utföra följande uppgifter:

* Skapa HIVE-tabeller för att fråga efter data lagras i en fil med kommaavgränsade värden (CSV) filer.
* Skapa HIVE-frågor för att analysera data.
* Använd Microsoft Excel för att hämta analyserade data, för att ansluta till HDInsight.
* Använd Power View om du vill visualisera data.

![Ett diagram över lösningsarkitekturen](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett kluster i HDInsight (Hadoop): se [skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) information om hur du skapar ett kluster.
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel används för datavisualisering med [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC-drivrutinen](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Att köra exemplet

1. Från din webbläsare, navigerar du till följande URL: 

         https://<clustername>.azurehdinsight.net

    Ersätt `<clustername>` med namnet på HDInsight-klustret.

    När du uppmanas, autentisera med hjälp av administratörsanvändarnamn och lösenord som du använde vid etablering av det här klustret.

2. Från sidan som öppnas, klickar du på den **komma igång-galleriet** fliken och sedan under den **lösningar med exempeldata** kategori, klicka på den **Sensor dataanalys** exempel.

    ![Komma igång galleriet bild](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Följ anvisningarna på sidan för att slutföra exemplet.
