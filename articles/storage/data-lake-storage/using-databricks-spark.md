---
title: Få åtkomst till Azure Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark| Microsoft Docs
description: Lär dig hur du kör Spark-frågor i ett Azure Databricks-kluster för att komma åt data i ett Azure Data Lake Storage Gen2-lagringskonto.
services: hdinsight,storage
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: fd9dfaa2042cae0923c919f4e76d7b59a170918e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466038"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Självstudie: Få åtkomst till Azure Data Lake Storage Gen2-data (förhandsversion) med Azure Databricks med hjälp av Spark

I den här självstudien får du lära dig hur du kör Spark-frågor i ett Azure Databricks-kluster för att fråga efter data i ett Azure Data Lake Storage Gen2-konto (förhandsversion).

> [!div class="checklist"]
> * Skapa ett Databricks-kluster
> * Mata in ostrukturerade data i ett lagringskonto
> * Köra analyser på dina data i Blob Storage

## <a name="prerequisites"></a>Krav

Den här självstudien visar hur du använder och frågar efter flyginformation som är tillgänglig från [USA:s transportdepartement](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Ladda ned minst två års flyginformation (markera alla fält) och spara resultatet på din dator. Se till att anteckna filnamnet och sökvägen för din nedladdning. Du behöver den informationen i ett senare steg.

> [!NOTE]
> Klicka på kryssrutan **Prezipped file** (Förkomprimerad fil) för att markera alla datafält. Nedladdningen är många GB, men den här mängden data krävs för att kunna göra analyser.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Skapa ett Azure Data Lake Storage Gen2-konto

Börja med att skapa ett nytt [Azure Data Lake Storage Gen2-konto](quickstart-create-account.md) och ge det ett unikt namn. Gå sedan till lagringskontot för att hämta konfigurationsinställningar.

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
14. Klistra in följande kod i cellen **Cmd 1**. Ersätt värdena med de värden som du bevarade från lagringskontot.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfss://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

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
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
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
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Kör det här skriptet om du vill skapa en ny fil och visa en lista med filerna i mappen *parquet/flights*:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Med dessa kodexempel har du utforskat den hierarkiska strukturen för HDFS med hjälp av data som lagras i ett Azure Data Lake Storage Gen2-konto.

## <a name="query-the-data"></a>Fråga efter data

Nu kan du börja fråga efter data som du överfört till Azure Data Lake Storage. Ange vart och ett av följande kodblock i **Cmd 1** och tryck på **Cmd + Retur** för att köra Python-skriptet.

### <a name="simple-queries"></a>Exempelfrågor

Kör följande skript om du vill skapa dataramar för dina datakällor:

> [!IMPORTANT]
> Ersätt platshållaren **<YOUR_CSV_FILE_NAME>** med det filnamn som du hämtade i början av denna självstudie.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

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
#preferrably run this in a separate cmd cell
display(flightDF)
```

Kör följande skript om du vill köra analysfrågor mot data:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
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

* [Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](tutorial-extract-transform-load-hive.md)
