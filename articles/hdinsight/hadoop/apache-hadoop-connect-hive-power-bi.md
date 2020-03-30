---
title: Visualisera Apache Hive-data med Power BI – Azure HDInsight
description: Lär dig hur du använder Microsoft Power BI för att visualisera Hive-data som bearbetas av Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370006"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualisera Apache Hive-data med Microsoft Power BI och ODBC i Azure HDInsight

Lär dig hur du ansluter Microsoft Power BI Desktop till Azure HDInsight med ODBC och visualiserar Apache Hive-data.

> [!IMPORTANT]
> Du kan använda Hive ODBC-drivrutinen för att importera via den generiska ODBC-kontakten i Power BI Desktop. Det rekommenderas dock inte för BI-arbetsbelastningar som inte är interaktiva för Hive-frågemotorn. [HDInsight Interactive Query-kontakt](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Spark-kontakt](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) är bättre val för deras prestanda.

I den här artikeln läser `hivesampletable` du in data från en Hive-tabell till Power BI. Hive-tabellen innehåller vissa användningsdata för mobiltelefoner. Sedan ritar du användningsdata på en världskarta:

![HDInsight Power BI kartrapporten](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informationen gäller även för den nya interaktiva frågeklustertypen. [Interactive Query](../interactive-query/apache-interactive-query-get-started.md) Information om hur du ansluter till HDInsight Interactive Query med direktfråga finns i [Visualisera interaktiva frågeinsdata med Microsoft Power BI med direktfrågan i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Krav

Innan du går igenom den här artikeln måste du ha följande objekt:

* HDInsight-kluster. Klustret kan vara antingen ett HDInsight-kluster med Hive eller ett nyutvecklat Interactive Query-kluster. Information om hur du skapar kluster finns i [Skapa kluster](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI-skrivbordet](https://powerbi.microsoft.com/desktop/). Du kan hämta en kopia från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Se [Skapa Hive ODBC-datakälla](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Läsa in data från HDInsight

**Hivesampletable** Hive-tabellen levereras med alla HDInsight-kluster.

1. Starta Power BI Desktop.

1. På den översta menyn navigerar du till **Hem** > **hämta data** > **mer...**.

    ![Öppna data för HDInsight Excel Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Välj **Annat** från vänster i dialogrutan **Hämta data,** välj **ODBC** till höger och välj sedan **Anslut** längst ned.

1. I dialogrutan **Från ODBC** väljer du det datakällnamn som du skapade i det sista avsnittet i listrutan. Välj sedan **OK**.

1. För första användningen öppnas en **ODBC-drivrutinsdialogruta.** Välj **Standard eller Anpassad** på den vänstra menyn. Välj sedan **Anslut** för att öppna **Navigator**.

1. Expandera **ODBC > HIVE > standard**i dialogrutan **Navigator** , välj **hivesampletable**och välj sedan **Läs in**.

## <a name="visualize-data"></a>Visualisera data

Fortsätt från den sista proceduren.

1. Välj **Karta**i fönstret Visualiseringar, det är en jordglobsikon.

    ![HDInsight Power BI anpassar rapporten](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. Välj **land** och **devicemake**i fönstret **Fält** . Du kan se data som ritas på kartan.

1. Expandera kartan.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att visualisera data från HDInsight med Power BI.  Mer information finns i följande artiklar:

* [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight Tool för Visual Studio-kod](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
