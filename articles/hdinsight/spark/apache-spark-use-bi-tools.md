---
title: "Spark BI med hjälp av verktyg för visualisering av data på Azure HDInsight | Microsoft Docs"
description: "Använd verktyg för visualisering av data för analytics med hjälp av BI för Apache Spark i HDInsight-kluster"
keywords: "Apache spark bi, spark bi, spark datavisualisering Väck affärsinformation"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 97305ec6774e89e776653adbcdcf86b1cd63642f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI med hjälp av verktyg för visualisering av data med Azure HDInsight

Lär dig hur du använder [Microsoft Power BI](http://powerbi.microsoft.com) visualisera data i Apache Spark-kluster i Azure HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* **Slutföra artikeln [köra interaktiva frågor i Spark-kluster i HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) och [Power BI-utvärderingsprenumeration](https://app.powerbi.com/signupredirect?pbi_source=web) (valfritt).


## <a name="hivetable"></a>Kontrollera data

Jupyter-anteckningsbok som du skapade i den [tidigare kursen](apache-spark-load-data-run-query.md) innehåller koden för att skapa en `hvac` tabell. Den här tabellen baserat på CSV-filen tillgänglig på alla HDInsight Spark-kluster på **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Använd följande procedur för att verifiera data.

1. Klistra in följande kod från Jupyter-anteckningsbok och tryck sedan på **SKIFT + RETUR**. Koden verifierar att förekomsten av tabellerna.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Det ser ut som utdata:

    ![Visa tabellerna i Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Om du har stängt den bärbara datorn innan du påbörjar den här självstudiekursen `hvactemptable` rensas, så inte ingår i utdata.
    Endast Hive-tabeller som lagras i metastore (anges av **FALSKT** under den **isTemporary** kolumn) kan nås från BI-verktyg. I den här självstudiekursen kommer du ansluta till den **hvac** tabellen som du skapade.

2. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT + RETUR**. Koden verifierar data i tabellen.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Det ser ut som utdata:

    ![Visa rader från hvac-tabellen i Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Öppna menyn **Arkiv** i anteckningsboken och klicka på **Stäng och stoppa**. Stänga ned anteckningsboken för att frigöra resurser. 















## <a name="powerbi"></a>Använda Powerbi

I det här avsnittet använda Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata. 

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, Läs in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]
> Kopplingen visas i den här artikeln är för närvarande under förhandsgranskning. Ge feedback du ha via den [Power BI-communityn](https://community.powerbi.com/) plats eller [Power BI idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Öppna [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Från den **Start** klickar du på **hämta Data**, sedan **mer**.

    ![Hämta data till Power BI Desktop från Apache Spark i HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "hämta data till Power BI från Apache Spark BI")


2. Ange `Spark` i sökrutan väljer **Azure HDInsight Spark (Beta)**, och klicka sedan på **Anslut**.

    ![Hämta data till Power BI från Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "hämta data till Power BI från Apache Spark BI")

3. Ange kluster-URL (i formatet `mysparkcluster.azurehdinsight.net`), Välj **DirectQuery**, och klicka sedan på **OK**.

    Du kan använda antingen data anslutningsläget med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att uppdatera hela datamängden. Om du importerar data måste du uppdatera datauppsättningen kan se ändringar. Mer information om hur och när du ska använda DirectQuery finns [med DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Ange konto för HDInsight inloggningsinformation och klicka sedan på **Anslut**. Konto för standardnamnet är *admin*.

5. Välj den `hvac` tabell väntetiden för att visa en förhandsgranskning av data och klicka sedan på **belastningen**.

    ![Väck klustret användarnamn och lösenord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Väck klustret användarnamn och lösenord")

    Power BI Desktop har information som behövs för att ansluta till Spark-klustret och Läs in data från den `hvac` tabell. Tabellen och dess kolumner visas i den **fält** fönstret.  Se följande skärmbild:

6. Visualisera variansen mellan target temperatur- och faktiska temperatur för skapande av varje: 

    1. I den **VISUALISERINGAR** väljer **ytdiagram**. 
    2. Dra den **BuildingID** till **axel**, och dra den **ActualTemp** och **TargetTemp** fält till **värdet**.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "skapa Spark datavisualiseringar med Apache Spark BI")

        Det ser ut som diagrammet:

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "skapa Spark datavisualiseringar med Apache Spark BI")

        Som standard visas visualiseringen summan för **ActualTemp** och **TargetTemp**. Klicka på pilen bredvid **ActualTemp** och **TragetTemp** i fönstret visualiseringar kan du se **summan** är markerad.

    3. Klicka på nedåtpilarna bredvid **ActualTemp** och **TragetTemp** Välj i rutan visualiseringar **genomsnittlig** få ett genomsnitt av faktiska och temperaturer som mål för varje Skapa.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "skapa Spark datavisualiseringar med Apache Spark BI")

        Din datavisualisering skall vara liknar det i skärmbilden. Flytta markören över visualiseringen få verktygstips med relevanta data.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "skapa Spark datavisualiseringar med Apache Spark BI")

7. Klicka på **filen** sedan **spara**, och ange namnet `BuildingTemperature.pbix` för filen. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)

