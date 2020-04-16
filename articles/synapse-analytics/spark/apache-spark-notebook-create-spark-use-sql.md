---
title: 'Snabbstart: Skapa en Apache Spark-pool (förhandsversion) i Azure Synapse Analytics'
description: Den här snabbstarten visar hur du använder webbverktygen för att skapa en Apache Spark-pool (förhandsversion) i Azure Synapse Analytics och köra en Spark SQL-fråga.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424302"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Snabbstart: Skapa en Apache Spark-pool (förhandsversion) i Synapse Analytics med hjälp av webbverktyg

I den här snabbstarten får du lära dig hur du skapar en Apache Spark-pool (förhandsversion) i Azure Synapse Analytics med hjälp av webbverktyg. Du lär dig sedan att ansluta till Apache Spark-poolen och köra Spark SQL-frågor mot filer och tabeller. Apache Spark möjliggör snabb data-analys och kluster-computing med minnesintern bearbetning. Information om Spark på Synapse Analytics finns i [Översikt: Apache Spark på Azure Synapse Analytics](apache-spark-overview.md).

> [!IMPORTANT]
> Fakturering för Spark-instanser beräknas proportionellt per minut, oavsett om du använder dem eller inte. Var noga med att stänga av Spark-instansen när du har använt den eller angett en kort timeout. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-spark-pool"></a>Skapa en Apache Spark-pool

Den här artikeln visar hur du skapar en ny Apache Spark-pool med hjälp av webbverktyg.

## <a name="create-a-notebook"></a>Skapa en notebook-fil

En anteckningsbok är en interaktiv miljö som stöder olika programmeringsspråk. I anteckningsboken kan du interagera med dina data, kombinera kod med markdown, text och utföra enkla visualiseringar.

