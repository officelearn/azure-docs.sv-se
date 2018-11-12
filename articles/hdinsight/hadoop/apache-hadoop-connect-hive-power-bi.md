---
title: Visualisera big data med Power BI i Azure HDInsight
description: Lär dig hur du använder Microsoft Power BI för att visualisera Hive-data som bearbetas av Azure HDInsight.
keywords: hdinsight, hadoop, hive, interaktiv fråga, interactive hive, LLAP, odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: b094df367d4396533d368b9fdbcb4b78ec1673de
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008903"
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualisera Hive-data med Microsoft Power BI med hjälp av ODBC i Azure HDInsight

Lär dig hur du ansluter Microsoft Power BI till Azure HDInsight med hjälp av ODBC och visualisera Hive-data. 

>[!IMPORTANT]
> Du kan använda Hive ODBC-drivrutinen att importera via allmän ODBC-anslutningsprogram i Power BI Desktop. Men det rekommenderas inte för BI-arbetsbelastningar som icke-interaktiv naturen för motorn för Hive-frågor. [Interaktiv HDInsight-fråga connector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Spark-anslutningsappen](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) är bättre alternativ för deras prestanda.

I den här självstudien får du läsa in data från en hivesampletable Hive-tabell till Power BI. Hive-tabellen innehåller vissa mobiltelefon användningsdata. Sedan kan du rita användningsdata på en världskarta:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informationen gäller även för den nya [interaktiv fråga](../interactive-query/apache-interactive-query-get-started.md) typ av kluster. Att ansluta till HDInsight interaktiv fråga med direct query finns i [visualisera Interactive Query som Hive-data med Microsoft Power BI med direct query i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).



## <a name="prerequisites"></a>Förutsättningar
Innan du går igenom den här artikeln, måste du ha följande objekt:

* **HDInsight-kluster**. Klustret kan vara antingen ett HDInsight-kluster med Hive eller en nyligen utgiven klustret för interaktiv fråga. Skapa kluster finns i [Skapa kluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan ladda ned en kopia från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Se [skapa Hive ODBC-datakälla](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Läsa in data från HDInsight

Hive-tabell hivesampletable medföljer alla HDInsight-kluster.

1. Logga in på Power BI Desktop.
2. Klicka på den **Start** klickar du på **hämta Data** från den **externa data** menyfliksområdet och välj sedan **mer**.

    ![Öppna HDInsight Power BI-data](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Från den **hämta Data** fönstret klickar du på **andra** från vänster, klickar du på **ODBC** från höger, och klicka sedan på **Connect** längst ned.
4. Från den **från ODBC** väljer data source name som du skapade i det sista avsnittet och klicka sedan på **OK**.
5. Från den **Navigator** fönstret expanderar **ODBC -> HIVE -> standard**väljer **hivesampletable**, och klicka sedan på **belastningen**.

## <a name="visualize-data"></a>Visualisera data

Fortsätt från föregående procedur.

1. Från fönstret visualiseringar väljer **kartan**.  Det är en Globikon.

    ![HDInsight Power BI anpassar rapporten](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Från fönstret fält, Välj **land** och **devicemake**. Du kan se de data som visas på kartan.
3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att visualisera data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp Data till HDInsight](./../hdinsight-upload-data.md).