Power BI-tjänsten kan du dela rapporter och instrumentpaneler i din organisation. I det här avsnittet kan publicera du datamängden och rapporten. Sedan kan fästa du rapporten till en instrumentpanel. Instrumentpaneler används vanligtvis för att fokusera på en delmängd av data i rapporten. du har en enda visualisering i rapporten, men det är ändå bra att gå igenom steg.

1. Öppna Power BI Desktop.
2. Från den **Start** klickar du på **publicera**.

    ![Publicera från Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "publicera från Power BI Desktop")

2. Välj en arbetsyta för att publicera datamängden och rapportera till, klicka på **Välj**. I följande bild, standard **Min arbetsyta** är markerad.

    ![Välj att publicera datamängden och rapportera till](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "väljer arbetsytan att publicera datamängden och rapportera till") 

3. När publiceringen är klar klickar du på **öppna 'BuildingTemperature.pbix' i Power BI**.

    ![Publicera lyckade, klicka för att ange autentiseringsuppgifter](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "publicera lyckade, klicka för att ange autentiseringsuppgifter") 

4. Klicka på Power BI-tjänsten **ange autentiseringsuppgifter**.

    ![Ange autentiseringsuppgifterna i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "ange autentiseringsuppgifterna i Power BI-tjänsten")

5. Klicka på **redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifter i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "redigera autentiseringsuppgifter i Power BI-tjänsten")

6. Ange konto för HDInsight inloggningsinformation och klicka sedan på **logga in**. Konto för standardnamnet är *admin*.

    ![Logga in på Spark-kluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "logga in på Spark-kluster")

7. I den vänstra rutan, gå till **arbetsytor** > **Min arbetsyta** > **rapporter**, klicka på **BuildingTemperature**.

    ![Rapporten som listas under rapporter i vänster](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "rapporten som listas under rapporter i vänster")

    Du bör också se **BuildingTemperature** visas under **DATAUPPSÄTTNINGAR** i den vänstra rutan.

    Den visuella informationen som du skapade i Power BI Desktop är nu tillgänglig i Power BI-tjänsten. 

8. Håller markören över visualiseringen och sedan klicka på ikonen PIN-kod i det övre högra hörnet.

    ![Rapporten i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "rapporten i Power BI-tjänsten")

9. Välj ”ny instrumentpanel”, ange namnet `Building temperature`, klicka på **PIN-kod**.

    ![PIN-kod för ny instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "PIN-kod för ny instrumentpanel")

10. I rapporten klickar du på **gå till instrumentpanelen**. 

Din visuella är fäst på instrumentpanelen – du kan lägga till annan visuell information i rapporten och fästa dem på samma instrumentpanelen. Mer information om rapporter och instrumentpaneler finns [rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)och [instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Nästa steg

Så länge du lärt dig hur du skapar ett kluster, och skapa Spark ramar att fråga efter data och komma åt dessa data från BI-verktyg. Du kan nu se anvisningar om hur du hanterar klusterresurserna och felsöka jobb som körs i ett HDInsight Spark-kluster.

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