1. Välj **Starta Synapse Studio**i Azure-portalvyn för den Arbetsyta i Synapse Analytics som du vill använda .
2. När Synapse Analytics Studio har lanserats väljer du **Utveckla**. Håll sedan muspekaren över posten **Anteckningsböcker.** Välj ellips (**...**).
3. Därifrån väljer du **Ny anteckningsbok**. En ny anteckningsbok skapas och öppnas med ett automatiskt genererat namn.
  ![Ny anteckningsbok](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Ny anteckningsbok")

4. Ange ett namn på anteckningsboken i fönstret **Egenskaper.**
5. Klicka på **Publicera**i verktygsfältet .
6. Om det bara finns en Apache Spark-pool på arbetsytan väljs den som standard. Använd listrutan för att välja rätt Apache Spark-pool om ingen är markerad.
7. Klicka på **Lägg till kod**. Standardspråket `Pyspark`är . Du kommer att använda en blandning av Pyspark och Spark SQL, så standardvalet är bra.
8. Därefter skapar du ett enkelt Spark DataFrame-objekt att manipulera. I det här fallet skapar du den från koden. Det finns tre rader och tre kolumner:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Kör nu cellen med någon av följande metoder:

   - Tryck på **SKIFT + RETUR**.
   - Markera den blå uppspelningsikonen till vänster om cellen.
   - Markera knappen **Kör alla** i verktygsfältet.

   ![Skapa dataramobjekt](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Utdata från Spark-jobbet")

10. Om Apache Spark-poolinstansen inte redan körs startas den automatiskt. Du kan se status för Apache Spark-poolförekomsten under cellen du kör och även på statuspanelen längst ned i anteckningsboken. Beroende på storleken på poolen, bör starten ta 2-5 minuter. När koden har körts visar information under cellen hur lång tid det tog att köra och dess körning. I utdatacellen visas utdata.

    ![Utdata från att köra en cell](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Utdata från Spark-jobbet")

11. Data finns nu i en DataFrame därifrån kan du använda data på många olika sätt. Du kommer att behöva det i olika format för resten av denna snabbstart.
12. Ange koden nedan i en annan cell och kör den, skapar detta en Spark-tabell, en CSV och en Parkettfil med kopior av data:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Om du använder lagringsutforskaren är det möjligt att se effekten av de två olika sätten att skriva en fil som används ovan. När inget filsystem har angetts används standardvärdet `default>user>trusted-service-user>demo_df`i det här fallet . Data sparas på platsen för det angivna filsystemet.

    Meddelande i både "csv" och "parkett" format, skriva operationer en katalog skapas med många partitionerade filer.

    ![Lagringsutforskaren för utdata](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "Lagringsutforskaren för utdata")

    ![Lagringsutforskaren för utdata](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "Lagringsutforskaren för utdata")

## <a name="run-spark-sql-statements"></a>Kör Spark SQL-instruktioner

SQL (Structured Query Language) är det vanligaste språket för frågor och definition av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Klistra in följande kod i en tom cell och kör sedan koden. Kommandot visar tabellerna i poolen.

   ```sql
   %%sql
   SHOW TABLES
   ```

   När du använder en anteckningsbok med din Synapse Analytics `sqlContext` Apache Spark-pool får du en förinställning som du kan använda för att köra frågor med Spark SQL. `%%sql`talar om för anteckningsboken att använda förinställningen `sqlContext` för att köra frågan. Frågan hämtar de 10 översta raderna från en systemtabell som levereras med alla Synapse Analytics Apache Spark-pooler som standard.

2. Kör ytterligare en fråga för att visa data i `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Koden producerar två utdataceller, en som innehåller dataresultat den andra, som visar jobbvyn.

    Som standard visar resultatvyn ett rutnät, men det finns en vyväxlare under rutnätet som gör att vyn kan växla mellan rutnäts- och diagramvyer.

    ![Frågeutdata i Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Frågeutdata i Synapse Analytics Spark")

3. Välj **Diagram** i **växelreglaget** Visa
4. Välj ikonen **Visa alternativ** längst till höger.
5. Välj "stapeldiagram" i fältet **Diagramtyp.**
6. Välj "tillstånd" i kolumnfältet X-axeln.
7. Välj "lön" i kolumnfältet Y-axeln.
8. Välj till "AVG" i fältet **Aggregering.**
9. Välj **Använd**.

   ![Diagramutdata i Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Diagramutdata i Synapse Analytics Spark")

10. Det är möjligt att få samma erfarenhet av att köra SQL men utan att behöva byta språk. Du kan göra detta genom att ersätta SQL-cellen ovan med denna **display** PySpark-cell, utdataupplevelsen är densamma eftersom visningskommandot används:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Var och en av de celler som tidigare körts hade möjlighet att gå till **History Server** och **Monitoring**. Om du klickar på länkarna kommer du till olika delar av användarupplevelsen.

## <a name="clean-up-resources"></a>Rensa resurser

Synapse Analytics sparar dina data i Azure Data Lake Storage. Du kan på ett säkert sätt låta en Spark-instans stängas av när den inte används. Du debiteras för en Synapse Analytics Apache Spark-pool så länge den körs, även när den inte används. Eftersom avgifterna för poolen är många gånger mer än avgifterna för lagring, är det ekonomiskt meningsfullt att låta Spark-instanser stängas när de inte används.

Om du vill vara säkra på att Spark-instansen stängs av avslutar du alla anslutna sessioner (anteckningsböcker). Poolen stängs av när den **inaktiva tid som** anges i Apache Spark-poolen har nåtts. Du kan också välja **slutsession** från statusfältet längst ned i anteckningsboken.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en Synapse Analytics Apache Spark-pool och kör en grundläggande Spark SQL-fråga.

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/latest/)

>[!NOTE]
> En del av den officiella Apache Spark-dokumentationen är beroende av att använda Spark-konsolen, som inte är tillgänglig på Azure Synapse Spark. Använd [anteckningsboken](../spark/apache-spark-notebook-create-spark-use-sql.md) eller [IntelliJ-upplevelserna](../spark/intellij-tool-synapse.md) i stället.
