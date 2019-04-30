---
title: Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight
description: Använd Microsoft Power BI för att visualisera Interactive Query Hive data från Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 657f8df959ccda5d51748ef5fbfc2e280f7d2c2e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126587"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualisera Interactive Query Apache Hive-data med Microsoft Power BI med direct query i Azure HDInsight

Den här artikeln beskriver hur du ansluter Microsoft Power BI till interaktiv fråga i Azure HDInsight-kluster och visualisera Apache Hive-data med direct query. Det exempel läser in data från en `hivesampletable` Hive-tabell till Power BI. Den `hivesampletable` Hive-tabell som innehåller vissa mobiltelefon användningsdata. Sedan kan du rita användningsdata på en världskarta:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Du kan utnyttja den [Apache Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-power-bi.md) att importera via allmän ODBC-anslutningsprogram i Power BI Desktop. Men det rekommenderas inte för BI-arbetsbelastningar som icke-interaktiv naturen för motorn för Hive-frågor. [Interaktiv HDInsight-fråga connector](./apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Apache Spark-anslutningsappen](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) är bättre alternativ för deras prestanda.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du går igenom den här artikeln, måste du ha följande objekt:

* **HDInsight-kluster**. Klustret kan vara antingen ett HDInsight-kluster med Apache Hive eller en nyligen utgiven klustret för interaktiv fråga. Skapa kluster finns i [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan ladda ned en kopia från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Läsa in data från HDInsight

Den `hivesampletable` Hive-tabell som medföljer alla HDInsight-kluster.

1. Starta Power BI Desktop.

2. Från menyraden navigerar du till **Start** > **hämta Data** > **mer...** .

    ![Öppna HDInsight Power BI-data](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Från den **hämta Data** fönstret, ange **hdinsight** i sökrutan.  

4. I sökresultaten väljer **interaktiv HDInsight-fråga**, och välj sedan **Connect**.  Om du inte ser **interaktiv HDInsight-fråga**, måste du uppdatera din Power BI Desktop till den senaste versionen.

5. Välj **Fortsätt** att Stäng den **ansluter till en tjänst från tredje part** dialogrutan.

6. I den **interaktiv HDInsight-fråga** fönstret anger du följande information och välj sedan **OK**:

    |Egenskap  | Värde |
    |---|---|
    |Server |Ange klustrets namn, till exempel *myiqcluster.azurehdinsight.net*.|
    |Databas |Ange **standard** för den här artikeln.|
    |Läge för dataanslutning |Välj **DirectQuery** för den här artikeln.|

    ![Interaktiv HDInsight-fråga Power BI DirectQuery ansluter](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Ange HTTP-autentiseringsuppgifterna och välj sedan **Connect**. Standardanvändarnamnet är **admin**.

8. Från den **Navigator** fönstret i den vänstra rutan, väljer **hivesampletale**.

9. Välj **belastningen** i huvudfönstret.

    ![HDInsight interaktiv fråga Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualisera data på en karta

Fortsätt från föregående procedur.

1. Från fönstret visualiseringar väljer **kartan**, ikonen. En allmän karta visas sedan i huvudfönstret.

    ![HDInsight Power BI anpassar rapporten](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Från fönstret fält, Välj **land** och **devicemake**. En världskarta med datapunkter visas i fönstret efter en liten stund.

3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att visualisera data från HDInsight med hjälp av Microsoft Power BI.  Mer information om datavisualisering finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI med hjälp av ODBC i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Apache Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp Data till HDInsight](./../hdinsight-upload-data.md).
