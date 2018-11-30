---
title: Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight
description: Använd Microsoft Power BI för att visualisera Interactive Query Hive data från Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 68f2314b995eb0f2f67307b44cbfd177f5c5a796
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309278"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualisera Interactive Query Apache Hive-data med Microsoft Power BI med direct query i Azure HDInsight

Den här artikeln beskriver hur du ansluter Microsoft Power BI till interaktiv fråga i Azure HDInsight-kluster och visualisera Apache Hive-data med direct query. Det exempel läser in data från en hivesampletable Hive-tabell till Power BI. Hive-tabell hivesampletable innehåller vissa mobiltelefon användningsdata. Sedan kan du rita användningsdata på en världskarta:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Du kan utnyttja den [Apache Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-power-bi.md) att importera via allmän ODBC-anslutningsprogram i Power BI Desktop. Men det rekommenderas inte för BI-arbetsbelastningar som icke-interaktiv naturen för motorn för Hive-frågor. [Interaktiv HDInsight-fråga connector](./apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Apache Spark-anslutningsappen](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) är bättre alternativ för deras prestanda.

## <a name="prerequisites"></a>Förutsättningar
Innan du går igenom den här artikeln, måste du ha följande objekt:

* **HDInsight-kluster**. Klustret kan vara antingen ett HDInsight-kluster med Apache Hive eller en nyligen utgiven klustret för interaktiv fråga. Skapa kluster finns i [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan ladda ned en kopia från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Läsa in data från HDInsight

Hive-tabell hivesampletable medföljer alla HDInsight-kluster.

1. Logga in på Power BI Desktop.

2. Klicka på den **Start** klickar du på **hämta Data** från den **externa data** menyfliksområdet och välj sedan **mer...** .

    ![Öppna HDInsight Power BI-data](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. Från den **hämta Data** rutan, skriver du in **hdinsight** i sökrutan. Om du inte ser **interaktiv HDInsight-fråga (Beta)**, måste du uppdatera din Power BI Desktop till den senaste versionen.

4. Välj **interaktiv HDInsight-fråga (Beta)**, och välj sedan **Connect**.

5. Välj **Fortsätt** att Stäng den **Förhandsgranska anslutningsapp** varningsdialogruta.

6. Från **interaktiv HDInsight-fråga**, Välj eller ange följande information:

    - **Server**: ange klusternamnet interaktiv fråga, till exempel *myiqcluster.azurehdinsight.net*.

    - **Databasen**: den här självstudien anger **standard**.
    
    - **Läge för dataanslutning**: den här självstudien väljer **DirectQuery**.

    ![Interaktiv HDInsight-fråga Power BI DirectQuery ansluter](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Klicka på **OK**.

8. Ange http-användarens autentiseringsuppgifter och klicka sedan på **OK**. Standardanvändarnamnet är **admin**

9. I den vänstra rutan, Välj **hivesampletale**, och klicka sedan på **belastningen**.

    ![HDInsight interaktiv fråga Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualisera data på en karta

Fortsätt från föregående procedur.

1. Från fönstret visualiseringar väljer **kartan**.  Det är en Globikon.

    ![HDInsight Power BI anpassar rapporten](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. Från fönstret fält, Välj **land** och **devicemake**. Du kan se de data som visas på kartan.

3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att visualisera data från HDInsight med hjälp av Power BI.  Mer information om datavisualisering finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI med hjälp av ODBC i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Apache Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp Data till HDInsight](./../hdinsight-upload-data.md).
