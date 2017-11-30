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
ms.date: 10/24/2017
ms.author: jgao
ms.openlocfilehash: fabf48da80cf44e82068d180c896ebbca7078d18
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI med hjälp av verktyg för visualisering av data med Azure HDInsight

Lär dig hur du använder Power BI och Tableau visualisera data i ett Apache Spark-kluster i Azure HDInsight.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Exempeldata i klustret. Instruktioner finns i [köra interaktiva frågor på ett HDInsight Spark-kluster](apache-spark-load-data-run-query.md).
* Powerbi: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) och [Power BI-utvärderingsprenumeration](https://app.powerbi.com/signupredirect?pbi_source=web) (valfritt).
* Tableau: [Tableau Desktop](http://www.tableau.com/products/desktop) och [Microsoft Spark ODBC-drivrutinen](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Granska exempeldata

Jupyter-anteckningsbok som du skapade i den [tidigare kursen](apache-spark-load-data-run-query.md) innehåller koden för att skapa en `hvac` tabell. Den här tabellen baserat på CSV-filen tillgänglig på alla HDInsight Spark-kluster på **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Nu ska vi se data i Spark-klustret innan du skapar visualiseringar.

1. Kontrollera att de förväntade tabellerna finns. Kopiera följande fragment och tryck på på en tom cell i anteckningsboken **SKIFT + RETUR**.

        %%sql
        SHOW TABLES

    Du kan se utdata som liknar det som visas nedan:

    ![Visa tabellerna i Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Om du har stängt den bärbara datorn innan du påbörjar den här självstudiekursen `hvactemptable` rensas, så inte ingår i utdata.
    Endast hive-tabeller som lagras i metastore (anges av **FALSKT** under den **isTemporary** kolumn) kan nås från BI-verktyg. I den här självstudiekursen kommer det att ansluta till den **hvac** tabellen som vi skapade.

2. Kontrollera att tabellen innehåller de förväntade data. Kopiera följande fragment och tryck på på en tom cell i anteckningsboken **SKIFT + RETUR**.

        %%sql
        SELECT * FROM hvac LIMIT 10

    Du kan se utdata som liknar det som visas nedan:

    ![Visa rader från hvac-tabellen i Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny.

## <a name="powerbi"></a>Använd Power BI för Spark datavisualisering

Nu när du har kontrollerat att förväntade data finns kan använda du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från dessa data. I den här artikeln ska vi visar ett enkelt exempel med statiska data, men låt oss veta i kommentarer om du vill se mer komplexa strömmande exempel.

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, Läs in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

> [!NOTE]
> Kopplingen visas i den här artikeln är för närvarande under förhandsgranskning, men vi rekommenderar att du kan använda den och ge feedback du ha via den [Power BI-communityn](https://community.powerbi.com/) plats eller [Power BI idéer](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. I Power BI Desktop på den **Start** klickar du på **hämta Data**, sedan **mer**.

2. Sök efter `Spark`väljer **Azure HDInsight Spark**, klicka på **Anslut**.

    ![Hämta data till Power BI från Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "hämta data till Power BI från Apache Spark BI")

3. Ange kluster-URL (i formatet `mysparkcluster.azurehdinsight.net`), Välj **DirectQuery**, klicka på **OK**.

    ![Ansluta till Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "ansluta till Apache Spark BI")

    > [!NOTE]
    > Du kan använda antingen anslutningsläget med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att uppdatera hela datamängden. Om du importerar data måste du uppdatera datauppsättningen kan se ändringar. Mer information om hur och när du ska använda DirectQuery finns [med DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Ange kontoinformationen för HDInsight inloggning **användarnamn** och **lösenord** (standardkontot är `admin`), klicka på **Anslut**.

    ![Väck klustret användarnamn och lösenord](./media/apache-spark-use-bi-tools/user-password.png "Väck klustret användarnamn och lösenord")

5. Välj den `hvac` table och vänta med att visa en förhandsgranskning av data. Klicka på **belastningen**.

    ![Väck klustret användarnamn och lösenord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Väck klustret användarnamn och lösenord")

    Power BI Desktop har nu all information som behövs för att ansluta till Spark-klustret och Läs in data från den `hvac` tabell. Tabellen och dess kolumner visas i den **fält** fönstret.

    ![Visa en lista med tabeller på Apache Spark BI-instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "lista tabeller på Apache Spark BI-instrumentpanel")

7. Skapa en visualisering för att visa skillnaden mellan target temperatur- och faktiska temperatur för skapande av varje: 

    1. I den **VISUALISERINGAR** väljer **ytdiagram**. Dra den **BuildingID** till **axel**, och dra den **ActualTemp** och **TargetTemp** fält till **värdet**.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "skapa Spark datavisualiseringar med Apache Spark BI")

    2. Som standard visas visualiseringen summan för **ActualTemp** och **TargetTemp**. För båda fälten från listrutan, Välj **genomsnittlig** få ett genomsnitt av faktiska och temperaturer som mål för varje byggnad.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "skapa Spark datavisualiseringar med Apache Spark BI")

    3. Visualisering av data ska vara liknar det i skärmbilden. Flytta markören över visualiseringen få verktygstips med relevanta data.

        ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "skapa Spark datavisualiseringar med Apache Spark BI")

11. Klicka på **filen** sedan **spara**, och ange namnet `spark.pbix` för filen. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)
Nu har du en fullt fungerande rapporten i Power BI Desktop och du kan stoppa det, men många användare som vill dra nytta av Power BI-tjänsten, vilket gör det enkelt att dela rapporter och instrumentpaneler för hela organisationen. 

I det här avsnittet kan publicera du datasetet och rapporten i Power BI Desktop-fil som du skapade. Du kan sedan fästa visualiseringen från rapporten till en instrumentpanel. Instrumentpaneler används vanligtvis för att fokusera på en delmängd av data i rapporten. du har en enda visualisering i rapporten, men det är ändå bra att gå igenom steg.

1. I Power BI Desktop på den **Start** klickar du på **publicera**.

    ![Publicera från Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "publicera från Power BI Desktop")

2. Välj en arbetsyta för att publicera datamängden och rapportera till, klicka på **Välj**. I följande bild, standard **Min arbetsyta** är markerad.

    ![Välj att publicera datamängden och rapportera till](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "väljer arbetsytan att publicera datamängden och rapportera till") 

3. När Power BI Desktop returnerar med ett meddelande, klickar du på **öppna 'spark.pbix' i Power BI**.

    ![Publicera lyckade, klicka för att ange autentiseringsuppgifter](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "publicera lyckade, klicka för att ange autentiseringsuppgifter") 

4. Klicka på Power BI-tjänsten **ange autentiseringsuppgifter**.

    ![Ange autentiseringsuppgifterna i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "ange autentiseringsuppgifterna i Power BI-tjänsten")

5. Klicka på **redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifter i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "redigera autentiseringsuppgifter i Power BI-tjänsten")

6. Ange konto för HDInsight inloggningsinformation (vanligtvis standard `admin` konto som du använde i Power BI Desktop), klicka på **logga in**.

    ![Logga in på Spark-kluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "logga in på Spark-kluster")

7. I den vänstra rutan, gå till **arbetsytor** > **Min arbetsyta** > **rapporter**, klicka på **spark**.

    ![Rapporten som listas under rapporter i vänster](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "rapporten som listas under rapporter i vänster")

    Du bör också se **spark** visas under **DATAUPPSÄTTNINGAR** i den vänstra rutan.

8. Den visuella informationen som du skapade i Power BI Desktop är nu tillgänglig i tjänsten. Hovra över den visuella informationen och klicka på ikonen PIN-kod för att fästa den här visuella informationen till en instrumentpanel.

    ![Rapporten i Power BI-tjänsten](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "rapporten i Power BI-tjänsten")

9. Välj ”ny instrumentpanel”, ange namnet `SparkDemo`, klicka på **PIN-kod**.

    ![PIN-kod för ny instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "PIN-kod för ny instrumentpanel")

10. I rapporten klickar du på **gå till instrumentpanelen**. 

    ![Gå till instrumentpanelen](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "gå till instrumentpanelen")

Din visuella är fäst på instrumentpanelen – du kan lägga till annan visuell information i rapporten och fästa dem på samma instrumentpanelen. Mer information om rapporter och instrumentpaneler finns [rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)och [instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>Använda Tableau skrivbordet för Spark datavisualisering

> [!NOTE]
> Det här avsnittet gäller endast för Spark 1.5.2 kluster som skapas i Azure HDInsight.
>
>

1. Installera [Tableau Desktop](http://www.tableau.com/products/desktop) på datorn där du kör den här kursen om Apache Spark BI.

2. Kontrollera att datorn har även Microsoft Spark ODBC-drivrutinen ska installeras. Du kan installera drivrutinen från [här](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Starta Tableau skrivbordet. I den vänstra rutan i listan över server för att ansluta till, klickar du på **Spark SQL**. Om inte Spark SQL visas som standard i den vänstra rutan, du kan hitta den genom att klicka på **fler servrar**.
2. I dialogrutan Spark SQL-anslutningen anger du värdena som visas i skärmbilden och klicka sedan på **OK**.

    ![Anslut till ett kluster för Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Anslut till ett kluster för Apache Spark BI")

    Listrutorna autentisering **Microsoft Azure HDInsight-tjänst** som ett alternativ, bara om du har installerat den [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) på datorn.
3. På nästa skärm från den **schemat** listrutan, klicka på den **hitta** ikonen och klickar sedan på **standard**.

    ![Hitta schema för Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "hitta schemat för Apache Spark BI")
4. För den **tabell** klickar du på **hitta** ikonen igen till alla Hive-tabeller som finns i klustret. Du bör se den **hvac** tabellen som du skapat tidigare med den bärbara datorn.

    ![Hitta tabellen för Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "hitta tabellen för Apache Spark BI")
5. Dra och släppa tabellen till den översta rutan till höger. Tableau importerar data och visar schemat som markerade med en röd ruta.

    ![Lägga till tabeller i Tableau för Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "lägga till tabeller i Tableau för Apache Spark BI")
6. Klicka på den **Blad1** längst till vänster. Gör en visualisering som visar genomsnittlig mål- och faktiska temperaturer för alla byggnader för varje datum. Dra **datum** och **skapa ID** till **kolumner** och **faktiska Temp**/**mål Temp** att **rader**. Under **märken**väljer **område** att använda en mappning för område för Spark datavisualisering.

     ![Lägga till fält i Spark datavisualisering](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "lägga till fält i Spark datavisualisering")
7. Som standard temperatur fält visas som aggregat. Om du vill visa de genomsnittliga temperaturerna i stället kan du göra det från nedrullningsbara enligt nedan.

    ![Ta medelvärde för temperaturen för Spark datavisualisering](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "ta medelvärde för temperaturen för Spark datavisualisering")

8. Du kan också super-införa en temperatur karta över den andra sätt att få en bättre känsla av skillnaden mellan mål och faktiska temperaturer. Flytta musen till lägre området kartan hörn tills du ser referensen formen markerade med en röd cirkel. Dra kartan till andra kartan högst upp och släpper musknappen när du ser formen markerat i rött rektangel.

    ![Sammanfoga för Spark datavisualisering](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge mappar för Spark datavisualisering")

     Ändra din datavisualisering som visas i skärmbilden:

    ![Tableau utdata för Spark datavisualisering](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau utdata för Spark datavisualisering")
9. Klicka på **spara** spara kalkylbladet. Du kan skapa instrumentpaneler och Lägg till en eller flera blad.

## <a name="next-steps"></a>Nästa steg

Så länge du lärt dig hur du skapar ett kluster, och skapa Spark ramar att fråga efter data och komma åt dessa data från BI-verktyg. Du kan nu se anvisningar om hur du hanterar klusterresurserna och felsöka jobb som körs i ett HDInsight Spark-kluster.

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

