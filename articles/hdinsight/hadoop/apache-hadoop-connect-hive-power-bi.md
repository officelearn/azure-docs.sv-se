---
title: Visualisera stora data med Power BI i Azure HDInsight | Microsoft Docs
description: "Lär dig hur du använder Microsoft Power BI för att visualisera Hive-data som bearbetas av Azure HDInsight."
keywords: "hdinsight hadoop och hive, interaktiva fråga interaktiva hive, LLAP, odbc"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 390342eb08ae970fa760b414674b1a6783404d80
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Visualisera Hive-data med Microsoft Power BI i Azure HDInsight

Lär dig mer om att ansluta Microsoft Power BI till Azure HDInsight och visualisera data med Hive. Power BI stöder för närvarande endast ODBC-anslutning till HDInsight. I den här självstudiekursen läsa in data från en hivesampletable Hive-tabell till Power BI. Hive-tabellen innehåller några mobiltelefon användningsdata. Sedan kan du rita användningsdata i en värld av:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informationen gäller även till den nya [interaktiva frågan](../interactive-query/apache-interactive-query-get-started.md) kluster typen.

## <a name="prerequisites"></a>Krav
Innan du fortsätter med den här artikeln, måste du ha följande:

* **HDInsight-kluster**. Klustret kan vara ett HDInsight-kluster med Hive eller ett nyligen utgivna interaktiva fråga-kluster. För att skapa kluster, se [Skapa kluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan hämta en kopia från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Se [skapa Hive ODBC-datakällan](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Läs in data från HDInsight

Hivesampletable Hive-tabell kommer med alla HDInsight-kluster.

1. Logga in på Power BI Desktop.
2. Klicka på den **Start** klickar du på **hämta Data** från den **externa data** band och välj sedan **mer**.

    ![Öppna HDInsight Power BI-data](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Från den **hämta Data** rutan klickar du på **andra** från vänster, klickar du på **ODBC** från höger, och klicka sedan på **Anslut** längst ned.
4. Från den **från ODBC** panelen, väljer du datakälla namn som du skapade i det sista avsnittet, och klicka sedan på **OK**.
5. Från den **Navigator** rutan Expandera **ODBC -> HIVE -> standard**väljer **hivesampletable**, och klicka sedan på **belastningen**.

## <a name="visualize-date"></a>Visualisera datum

Fortsätt från föregående procedur.

1. Fönstret visualiseringar Välj **kartan**.  Det är en Globikon.

    ![HDInsight Power BI anpassar rapport](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Fönstret fält, Välj **land** och **devicemake**. Du kan se de data som visas på kartan.
3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du visualisera data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC-drivrutinen](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra Data till HDInsight](./../hdinsight-upload-data.md).
