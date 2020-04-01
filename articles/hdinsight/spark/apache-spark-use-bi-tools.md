---
title: 'Självstudiekurs: Analysera Azure HDInsight Apache Spark-data med Power BI'
description: Självstudiekurs – Använd Microsoft Power BI för att visualisera Apache Spark-data som lagrats HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 03/02/2020
ms.openlocfilehash: d7330225ecbdc6715847821a47c140a3c2b8d1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251956"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Självstudie: Analysera Apache Spark-data med Power BI i HDInsight

I den här självstudien får du lära dig hur du använder [Microsoft Power BI](https://powerbi.microsoft.com/) för att visualisera data i ett Apache Spark-kluster i Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/).

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Visualisera Spark-data med Power BI

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Slutför artikeln [Självstudie: Läsa in data och köra frågor på ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI-skrivbordet](https://powerbi.microsoft.com/en-us/desktop/).

* Valfritt: [Power BI-utvärderingsprenumeration](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Kontrollera datan

Den [Jupyter Notebook](https://jupyter.org/)-anteckningsbok som du skapade i [föregående självstudie](apache-spark-load-data-run-query.md) innehåller kod för att skapa en `hvac`-tabell. Den här tabellen är baserad på CSV-filen som `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`är tillgänglig på alla HDInsight Spark-kluster på . Följ stegen nedan för att verifiera datan.

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

3. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Stäng anteckningsboken för att frigöra resurser.

## <a name="visualize-the-data"></a>Visualisera datan

I det här avsnittet använder du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata.

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop

De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, läsa in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]  
> Anslutningsprogrammet som visas i den här artikeln finns för närvarande i en förhandsversion. Ge feedback som du har på webbplatsen för [Power BI Community](https://community.powerbi.com/) eller [Power BI-idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Öppna Power BI Desktop. Stäng startstänkskärmen om den öppnas.

2. Gå till **Hämta data** > mer på fliken**Start..**. **Home**

    ![Hämta data till Power BI Desktop från HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Hämta data till Power BI från Apache Spark BI")

3. Ange `Spark` i sökrutan, välj **Azure HDInsight Spark**och välj sedan **Anslut**.

    ![Hämta data till Power BI från Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Hämta data till Power BI från Apache Spark BI")

4. Ange kluster-URL:en `mysparkcluster.azurehdinsight.net`(i formuläret ) i textrutan **Server.**

5. Under **Dataanslutningsläge**väljer du **DirectQuery**. Välj sedan **OK**.

    Du kan använda valfritt dataanslutningsläge med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att hela datamängden uppdateras. Om du importerar data måste du uppdatera datamängden för att se ändringarna. Mer information om hur och när du ska använda DirectQuery finns i [Använda DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Ange hdinsight-inloggningskontoinformationen och välj sedan **Anslut**. Standardkontonamnet är *admin*.

7. Markera `hvac` tabellen, vänta för att se en förhandsgranskning av data och välj sedan **Läs in**.

    ![Spark-klusteranvändarnamn och lösenord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-klusteranvändarnamn och lösenord")

    Power BI Desktop har den information som behövs för att ansluta till Spark-klustret och läsa in data från `hvac`-tabellen. Tabellen och dess kolumner visas i fönstret **Fält**.

8. Visualisera skillnaden mellan måltemperatur och faktisk temperatur för varje byggnad:

    1. I fönstret **VISUALISERINGAR** väljer du **Ytdiagram**.

    2. Dra fältet **BuildingID** till **Axel** och dra fälten **ActualTemp** och **TargetTemp** till **Värde**.

        ![lägga till värdekolumner](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "lägga till värdekolumner")

        Diagrammet ser ut så här:

        ![områdesdiagramsumma](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "områdesdiagramsumma")

        Som standard visar visualiseringen summan för **ActualTemp** och **TargetTemp**. Markera nedpilen bredvid **ActualTemp** och **TragetTemp** i fönstret Visualiseringar, du kan se **Summa** är markerad.

    3. Välj nedpilarna bredvid **ActualTemp** och **TragetTemp** i fönstret Visualiseringar, välj **Medel för** att få ett genomsnitt av faktiska temperaturer och måltemperaturer för varje byggnad.

        ![genomsnitt av värden](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "genomsnitt av värden")

        Din datavisualisering bör likna den på skärmbilden. Flytta markören över visualiseringen för att få verktygstips med relevanta data.

        ![områdesdiagram](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "områdesdiagram")

9. Navigera **File** > till**Spara**fil `BuildingTemperature` , ange namnet på filen och välj sedan **Spara**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)

Med Power BI-tjänsten kan du dela rapporter och instrumentpaneler i din organisation. I det här avsnittet publicerar du först datamängden och rapporten. Sedan fäster du rapporten på en instrumentpanel. Instrumentpaneler används vanligtvis för att fokusera på en delmängd av data i en rapport. Du har bara en visualisering i rapporten, men det är fortfarande användbart att gå igenom stegen.

1. Öppna Power BI Desktop.

1. Välj **Publicera**på fliken **Start** .

    ![Publicera från Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicera från Power BI Desktop")

1. Välj en arbetsyta att publicera datauppsättningen och rapportera till och välj sedan **Välj**. I följande bild är standardinställningen **Min arbetsyta** markerad.

    ![Välj arbetsyta att publicera datauppsättning och rapportera till](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Välj arbetsyta att publicera datauppsättning och rapportera till")

1. När publiceringen har slutförts väljer du **Öppna "BuildingTemperature.pbix" i Power BI**.

    ![Publicera lyckad, klicka för att ange autentiseringsuppgifter](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicera lyckad, klicka för att ange autentiseringsuppgifter")

1. I Power BI-tjänsten väljer du **Ange autentiseringsuppgifter**.

    ![Ange autentiseringsuppgifter i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Ange autentiseringsuppgifter i Power BI-tjänsten")

1. Välj **Redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifter i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Redigera autentiseringsuppgifter i Power BI-tjänsten")

1. Ange hdinsight-inloggningskontoinformationen och välj sedan **Logga in**. Standardkontonamnet är *admin*.

    ![Logga in i Spark-kluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Logga in i Spark-kluster")

1. Gå till **Arbetsytor** > **I den** > vänstra rutan**och**välj sedan **BuildingTemperature**.

    ![Rapport som visas under rapporter i den vänstra rutan](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport som visas under rapporter i den vänstra rutan")

    Du bör också se **BuildingTemperature** under **DATAMÄNGDER** i den vänstra rutan.

    Det visuella objektet som du skapade i Power BI Desktop är nu tillgängligt i Power BI-tjänsten.

1. Håll markören över visualiseringen och markera sedan pinikonen i det övre högra hörnet.

    ![Rapport i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport i Power BI-tjänsten")

1. Välj "Ny instrumentpanel", `Building temperature`ange namnet och välj sedan **Fäst**.

    ![Fästa på den nya instrumentpanelen](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Fästa på den nya instrumentpanelen")

1. Välj **Gå till instrumentpanelen**i rapporten .

Ditt visuella objekt är fastsatt på instrumentpanelen. Du kan lägga till fler visuella objekt i rapporten och fästa dem på samma instrumentpanel. Mer information om rapporter och instrumentpaneler finns i [Rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) och [Instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder [Microsoft Power BI](https://powerbi.microsoft.com/) för att visualisera data i ett Apache Spark-kluster i Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/). Gå vidare till nästa artikel för att se att du kan skapa ett maskininlärningsprogram.

> [!div class="nextstepaction"]
> [Skapa ett maskininlärningsprogram](./apache-spark-ipython-notebook-machine-learning.md)