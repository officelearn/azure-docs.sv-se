---
title: Visualisera stora data med Power BI i Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder Microsoft Power BI för att visualisera Hive-data som bearbetas av Azure HDInsight.
keywords: hdinsight hadoop och hive, interaktiva fråga interaktiva hive, LLAP, odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 6f4efdebd6fc2833747b4cb72496447e0be03a58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualisera data med Hive med Microsoft Power BI med ODBC i Azure HDInsight

Lär dig hur du ansluter Microsoft Power BI till Azure HDInsight med hjälp av ODBC och visualisera data med Hive. 

>[!IMPORTANT]
> Du kan utnyttja ODBC-drivrutinen att importera via de allmänna ODBC-kopplingen i Power BI Desktop. Men det rekommenderas inte för BI arbetsbelastningar eftersom icke-interaktiv frågemotorn Hive. [HDInsight interaktiva frågan connector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Spark connector](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) bättre val för deras prestanda.

I den här självstudiekursen läsa in data från en hivesampletable Hive-tabell till Power BI. Hive-tabellen innehåller några mobiltelefon användningsdata. Sedan kan du rita användningsdata i en värld av:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informationen gäller även till den nya [interaktiva frågan](../interactive-query/apache-interactive-query-get-started.md) kluster typen. Att ansluta till HDInsight interaktiva frågan med hjälp av direkt fråga, se [visualisera interaktiva frågan som Hive data med Microsoft Power BI med direktfrågan i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).



## <a name="prerequisites"></a>Förutsättningar
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

## <a name="visualize-data"></a>Visualisera data

Fortsätt från föregående procedur.

1. Fönstret visualiseringar Välj **kartan**.  Det är en Globikon.

    ![HDInsight Power BI anpassar rapport](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Fönstret fält, Välj **land** och **devicemake**. Du kan se de data som visas på kartan.
3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du visualisera data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC-drivrutinen](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra Data till HDInsight](./../hdinsight-upload-data.md).
