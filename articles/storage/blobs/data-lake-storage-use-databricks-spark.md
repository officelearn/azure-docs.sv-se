---
title: 'Självstudier: Få åtkomst till Azure Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark| Microsoft Docs'
description: I den här självstudien lär du dig att köra Spark-frågor i ett Azure Databricks-kluster för att komma åt data i ett Azure Data Lake Storage Gen2-lagringskonto.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: e72a4f71a42a892d14fad076b124426f0c32ac7d
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321814"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Självstudier: Få åtkomst till Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark

Den här självstudien visar hur du ansluter ditt Azure Databricks-kluster till data som lagras i ett Azure-lagringskonto som har Azure Data Lake Storage Gen2 (förhandsversion) aktiverat. Med den här anslutningen kan du internt köra frågor och analyser från klustret på dina data.

I den här kursen ska du:

> [!div class="checklist"]
> * Skapa ett Databricks-kluster
> * Mata in ostrukturerade data i ett lagringskonto
> * Köra analyser på dina data i Blob Storage

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien visar hur du använder och frågar efter flyginformation som är tillgänglig från [USA:s transportdepartement](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Markera kryssrutan **Prezipped file** (Förkomprimerad fil) för att markera alla datafält.
2. Välj **Ladda ned** och spara resultatet på datorn.
3. Anteckna filnamnet och sökvägen för nedladdningen. Du behöver den informationen i ett senare steg.

För att slutföra den här självstudien behöver du ett lagringskonto med analysfunktioner. Vi rekommenderar att du slutför [snabbstarten](data-lake-storage-quickstart-create-account.md) om ämnet för att skapa ett. 

## <a name="set-aside-storage-account-configuration"></a>Spara lagringskontokonfiguration

Du behöver namnet på ditt lagringskonto och en slutpunkts-URI för filsystem.

Du kan få fram namnet på ditt lagringskonto i Azure-portalen genom att välja **Alla tjänster** och filtrera på *lagring*. Välj sedan **Lagringskonton** och leta rätt på ditt lagringskonto.

Du kan få fram slutpunkts-URI:n för filsystem genom att välja **Egenskaper**, och i egenskapsfönstret hittar du värdet för fältet **Primär slutpunkt för ADLS-filsystem**.

Klistra in båda dessa värden i en textfil. Du kommer att behöva dem snart.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa ett huvudnamn för tjänsten genom att följa anvisningarna i det här avsnittet: [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Det finns några saker som du måste göra när du utför stegen i den här artikeln.

:heavy_check_mark: När du utför stegen i avsnittet [Skapa ett Azure Active Directory-program](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) i artikeln måste du ange slutpunkts-URI:n som du nyss tog fram i fältet **Inloggnings-URL** i dialogrutan **Skapa**.

:heavy_check_mark: När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) i artikeln måste du tilldela programmet till **deltagarrollen för bloblagring**.

:heavy_check_mark: När du utför stegen i avsnittet [Hämta värden för att logga in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i artikeln klistrar du in värdena för klient-ID, program-ID och autentiseringsnyckel i en textfil. Du kommer att behöva dem snart.

## <a name="create-a-databricks-cluster"></a>Skapa ett Databricks-kluster

Nästa steg är att skapa ett Databricks-kluster för att skapa en dataarbetsyta.

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs**.
2. Ange **Azure Databricks** i sökfältet.
3. Välj **Skapa** på Azure Databricks-bladet.
4. Ge Databricks-tjänsten namnet **myFlightDataService** (se till att markera kryssrutan *Fäst på instrumentpanelen* när du skapar tjänsten).
5. Välj **Launch Workspace** (Starta arbetsyta) för att öppna arbetsytan i ett nytt webbläsarfönster.
6. Välj **Kluster** i det vänstra navigeringsfältet.
7. Välj **Skapa kluster**.
8. Ange **myFlightDataCluster** i fältet **Klusternamn**.
9. Välj **Standard_D8s_v3** i fältet **Worker Type** (Typ av arbetare).
10. Ändra värdet för **Min Workers** (Minsta antal arbetare) till **4**.
11. Välj **Skapa kluster** överst på sidan. (Den här processen kan ta upp till 5 minuter att slutföra.)
12. När processen är klar väljer du **Azure Databricks** längst upp till vänster i navigeringsfältet.
13. Välj **Notebook** (Anteckningsbok) under avsnittet **New** (Ny) på den nedre halvan av sidan.
14. Ange valfritt namn i fältet **Name** (Namn) och välj **Python** som språk.
15. Alla andra fält kan lämnas med standardvärdena.
16. Välj **Skapa**.
17. Kopiera och klistra in följande kodblock i den första cellen, men kör inte den här koden än.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. I det här kodblocket ersätter du platshållarvärdena `storage-account-name`, `application-id`, `authentication-id` och `tenant-id` med de värden som du hämtade när du genomförde stegen i avsnitten [Spara lagringskontokonfiguration](#config) och [Skapa ett huvudnamn för tjänsten](#service-principal) i den här artikeln. Ersätt platshållaren `file-system-name` med ett namn som du vill ge ditt filsystem.

19. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

## <a name="ingest-data"></a>Mata in data

### <a name="copy-source-data-into-the-storage-account"></a>Kopiera källdata till lagringskontot

Sedan använder du AzCopy för att kopiera data från *.csv*-filen till Azure Storage. Öppna kommandotolken och ange följande kommandon. Se till att ersätta platshållarna `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` och `<ACCOUNT_KEY>` med motsvarande värden som du sparade i ett tidigare steg.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Använda Databricks-anteckningsboken för att konvertera CSV till Parquet

Öppna Databricks på nytt i webbläsaren och utför följande steg:

1. Välj **Azure Databricks** längst upp till vänster i navigeringsfältet.
2. Välj **Notebook** (Anteckningsbok) under avsnittet **New** (Ny) på den nedre halvan av sidan.
3. Ange **CSV2Parquet** i fältet **Name** (Namn).
4. Alla andra fält kan lämnas med standardvärdena.
5. Välj **Skapa**.
6. Klistra in följande kod i cellen **Cmd 1**. (Den här koden sparas automatiskt i redigeraren.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Utforska data

Gå tillbaka till Databricks-arbetsytan och välj ikonen **Recent** (Senaste) i navigeringsfältet.

1. Välj anteckningsboken **Flight Data Analytics**.
2. Tryck på **Ctrl + Alt + N** för att skapa en ny cell.

Ange vart och ett av följande kodblock i **Cmd 1** och tryck på **Cmd + Retur** för att köra Python-skriptet.

Kör följande skript om du vill visa en lista med CSV-filer som hämtats via AzCopy:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Kör det här skriptet om du vill skapa en ny fil och visa en lista med filerna i mappen *parquet/flights*:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Med dessa kodexempel har du utforskat den hierarkiska strukturen för HDFS med hjälp av data som lagras i ett lagringskonto med Azure Data Lake Storage Gen2 aktiverat.

## <a name="query-the-data"></a>Fråga efter data

Därefter kan du börja fråga efter de data du har laddat upp till ditt lagringskonto. Ange vart och ett av följande kodblock i **Cmd 1** och tryck på **Cmd + Retur** för att köra Python-skriptet.

### <a name="run-simple-queries"></a>Köra exempelfrågor

Kör följande skript om du vill skapa dataramar för dina datakällor:

> [!IMPORTANT]
> Ersätt platshållaren **<YOUR_CSV_FILE_NAME>** med det filnamn som du hämtade i början av denna självstudie.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Kör följande skript om du vill köra analysfrågor mot data:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>Köra komplexa frågor

Du kan köra följande komplexa frågor genom att köra ett segment i taget i anteckningsboken och se resultatet.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för lagringskontot och sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

[!div class="nextstepaction"] 
> [Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

