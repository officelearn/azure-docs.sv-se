---
title: 'Självstudier: Få åtkomst till Azure Data Lake Storage Gen2-data med Azure Databricks med hjälp av Spark | Microsoft Docs'
description: Den här självstudiekursen visar hur du kör Spark frågor på en Azure Databricks-kluster för att komma åt data i ett Azure Data Lake Storage Gen2 storage-konto.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: dineshm
ms.openlocfilehash: 7f712bcf3e82005480d4960484cb0ea3ad51fbff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482728"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Självstudier: Få åtkomst till Data Lake Storage Gen2-data med Azure Databricks med hjälp av Spark

Den här självstudien visar hur du ansluter ditt Azure Databricks-kluster till data som lagras i ett Azure-lagringskonto som har Azure Data Lake Storage Gen2 aktiverat. Med den här anslutningen kan du internt köra frågor och analyser från klustret på dina data.

I den här kursen ska du:

> [!div class="checklist"]
> * Skapa ett Databricks-kluster
> * Mata in ostrukturerade data i ett lagringskonto
> * Köra analyser på data i Blob Storage

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa ett Azure Data Lake Storage Gen2-konto.

  Se [skapa ett konto för Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Se till att ditt användarkonto har tilldelats rollen [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Installera AzCopy v10. Läs mer i [Överföra data med AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Skapa ett huvudnamn för tjänsten. Se [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Det finns några saker som du måste göra när du utför stegen i den här artikeln.

  :heavy_check_mark: När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) i artikeln ska du tilldela rollen **Storage Blob Data-deltagare** till tjänstens huvudnamn.

  > [!IMPORTANT]
  > Se till att tilldela rollen i omfånget för Data Lake Storage Gen2-lagringskontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

  :heavy_check_mark: När du utför stegen i avsnittet [Hämta värden för att logga in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i artikeln klistrar du in värdena för klient-ID, program-ID och autentiseringsnyckel i en textfil. Du kommer att behöva dem snart.

### <a name="download-the-flight-data"></a>Ladda ned flygdata

I den här självstudien används flygdata från Bureau of Transportation Statistics för att demonstrera hur du utför en ETL-åtgärd. Du måste hämta dessa data för att kunna gå självstudien.

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Markera kryssrutan **Prezipped file** (Förkomprimerad fil) för att markera alla datafält.

3. Klicka på knappen **Ladda ned** och spara resultaten lokalt. 

4. Packa upp innehållet i den komprimerade filen och anteckna filnamnet och sökvägen. Du behöver den här informationen i ett senare steg.

## <a name="create-an-azure-databricks-service"></a>Skapa en Azure Databricks-tjänst

I det här avsnittet skapar du en Azure Databricks-tjänst i Azure Portal.

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure-portalen.

    ![Databricks på Azure-portalen](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks på Azure-portalen")

2. Ange följande värden under **Azure Databricks-tjänst** för att skapa en Databricks-tjänst:

    |Egenskap   |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan.  |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **USA, västra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).       |
    |**Prisnivå**     |  Välj **standard**.     |

    ![Skapa en arbetsyta för Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Skapa en Azure Databricks-tjänst")

3. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

4. Det tar några minuter att skapa kontot. När kontot skapas visas panelen **Skicka distribution för Azure Databricks** till höger på portalen. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

    ![Distributionspanel för Databricks](./media/data-lake-storage-use-databricks-spark/databricks-deployment-tile.png "Distributionspanel för Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Skapa ett Spark-kluster i Azure Databricks

1. Gå till Databricks-tjänsten du skapade i Azure Portal och välj **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

4. Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    * Ange ett namn för klustret.

    * I den här artikeln ska du skapa ett kluster med **5.1**-körningen.

    * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Om klustret inte används anger du en varaktighet (i minuter) för att avsluta klustret.

    * Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-file-system-and-mount-it"></a>Skapa ett filsystem och montera det

I det här avsnittet skapar du ett filsystem och en mapp i lagringskontot.

1. Gå till Azure Databricks-tjänsten du skapade i [Azure Portal](https://portal.azure.com) och välj **Starta arbetsyta**.

2. Välj **Arbetsyta** till vänster. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa en anteckningsbok i Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Python** som språk och välj sedan det Spark-kluster du skapade tidigare.

4. Välj **Skapa**.

5. Kopiera och klistra in följande kodblock i den första cellen, men kör inte den här koden än.

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

18. I det här kodblocket ersätter du platshållarvärdena `application-id`, `authentication-id`, `tenant-id` och `storage-account-name` i det här kodblocket med de värden som du hämtade när du slutförde förutsättningarna för den här självstudien. Ersätt platshållarvärdet `file-system-name` med det namn som du vill ge filsystemet.

   * `application-id` och `authentication-id` kommer från den app som du registrerade med Active Directory som en del av skapandet av ett tjänsthuvudnamn.

   * `tenant-id` kommer från din prenumeration.

   * `storage-account-name` är namnet på ditt Azure Data Lake Storage Gen2-lagringskonto.

   > [!NOTE]
   > I en produktionsinställning bör du överväga att lagra din autentiseringsnyckel i Azure Databricks. Sedan lägger du till en lookup-nyckel i kodblocket i stället för autentiseringsnyckeln. När du har slutfört den här snabbstarten kan du läsa artikeln [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) på Azure Databricks-webbplatsen för att se exemplen för den här metoden.

19. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

   Lämna den här anteckningsboken öppen eftersom du ska lägga till kommandon i den senare.

## <a name="ingest-data"></a>Mata in data

### <a name="copy-source-data-into-the-storage-account"></a>Kopiera källdata till lagringskontot

Använd AzCopy till att kopiera data från *.csv*-filen till Data Lake Storage Gen2-kontot.

1. Öppna en kommandotolk och ange följande kommandon för att logga in på lagringskontot.

   ```bash
   azcopy login
   ```

   Följ anvisningarna som visas i kommandotolken för att autentisera kontot.

2. Kopiera data från *.csv*-filen med följande kommando.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * Ersätt den `<csv-folder-path>` platshållarvärdet med sökvägen till den *.csv* fil.

   * Ersätt platshållarvärdet `storage-account-name` med namnet på ditt lagringskonto.

   * Ersätt platshållaren `file-system-name` med ett namn som du vill ge ditt filsystem.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Använda Databricks-anteckningsboken för att konvertera CSV till Parquet

Lägg till en ny cell i anteckningsboken du skapade tidigare och klistra in följande kod i cellen. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Utforska data

Klistra in följande kod i en ny cell för att få en lista med CSV-filer som laddats upp via AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Kör det här skriptet om du vill skapa en ny fil och visa en lista med filerna i mappen *parquet/flights*:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Med dessa kodexempel har du utforskat den hierarkiska strukturen för HDFS med hjälp av data som lagras i ett lagringskonto med Azure Data Lake Storage Gen2 aktiverat.

## <a name="query-the-data"></a>Fråga efter data

Därefter kan du börja fråga efter de data du har laddat upp till ditt lagringskonto. Ange vart och ett av följande kodblock i **Cmd 1** och tryck på **Cmd + Retur** för att köra Python-skriptet.

Skapa dataramar för dina datakällor med följande skript:

* Ersätt den `<csv-folder-path>` platshållarvärdet med sökvägen till den *.csv* fil.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
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

Med det här skriptet kör du några grundläggande analysfrågor mot data.

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
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för lagringskontot och sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
