---
title: Få åtkomst till Azure Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark| Microsoft Docs
description: Lär dig hur du kör Spark-frågor i ett Azure Databricks-kluster för att komma åt data i ett Azure Data Lake Storage Gen2-lagringskonto.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: 88a05eb8fa59740012ca6c7a8d8508d565854dc7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974164"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Självstudie: Få åtkomst till Azure Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark

I den här självstudien får du lära dig hur du kör Spark-frågor i ett Azure Databricks-kluster för att fråga efter data i ett Azure-lagringskonto med förhandsversionen av Azure Data Lake Storage Gen2 aktiverad.

> [!div class="checklist"]
> * Skapa ett Databricks-kluster
> * Mata in ostrukturerade data i ett lagringskonto
> * Köra analyser på dina data i Blob Storage

## <a name="prerequisites"></a>Krav

Den här självstudien visar hur du använder och frågar efter flyginformation som är tillgänglig från [USA:s transportdepartement](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Ladda ned minst två års flyginformation (markera alla fält) och spara resultatet på din dator. Se till att anteckna filnamnet och sökvägen för din nedladdning. Du behöver den informationen i ett senare steg.

> [!NOTE]
> Klicka på kryssrutan **Prezipped file** (Förkomprimerad fil) för att markera alla datafält. Nedladdningen är många GB, men den här mängden data krävs för att kunna göra analyser.

## <a name="create-an-azure-storage-account-with-analytic-capabilities"></a>Skapa ett Azure Storage-konto med analysfunktioner

Börja med att skapa ett nytt [lagringskonto med analysfunktioner](data-lake-storage-quickstart-create-account.md) och ge det unikt namn. Gå sedan till lagringskontot för att hämta konfigurationsinställningar.

1. Under **Inställningar** klickar du på **Åtkomstnycklar**.
2. Klicka på **Kopiera** bredvid **key1** för att kopiera nyckelvärdet.

Både kontonamnet och nyckeln behövs i senare steg i den här självstudien. Öppna ett textredigeringsprogram och spara kontonamnet och nyckeln för framtida bruk.

## <a name="create-a-databricks-cluster"></a>Skapa ett Databricks-kluster

Nästa steg är att skapa ett [Databricks-kluster](https://docs.azuredatabricks.net/) för att skapa en dataarbetsyta.

1. Skapa en [Databricks-tjänst](https://ms.portal.azure.com/#create/Microsoft.Databricks) och ge den namnet **myFlightDataService** (se till att markera kryssrutan *Fäst på instrumentpanelen* när du skapar tjänsten).
2. Klicka på **Launch Workspace** (Starta arbetsyta) för att öppna arbetsytan i ett nytt webbläsarfönster.
3. Klicka på **Clusters** (Kluster) i det vänstra navigeringsfältet.
4. Klicka på **Create Cluster** (Skapa kluster).
5. Ange **myFlightDataCluster** i fältet *Klusternamn*.
6. Välj **Standard_D8s_v3** i fältet *Worker Type* (Typ av arbetare).
7. Ändra värdet för **Min Workers** (Minsta antal arbetare) till *4*.
8. Klicka på **Create Cluster** (Skapa kluster) längst upp på sidan (den här processen kan ta upp till fem minuter att slutföra).
9. När processen är klar väljer du **Azure Databricks** längst upp till vänster i navigeringsfältet.
10. Välj **Notebook** (Anteckningsbok) under avsnittet **New** (Ny) på den nedre halvan av sidan.
11. Ange namnet på ditt val i fältet **Name** (Namn) och välj **Python** som språk.
12. Alla andra fält kan lämnas med standardvärdena.
13. Välj **Skapa**.
14. Klistra in följande kod i cellen **Cmd 1**. Kom ihåg att ersätta platshållarna inom hakparentes i exemplet med dina egna värden:

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
15. Kör kodcellen genom att trycka på **SKIFT + RETUR**.

## <a name="ingest-data"></a>Mata in data

### <a name="copy-source-data-into-the-storage-account"></a>Kopiera källdata till lagringskontot

Sedan använder du AzCopy för att kopiera data från *.csv*-filen till Azure Storage. Öppna kommandotolken och ange följande kommandon. Se till att ersätta platshållarna `<DOWNLOAD_FILE_PATH>` och `<ACCOUNT_KEY>` med motsvarande värden som du sparade i ett tidigare steg.

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
6. Klistra in följande kod i cellen **Cmd 1** (den här koden sparas automatiskt i redigeraren).

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Utforska data med HDFS (Hadoop Distributed File System)

Gå tillbaka till Databricks-arbetsytan och klicka på ikonen **Recent** (Senaste) i navigeringsfältet.

1. Klicka på anteckningsboken **Flight Data Analytics**.
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

### <a name="simple-queries"></a>Exempelfrågor

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
### <a name="complex-queries"></a>Komplexa frågor

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

## <a name="next-steps"></a>Nästa steg

* [Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
