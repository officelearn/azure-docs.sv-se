---
title: "Spark BI med hjälp av verktyg för visualisering av data på Azure HDInsight | Microsoft Docs"
description: "Använd verktyg för visualisering av data för analytics med hjälp av BI för Apache Spark i HDInsight-kluster"
keywords: "Apache spark bi, spark bi, spark datavisualisering Väck affärsinformation"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: d1d5405a635b9f990f53b2bf32c8270a71a0f344
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI med hjälp av verktyg för visualisering av data med Azure HDInsight

Lär dig hur du använder Verktyg för visualisering av data som Power BI och Tableau för att analysera raw data exempeldata med Apache Spark BI på HDInsight-kluster.

> [!NOTE]
> Anslutning med BI-verktyg som beskrivs i denna artikel stöds inte på 2.1 Spark på Azure HDInsight 3,6 Preview. Endast Spark version 1.6 och 2.0 (HDInsight 3.4, 3.5 respektive) stöds.
>

Den här kursen är också tillgängliga som en Jupyter-anteckningsbok på ett HDInsight Spark-kluster. Upplevelse för bärbara datorer kan du köra Python kodavsnitt från den bärbara datorn sig själv. För att genomföra vägledningen i en bärbar dator, skapa ett Spark-kluster, starta en Jupyter-anteckningsbok (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), och kör sedan anteckningsboken **Använd BI-verktyg med Apache Spark i HDInsight.ipynb** under den **Python** mapp.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Förbered data för Spark datavisualisering

I det här avsnittet ska vi använda den [Jupyter](https://jupyter.org) bärbar dator från ett HDInsight Spark-klustret för att köra jobb som bearbetar dina rådata exempeldata och spara den som en tabell. Exempeldata är en CSV-fil (hvac.csv) tillgängliga på alla kluster som standard. När du har sparat dina data som en tabell i nästa avsnitt använder vi BI-verktyg kan ansluta till tabellen och utföra datavisualiseringar.

> [!NOTE]
> Om du utför stegen i den här artikeln när du har slutfört instruktionerna i [köra interaktiva frågor på ett HDInsight Spark-kluster](apache-spark-load-data-run-query.md), du kan hoppa till steg 8 nedan.
>

1. På startsidan i [Azure Portal](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

2. Från Spark-klusterbladet, klickar du på **Klusterinstrumentpanel** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   > [!NOTE]
   > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Skapa en anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

    ![Skapa en Jupyter-anteckningsbok för Apache Spark BI](./media/apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "skapa en Jupyter-anteckningsbok för Apache Spark BI")

4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken för Apache Spark BI](./media/apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "ange ett namn för anteckningsboken för Apache Spark BI")

5. Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. Du kan börja med att importera de typer som krävs för det här scenariot. Det gör du genom att placera markören i cell och trycka på **SKIFT + RETUR**.

        from pyspark.sql import *

6. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight, kopieras exempeldatafilen **hvac.csv** till det associerade lagringskontot under **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Klistra in följande kodutdrag och tryck på i en tom cell **SKIFT + RETUR**. Den här fragment registrerar data i en tabell som kallas **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Kontrollera att tabellen har skapats. Du kan använda den `%%sql` Magiskt tal för att köra Hive-frågor direkt. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Du se utdata som visas nedan:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    De tabeller som har falskt under den **isTemporary** kolumnen är hive-tabeller som är lagrade på metastore och kan nås från BI-verktyg. I den här självstudiekursen kommer det att ansluta till den **hvac** tabellen som vi skapade.

8. Kontrollera att tabellen innehåller de avsedda data. Kopiera följande fragment och tryck på på en tom cell i anteckningsboken **SKIFT + RETUR**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny.

## <a name="powerbi"></a>Använd Power BI för Spark datavisualisering

> [!NOTE]
> Det här avsnittet gäller endast för 1.6 Spark på HDInsight 3.4 och 2.0 Spark på HDInsight 3.5.
>
>

När du har sparat data som en tabell kan använda du Power BI för att ansluta till data och visualisera den för att skapa rapporter, instrumentpaneler och så vidare.

1. Kontrollera att du har åtkomst till Power BI. Du kan hämta en kostnadsfri förhandsversion prenumeration på Power BI från [http://www.powerbi.com/](http://www.powerbi.com/).

2. Logga in på [Power BI](http://www.powerbi.com/).

3. Längst ned i den vänstra rutan klickar du på **hämta Data**.

4. På den **hämta Data** sidan under **importera eller Anslut till Data**, för **databaser**, klickar du på **hämta**.

    ![Hämta data till Power BI för Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "hämta data till Power BI för Apache Spark BI")

5. Klicka på nästa skärm **Spark på Azure HDInsight** och klicka sedan på **Anslut**. När du uppmanas, anger kluster-URL (`mysparkcluster.azurehdinsight.net`) och autentiseringsuppgifterna för att ansluta till klustret.

    ![Ansluta till Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "ansluta till Apache Spark BI")

    När anslutningen har upprättats startar Power BI import av data från Spark-kluster i HDInsight.

6. Powerbi importerar data och lägger till en **Spark** dataset under den **datauppsättningar** rubrik. Klicka på den data att öppna ett nytt kalkylblad för att visualisera data. Du kan också spara kalkylbladet som en rapport. Sparar ett kalkylblad från den **filen** -menyn klickar du på **spara**.

    ![Apache Spark BI-panelen på Power BI-instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Apache Spark BI-panelen på Power BI-instrumentpanel")
7. Observera att den **fält** listan på rätt listorna den **hvac** tabellen som du skapade tidigare. Expandera tabellen om du vill se fälten i tabellen som du har definierat i anteckningsbok tidigare.

      ![Visa en lista med tabeller på Apache Spark BI-instrumentpanel](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "lista tabeller på Apache Spark BI-instrumentpanel")

8. Skapa en visualisering för att visa skillnaden mellan target temperatur- och faktiska temperatur för varje byggnad. Om du vill visualisera dina data, Välj **ytdiagram** (visas med röd ruta). Definiera axelns dra och släpp den **BuildingID** fältet under **axel**, och **ActualTemp**/**TargetTemp** fält under **värdet**.

    ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "skapa Spark datavisualiseringar med Apache Spark BI")

9. Som standard visas visualiseringen summan för **ActualTemp** och **TargetTemp**. Båda fälten listrutan, Välj **genomsnittlig** få ett genomsnitt av faktiska och temperaturer som mål för båda byggnader.

    ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "skapa Spark datavisualiseringar med Apache Spark BI")

10. Visualisering av data ska vara liknar det i skärmbilden. Flytta markören över visualiseringen få verktygstips med relevanta data.

    ![Skapa Spark visualisera data med Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "skapa Spark datavisualiseringar med Apache Spark BI")

11. Klicka på **spara** på den översta menyn och ange ett rapportnamn. Du kan även fästa den visuella informationen. När du fäster en visualisering lagras den på din instrumentpanel så att du kan spåra det senaste värdet i korthet.

   Du kan lägga till så många visualiseringar som du vill använda för samma datamängd och fästa dem på instrumentpanelen för en ögonblicksbild av dina data. Dessutom Spark-kluster i HDInsight är ansluten till Power BI med direct connect. Detta säkerställer att Power BI alltid har den senaste informationen från ditt kluster så du inte behöver schemalägga en uppdatering för datauppsättningen.

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

