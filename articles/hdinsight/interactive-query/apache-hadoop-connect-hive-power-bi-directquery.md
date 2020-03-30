---
title: Se interaktiva frågeinsekedata med Power BI i Azure HDInsight
description: Använda Microsoft Power BI för att visualisera interaktiva frågeinsdata från Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368000"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualisera interaktiva Apache Hive-data med Microsoft Power BI med direktfrågan i HDInsight

I den här artikeln beskrivs hur du ansluter Microsoft Power BI till Azure HDInsight Interactive Query-kluster och visualiserar Apache Hive-data med hjälp av direktfråga. Exemplet som tillhandahålls läser `hivesampletable` in data från en Hive-tabell till Power BI. `hivesampletable` Hive-tabellen innehåller vissa användningsdata för mobiltelefoner. Sedan ritar du användningsdata på en världskarta:

![HDInsight Power BI kartrapporten](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Du kan använda [Apache Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-power-bi.md) för att importera via den generiska ODBC-kontakten i Power BI Desktop. Det rekommenderas dock inte för BI-arbetsbelastningar som inte är interaktiva för Hive-frågemotorn. [HDInsight Interactive Query-kontakt](./apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Apache Spark-kontakt](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) är bättre val för deras prestanda.

## <a name="prerequisites"></a>Krav
Innan du går igenom den här artikeln måste du ha följande objekt:

* **HDInsight-kluster**. Klustret kan antingen vara ett HDInsight-kluster med Apache Hive eller ett nyutvecklat Interactive Query-kluster. Information om hur du skapar kluster finns i [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI-skrivbordet](https://powerbi.microsoft.com/desktop/)**. Du kan hämta en kopia från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Läsa in data från HDInsight

`hivesampletable` Hive-tabellen levereras med alla HDInsight-kluster.

1. Starta Power BI Desktop.

2. Från menyraden navigerar du till **Hem** > **hämta data** > **mer...**.

    ![HDInsight Power BI få data mer](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Från fönstret **Hämta data** anger du **hdinsight** i sökrutan.  

4. Välj **HDInsight Interactive Query**i sökresultaten och välj sedan **Anslut**.  Om du inte ser **HDInsight Interactive Query**måste du uppdatera Power BI-skrivbordet till den senaste versionen.

5. Välj **Fortsätt** om du vill stänga **dialogrutan Ansluta till en tjänst från tredje part.**

6. I fönstret **HDInsight Interactive Query** anger du följande information och väljer sedan **OK:**

    |Egenskap | Värde |
    |---|---|
    |Server |Ange klusternamnet, till exempel *myiqcluster.azurehdinsight.net*.|
    |Databas |Ange **standard** för den här artikeln.|
    |Läge för dataanslutning |Välj **DirectQuery** för den här artikeln.|

    ![HDInsight interaktiv fråga Power BI DirectQuery ansluta](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Ange HTTP-autentiseringsuppgifterna och välj sedan **Anslut**. Standardanvändarnamnet är **admin**.

8. Välj **hivesampletale**i **fönstret Navigator** i den vänstra rutan .

9. Välj **Läs in** i huvudfönstret.

    ![HDInsight interaktiv fråga Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualisera data på en karta

Fortsätt från den sista proceduren.

1. Välj **Karta**i fönstret Visualiseringar . En allmän karta visas sedan i huvudfönstret.

    ![HDInsight Power BI anpassar rapporten](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Välj **land** och **devicemake**i fönstret Fält . En världskarta med datapunkterna visas i huvudfönstret efter en stund.

3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att visualisera data från HDInsight med Hjälp av Microsoft Power BI.  Mer information om datavisualisering finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI med ODBC i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight Tool för Visual Studio-kod](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
