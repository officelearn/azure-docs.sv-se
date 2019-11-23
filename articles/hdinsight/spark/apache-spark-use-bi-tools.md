---
title: 'Tutorial: Analyze Azure HDInsight Apache Spark data with Power BI'
description: Tutorial - Use Microsoft Power BI to visualize Apache Spark data stored HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327281"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Självstudie: Analysera Apache Spark-data med Power BI i HDInsight

In this tutorial, you learn how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Visualisera Spark-data med Power BI

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Complete the article [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI trial subscription](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Kontrollera datan

Den [Jupyter Notebook](https://jupyter.org/)-anteckningsbok som du skapade i [föregående självstudie](apache-spark-load-data-run-query.md) innehåller kod för att skapa en `hvac`-tabell. This table is based on the CSV file available on all HDInsight Spark clusters at `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Följ stegen nedan för att verifiera datan.

1. Klistra in följande kod från Jupyter-anteckningsboken och tryck sedan på **SKIFT+RETUR**. Koden kontrollerar att tabellerna finns.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    De utdata som returneras ser ut så här:

    ![Visa tabellerna i Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Om du har stängt anteckningsboken innan du startar självstudien så har `hvactemptable` rensats, så den ingår inte i utdatan.  Endast Hive-tabeller som lagras i metaarkivet (anges med **False** under kolumnen **isTemporary**) kan nås från BI-verktygen. I den här självstudien kommer du ansluta till **hvac**-tabellen som du skapade.

2. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR**. Koden verifierar datan i tabellen.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    De utdata som returneras ser ut så här:

    ![Visa rader från hvac-tabellen i Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Stäng anteckningsboken för att frigöra resurserna.

## <a name="visualize-the-data"></a>Visualisera datan

I det här avsnittet använder du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata.

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop

De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, läsa in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]  
> Anslutningsprogrammet som visas i den här artikeln finns för närvarande i en förhandsversion. Ge feedback som du har på webbplatsen för [Power BI Community](https://community.powerbi.com/) eller [Power BI-idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Öppna Power BI Desktop. Close the start-up splash screen if it opens.

2. From the **Home** tab, navigate to **Get Data** > **More..** .

    ![Get data into Power BI Desktop from HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")

3. Enter `Spark` in the search box, select **Azure HDInsight Spark**, and then select **Connect**.

    ![Get data into Power BI from Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Get data into Power BI from Apache Spark BI")

4. Enter your cluster URL (in the form `mysparkcluster.azurehdinsight.net`) in the **Server** text box.

5. Under **Data connectivity mode**, select **DirectQuery**. Välj sedan **OK**.

    Du kan använda valfritt dataanslutningsläge med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att hela datamängden uppdateras. Om du importerar data måste du uppdatera datamängden för att se ändringarna. Mer information om hur och när du ska använda DirectQuery finns i [Använda DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Enter the HDInsight login account information, then select **Connect**. Standardkontonamnet är *admin*.

7. Select the `hvac` table, wait to see a preview of the data, and then select **Load**.

    ![Spark cluster user name and password](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark cluster user name and password")

    Power BI Desktop har den information som behövs för att ansluta till Spark-klustret och läsa in data från `hvac`-tabellen. Tabellen och dess kolumner visas i fönstret **Fält**.

8. Visualisera skillnaden mellan måltemperatur och faktisk temperatur för varje byggnad:

    1. I fönstret **VISUALISERINGAR** väljer du **Ytdiagram**.

    2. Dra fältet **BuildingID** till **Axel** och dra fälten **ActualTemp** och **TargetTemp** till **Värde**.

        ![add value columns](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "add value columns")

        Diagrammet ser ut så här:

        ![area graph sum](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "area graph sum")

        Som standard visar visualiseringen summan för **ActualTemp** och **TargetTemp**. Select the down arrow next to **ActualTemp** and **TragetTemp** in the Visualizations pane, you can see **Sum** is selected.

    3. Select the down arrows next to **ActualTemp** and **TragetTemp** in the Visualizations pane, select **Average** to get an average of actual and target temperatures for each building.

        ![average of values](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "average of values")

        Din datavisualisering bör likna den på skärmbilden. Flytta markören över visualiseringen för att få verktygstips med relevanta data.

        ![area graph](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "area graph")

9. Navigate to **File** > **Save**, enter the name `BuildingTemperature` for the file, then select **Save**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)

Med Power BI-tjänsten kan du dela rapporter och instrumentpaneler i din organisation. I det här avsnittet publicerar du först datamängden och rapporten. Sedan fäster du rapporten på en instrumentpanel. Dashboards are typically used to focus on a subset of data in a report. You have only one visualization in your report, but it's still useful to go through the steps.

1. Öppna Power BI Desktop.

1. På fliken **Start** klickar du på **Publicera**.

    ![Publicera från Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicera från Power BI Desktop")

1. Välj den arbetsyta där du ska publicera datamängden och rapporten. Klicka sedan på **Välj**. I följande bild är standardinställningen **Min arbetsyta** markerad.

    ![Select workspace to publish dataset and report to](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Select workspace to publish dataset and report to") 

1. När publiceringen är klar klickar du på **Öppna ”BuildingTemperature.pbix” i Power BI**.

    ![Publish success, click to enter credentials](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publish success, click to enter credentials") 

1. I Power BI-tjänsten klickar du på **Ange autentiseringsuppgifter**.

    ![Enter credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Enter credentials in Power BI service")

1. Klicka på **Redigera autentiseringsuppgifter**.

    ![Edit credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edit credentials in Power BI service")

1. Ange kontoinformationen för HDInsight-inloggningen och klicka sedan på **Logga in**. Standardkontonamnet är *admin*.

    ![Sign in to Spark cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Sign in to Spark cluster")

1. I den vänstra rutan går du till **Arbetsytor** > **Min arbetsyta** > **RAPPORTER**. Klicka sedan på **BuildingTemperature**.

    ![Report listed under reports in left pane](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report listed under reports in left pane")

    Du bör också se **BuildingTemperature** under **DATAMÄNGDER** i den vänstra rutan.

    Det visuella objektet som du skapade i Power BI Desktop är nu tillgängligt i Power BI-tjänsten. 

1. Hovra markören över visualiseringen och klicka sedan på stiftikonen i det övre högra hörnet.

    ![Report in the Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report in the Power BI service")

1. Välj ”Ny instrumentpanel”, ange namnet `Building temperature` och klicka sedan på **Fäst**.

    ![Pin to new dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin to new dashboard")

1. I rapporten klickar du på **Gå till instrumentpanelen**.

Ditt visuella objekt är fastsatt på instrumentpanelen. Du kan lägga till fler visuella objekt i rapporten och fästa dem på samma instrumentpanel. Mer information om rapporter och instrumentpaneler finns i [Rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) och [Instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it isn't in use. You're also charged for an HDInsight cluster, even when it isn't in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they aren't in use.

To delete a cluster, see [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

In this tutorial, you learned how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Advance to the next article to see you can create a machine learning application.

> [!div class="nextstepaction"]
> [Create a machine learning application](./apache-spark-ipython-notebook-machine-learning.md)