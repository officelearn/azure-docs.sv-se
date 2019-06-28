---
title: 'Självstudier: Analysera Apache Spark-data med Power BI i Azure HDInsight '
description: Självstudie – Använd Microsoft Power BI för att visualisera data med Apache Spark lagras HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: d5296fe19cef9e8881d39bd9e59eb4c40d049959
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296193"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Självstudier: Analysera Apache Spark-data med Power BI i HDInsight

I den här självstudien får du lära dig hur du använder [Microsoft Power BI](https://powerbi.microsoft.com/) att visualisera data i ett Apache Spark-kluster i [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Visualisera Spark-data med Power BI

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutför artikeln [självstudien: Läs in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Valfritt: [Power BI-utvärderingsprenumeration](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Kontrollera datan

Den [Jupyter Notebook](https://jupyter.org/)-anteckningsbok som du skapade i [föregående självstudie](apache-spark-load-data-run-query.md) innehåller kod för att skapa en `hvac`-tabell. Den här tabellen är baserad på CSV-filen tillgänglig på alla HDInsight Spark-kluster på `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Följ stegen nedan för att verifiera datan.

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

3. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Stäng anteckningsboken för att frigöra resurserna.

## <a name="visualize-the-data"></a>Visualisera datan

I det här avsnittet använder du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata.

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop

De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, läsa in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]  
> Anslutningsprogrammet som visas i den här artikeln finns för närvarande i en förhandsversion. Ge feedback som du har på webbplatsen för [Power BI Community](https://community.powerbi.com/) eller [Power BI-idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Öppna Power BI Desktop. Stäng välkomstskärmen startskärm om den öppnas.

2. Från den **Start** fliken, navigera till **hämta Data** > **mer...** .

    ![Hämta data till Power BI Desktop från Apache Spark i HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Hämta data till Power BI från Apache Spark BI")

3. Ange `Spark` i sökrutan, Välj **Azure HDInsight Spark**, och välj sedan **Connect**.

    ![Hämta data till Power BI från Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Hämta data till Power BI från Apache Spark BI")

4. Anger en URL för kluster (i formatet `mysparkcluster.azurehdinsight.net`) i den **Server** textrutan.

5. Under **läge för dataanslutning**väljer **DirectQuery**. Välj sedan **OK**.

    Du kan använda valfritt dataanslutningsläge med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att hela datamängden uppdateras. Om du importerar data måste du uppdatera datamängden för att se ändringarna. Mer information om hur och när du ska använda DirectQuery finns i [Använda DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Ange konto för HDInsight inloggningsinformation och välj sedan **Connect**. Standardkontonamnet är *admin*.

7. Välj den `hvac` tabellen, vänta med att visa en förhandsgranskning av data och välj sedan **belastningen**.

    ![Spark-klustrets användarnamn och lösenord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-klustrets användarnamn och lösenord")

    Power BI Desktop har den information som behövs för att ansluta till Spark-klustret och läsa in data från `hvac`-tabellen. Tabellen och dess kolumner visas i fönstret **Fält**.

8. Visualisera skillnaden mellan måltemperatur och faktisk temperatur för varje byggnad:

    1. I fönstret **VISUALISERINGAR** väljer du **Ytdiagram**.

    2. Dra fältet **BuildingID** till **Axel** och dra fälten **ActualTemp** och **TargetTemp** till **Värde**.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Diagrammet ser ut så här:

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Som standard visar visualiseringen summan för **ActualTemp** och **TargetTemp**. Välj nedpilen bredvid **ActualTemp** och **TragetTemp** i fönstret visualiseringar, kan du se **summan** har valts.

    3. Välj nedåtpilarna bredvid **ActualTemp** och **TragetTemp** i fönstret visualiseringar väljer **genomsnittlig** att få ett genomsnitt av faktiska och mål temperaturer för varje att skapa.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

        Din datavisualisering bör likna den på skärmbilden. Flytta markören över visualiseringen för att få verktygstips med relevanta data.

        ![Skapa Spark-datavisualiseringar med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Skapa Spark-datavisualiseringar med Apache Spark BI")

9. Gå till **filen** > **spara**, anger du namnet `BuildingTemperature` för filen, Välj **spara**.

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

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder [Microsoft Power BI](https://powerbi.microsoft.com/) att visualisera data i ett Apache Spark-kluster i [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Gå vidare till nästa artikel för att se hur de data som du har registrerat i Spark kan hämtas till ett BI-analysverktyg, som till exempel Power BI.

> [!div class="nextstepaction"]
> [Köra ett Apache Spark-strömningsjobb](apache-spark-eventhub-streaming.md)