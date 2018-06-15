---
title: 'Självstudie: Analysera Apache Spark-data med Power BI i Azure HDInsight | Microsoft Docs'
description: Använda Microsoft Power BI till att visualisera Spark-data som lagras i HDInsight-kluster
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ece0132573f25f4d288309d2e7bb6710f8fd9519
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771175"
---
# <a name="tutorial-analyze-spark-data-using-power-bi-in-hdinsight"></a>Självstudie: Analysera Spark-data med Power BI i HDInsight 

Lär dig att använda Microsoft Power BI till att visualisera data i Apache Spark-kluster i Azure HDInsight.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Visualisera Spark-data med Power BI

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Slutför artikeln [Självstudie: Läsa in data och köra frågor på ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) och [Power BI-utvärderingsversion](https://app.powerbi.com/signupredirect?pbi_source=web) (valfritt).


## <a name="verify-the-data"></a>Kontrollera datan

Jupyter-anteckningsboken som du skapade i den [tidigare självstudien](apache-spark-load-data-run-query.md) innehåller kod för att skapa en `hvac`-tabell. Den här tabellen baseras på CSV-filen som finns tillgänglig i alla HDInsight Spark-kluster på **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Följ stegen nedan för att verifiera datan.

1. Klistra in följande kod från Jupyter-anteckningsboken och tryck sedan på **SKIFT+RETUR**. Koden kontrollerar att tabellerna finns.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    De utdata som returneras ser ut så här:

    ![Visa tabellerna i Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Om du har stängt anteckningsboken innan du startar självstudien så har `hvactemptable` rensats, så den ingår inte i utdatan.  Endast Hive-tabeller som lagras i metaarkivet (anges med **False** under kolumnen **isTemporary**) kan nås från BI-verktygen. I den här självstudien kommer du ansluta till **hvac**-tabellen som du skapade.

2. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR**. Koden verifierar datan i tabellen.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    De utdata som returneras ser ut så här:

    ![Visa rader från hvac-tabellen i Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Öppna menyn **Arkiv** i anteckningsboken och klicka på **Stäng och stoppa**. Stäng anteckningsboken för att frigöra resurserna. 

## <a name="visualize-the-data"></a>Visualisera datan

I det här avsnittet använder du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata. 

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, läsa in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]
> Anslutningsprogrammet som visas i den här artikeln finns för närvarande i en förhandsversion. Ge feedback som du har på webbplatsen för [Power BI Community](https://community.powerbi.com/) eller [Power BI-idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Öppna [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. På fliken **Start** klickar du på **Hämta data** och sedan på **Mer**.

    ![Hämta data till Power BI Desktop från Apache Spark i HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Hämta data till Power BI från Apache Spark BI")


2. Ange `Spark` i sökrutan, välj **Azure HDInsight Spark (betaversion)** och klicka sedan på **Anslut**.

    ![Hämta data till Power BI från Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Hämta data till Power BI från Apache Spark BI")

3. Ange din kluster-URL (i formatet `mysparkcluster.azurehdinsight.net`), välj **DirectQuery** och klicka sedan på **OK**.

    Du kan använda valfritt dataanslutningsläge med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att hela datamängden uppdateras. Om du importerar data måste du uppdatera datamängden för att se ändringarna. Mer information om hur och när du ska använda DirectQuery finns i [Använda DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Ange kontoinformationen för HDInsight-inloggningen och klicka sedan på **Anslut**. Standardkontonamnet är *admin*.

5. Välj `hvac`-tabellen, vänta för att se en förhandsgranskning av datan och klicka sedan på **Läs in**.

    ![Spark-klustrets användarnamn och lösenord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-klustrets användarnamn och lösenord")

    Power BI Desktop har den information som behövs för att ansluta till Spark-klustret och läsa in data från `hvac`-tabellen. Tabellen och dess kolumner visas i fönstret **Fält**.  Se följande skärmbild:

6. Visualisera skillnaden mellan måltemperatur och faktisk temperatur för varje byggnad: 

    1. I fönstret **VISUALISERINGAR** väljer du **Ytdiagram**. 
    2. Dra fältet **BuildingID** till **Axel** och dra fälten **ActualTemp** och **TargetTemp** till **Värde**.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Diagrammet ser ut så här:

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Som standard visar visualiseringen summan för **ActualTemp** och **TargetTemp**. Klicka på nedåtpilen bredvid **ActualTemp** och **TargetTemp** i fönstret Visualiseringar för att se **Summan** markerad.

    3. Klicka på nedåtpilarna bredvid **ActualTemp** och **TargetTemp** i rutan Visualiseringar och välj **Genomsnitt** för att få ett genomsnitt av faktiska temperaturer och måltemperaturer för varje byggnad.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Din datavisualisering bör likna den på skärmbilden. Flytta markören över visualiseringen för att få verktygstips med relevanta data.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

7. Klicka på **Arkiv**, **Spara** och ange namnet `BuildingTemperature.pbix` för filen. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)

Med Power BI-tjänsten kan du dela rapporter och instrumentpaneler i din organisation. I det här avsnittet publicerar du först datamängden och rapporten. Sedan fäster du rapporten på en instrumentpanel. Instrumentpaneler används vanligtvis för att fokusera på en delmängd data i en rapport. Det finns bara en enda visualisering i rapporten, men det är ändå bra att gå igenom stegen.

1. Öppna Power BI Desktop.
2. På fliken **Start** klickar du på **Publicera**.

    ![Publicera från Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicera från Power BI Desktop")

2. Välj den arbetsyta där du ska publicera datamängden och rapporten. Klicka sedan på **Välj**. I följande bild är standardinställningen **Min arbetsyta** markerad.

    ![Välj arbetsyta där datamängden och rapporten ska publiceras](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Välj arbetsyta där datamängden och rapporten ska publiceras") 

3. När publiceringen är klar klickar du på **Öppna ”BuildingTemperature.pbix” i Power BI**.

    ![Publiceringen är klar, klicka för att ange autentiseringsuppgifter](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publiceringen är klar, klicka för att ange autentiseringsuppgifter") 

4. I Power BI-tjänsten klickar du på **Ange autentiseringsuppgifter**.

    ![Ange autentiseringsuppgifter i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Ange autentiseringsuppgifter i Power BI-tjänsten")

5. Klicka på **Redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifterna i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Redigera autentiseringsuppgifterna i Power BI-tjänsten")

6. Ange kontoinformationen för HDInsight-inloggningen och klicka sedan på **Logga in**. Standardkontonamnet är *admin*.

    ![Logga in på Spark-kluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Logga in på Spark-kluster")

7. I den vänstra rutan går du till **Arbetsytor** > **Min arbetsyta** > **RAPPORTER**. Klicka sedan på **BuildingTemperature**.

    ![Rapport som listas under rapporter i den vänstra rutan](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport som listas under rapporter i den vänstra rutan")

    Du bör också se **BuildingTemperature** under **DATAMÄNGDER** i den vänstra rutan.

    Det visuella objektet som du skapade i Power BI Desktop är nu tillgängligt i Power BI-tjänsten. 

8. Hovra markören över visualiseringen och klicka sedan på stiftikonen i det övre högra hörnet.

    ![Rapport i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport i Power BI-tjänsten")

9. Välj ”Ny instrumentpanel”, ange namnet `Building temperature` och klicka sedan på **Fäst**.

    ![Fäst på ny instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Fäst på ny instrumentpanel")

10. I rapporten klickar du på **Gå till instrumentpanelen**. 

Ditt visuella objekt är fastsatt på instrumentpanelen. Du kan lägga till fler visuella objekt i rapporten och fästa dem på samma instrumentpanel. Mer information om rapporter och instrumentpaneler finns i [Rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) och [Instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

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

I den här självstudiekursen lärde du dig att:

- Visualisera Spark-data med Power BI.

Gå vidare till nästa artikel för att se hur de data som du har registrerat i Spark kan hämtas till ett BI-analysverktyg som till exempel Power BI. 
> [!div class="nextstepaction"]
> [Köra ett Spark-direktuppspelningsjobb](apache-spark-eventhub-streaming.md)

