---
title: 'Självstudier: Lär dig hur du utför extraherings-, inläsnings- och överföringsåtgärder med hjälp av Azure Databricks'
description: I den här självstudien lär du dig hur du extraherar data från förhandsversionen av Data Lake Storage Gen2 till Azure Databricks, transformerar dessa data och läser in dem i Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e4e75c65178c4bbedcf781c2fbf2149a94a702cd
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321202"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks

I den här självstudien ska du utföra en ETL-åtgärd (extrahera, transformera och läsa in data) med hjälp av Azure Databricks. Du flyttar data från ett Azure Data Lake Storage Gen2-aktiverat Azure Storage-konto till Azure SQL Data Warehouse.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta.
> * Skapa ett Spark-kluster i Azure Databricks.
> * Skapa ett Azure Data Lake Storage Gen2-kompatibelt konto.
> * Ladda upp data till Azure Data Lake Storage Gen2.
> * Skapa en anteckningsbok i Azure Databricks.
> * Extrahera data från Data Lake Storage Gen2.
> * Transformera data med Azure Databricks.
> * Läs in data till Azure SQL Data Warehouse.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* Skapa ett Azure SQL-informationslager, skapa en brandväggsregel på servernivå och anslut till servern som serveradministratör. Följ instruktionerna i [Snabbstart: Skapa ett Azure SQL-informationslager](../../sql-data-warehouse/create-data-warehouse-portal.md).
* Skapa en databashuvudnyckel för Azure SQL-informationslagret. Följ instruktionerna i artikeln [Skapa en databashuvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Skapa ett Azure Data Lake Storage Gen2-konto](data-lake-storage-quickstart-create-account.md).
* Ladda ned (**small_radio_json.json**) från lagringsplatsen [U-SQL Examples and Issue Tracking](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) och notera sökvägen där du sparar filen.
* Logga in på [Azure-portalen](https://portal.azure.com/).

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

## <a name="create-the-workspace"></a>Skapa arbetsytan

I det här avsnittet skapar du en Azure Databricks-arbetsyta med hjälp av Azure-portalen.

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure-portalen.

    ![Databricks på Azure-portalen](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks på Azure-portalen")

1. Under **Azure Databricks-tjänst** anger du följande värden för att skapa en Databricks-arbetsyta:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan.        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **USA, västra 2**.        |
    |**Prisnivå**     |  Välj **standard**.     |

    ![Skapa en arbetsyta för Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Skapa en arbetsyta för Azure Databricks")

1. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

1. Det tar några minuter att skapa kontot. När kontot skapas visas panelen **Skicka distribution för Azure Databricks** till höger på portalen. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

    ![Distributionspanel för Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Distributionspanel för Databricks")

## <a name="create-the-spark-cluster"></a>Skapa Spark-klustret

För att utföra åtgärderna i den här självstudien behöver du ett Spark-kluster. Följ stegen nedan för att skapa Spark-klustret.

1. På Azure-portalen går du till Databricks-arbetsytan som du skapade och väljer **Starta arbetsyta**.

1. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks på Azure")

1. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

1. Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    * Ange ett namn för klustret.
    * I den här artikeln ska du skapa ett kluster med **5.1**-körningen.
    * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Om klustret inte används anger du en varaktighet (i minuter) för att avsluta klustret.

1. Välj **Skapa kluster**.

När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-file-system"></a>Skapa ett filsystem

För att lagra data i ditt Data Lake Storage Gen2-lagringskonto måste du skapa ett filsystem.

Först måste du skapa en anteckningsbok på Azure Databricks-arbetsytan och köra kodfragment för att skapa filsystemet i lagringskontot.

1. På [Azure-portalen](https://portal.azure.com) går du till Azure Databricks-arbetsytan som du skapade och väljer **Starta arbetsyta**.

2. Välj **Arbetsyta** till vänster. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa en anteckningsbok i Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Scala** som språk och välj sedan det Spark-kluster som du skapade tidigare.

    ![Ange information för en anteckningsbok i Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Ange information för en anteckningsbok i Databricks")

    Välj **Skapa**.

4. Kopiera och klistra in följande kodblock i den första cellen, men kör inte den här koden än.

    ```scala
    val configs = Map(
    "fs.azure.account.auth.type" -> "OAuth",
    "fs.azure.account.oauth.provider.type" -> "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
    "fs.azure.account.oauth2.client.id" -> "<application-id>",
    "fs.azure.account.oauth2.client.secret" -> "<authentication-key>"),
    "fs.azure.account.oauth2.client.endpoint" -> "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
    "fs.azure.createRemoteFileSystemDuringInitialization"->"true")

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<directory-name>",
    mountPoint = "/mnt/<mount-name>",
    extraConfigs = configs)
    ```

5. I det här kodblocket ersätter du platshållarvärdena `storage-account-name`, `application-id`, `authentication-id` och `tenant-id` med de värden som du hämtade när du genomförde stegen i avsnitten [Spara lagringskontokonfiguration](#config) och [Skapa ett huvudnamn för tjänsten](#service-principal) i den här artikeln. Ställ in platshållarvärdena `file-system-name`, `directory-name` och `mount-name` på de namn du vill ge filsystemet, katalogen och monteringspunkten.

6. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

## <a name="upload-the-sample-data"></a>Ladda upp exempeldata

Nästa steg är att ladda upp en exempeldatafil till lagringskontot som senare transformeras i Azure Databricks.

Ladda upp de exempeldata som du hämtade till ditt lagringskonto. Metoden du använder för att ladda upp data till lagringskontot skiljer sig åt beroende på om den hierarkiska namnrymden är aktiverad.

Du kan använda Azure Data Factory, distp eller AzCopy (version 10) för att utföra överföringen. AzCopy version 10 är för närvarande endast tillgänglig som en förhandsversion. Använd AzCopy genom att klistra in följande kod i ett kommandofönster:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Extrahera data

För att arbeta med exempeldata i Databricks måste du extrahera data från ditt lagringskonto.

Gå tillbaka till Databricks-anteckningsboken och ange följande kod i en ny cell i anteckningsboken.

Lägg till följande kodfragment i en tom kodcell. Ersätt platshållarna inom hakparentes med de värden som du sparade tidigare från lagringskontot.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Välj Skift+Retur för att köra koden.

Du kan nu läsa in json-exempelfilen som en dataram i Azure Databricks. Klistra in följande kod i en ny cell. Ersätt platshållarna inom hakparentes med dina värden.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Välj Skift+Retur för att köra koden.

Kör följande kod om du vill se dataramens innehåll:

```scala
df.show()
```

Du bör se utdata som liknar följande fragment:

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

Du har nu extraherat data från Azure Data Lake Storage Gen2 till Azure Databricks.

## <a name="transform-the-data"></a>Omvandla data

Filen **small_radio_json.json** med exempelrådata fångar målgruppen för en radiostation och har flera olika kolumner. I det här avsnittet ska du transformera data så att endast specifika kolumner hämtas från datamängden.

Börja med att bara hämta kolumnerna **firstName**, **lastName**, **gender**, **location** och **level** från den dataram som du skapade.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

Du får utdata som liknar följande kodfragment:

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

Du kan transformera dessa data ytterligare genom att t.ex. ändra namnet på kolumnen **nivå** till **prenumerationstyp**.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

Du får utdata som liknar följande kodfragment.

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>Läs in data

I det här avsnittet kommer du att överföra de data du transformerade till Azure SQL Data Warehouse. Du använder Azure SQL Data Warehouse-anslutningen för Azure Databricks för att överföra en dataram direkt som en tabell i ett SQL-informationslager.

SQL Data Warehouse-anslutningen använder Azure Blob Storage som temporär lagring för att överföra data mellan Azure Databricks och Azure SQL Data Warehouse. Så du börjar med att tillhandahålla den konfiguration som ska ansluta till lagringskontot. Du måste redan ha skapat kontot som en del av förhandskraven för den här artikeln.

Ange konfigurationen så att du får åtkomst till Azure Storage-kontot från Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Ange en tillfällig mapp som ska användas när data flyttas mellan Azure Databricks och Azure SQL Data Warehouse.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Kör följande fragment om du vill lagra åtkomstnycklar för Azure Blob Storage i konfigurationen. Den här åtgärden gör att du inte behöver lagra åtkomstnyckeln i anteckningsboken som oformaterad text.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Ange värdena för att ansluta till Azure SQL Data Warehouse-instansen. Innan du börjar måste du skapa ett SQL-informationslager.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

Kör följande kodfragment för att läsa in den transformerande dataramen **renamedColumnsDf** som en tabell i ett SQL-informationslager. Det här kodfragmentet skapar en tabell med namnet **SampleTable** i SQL-databasen.

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

Anslut till SQL-databasen och kontrollera att du ser en databas med namnet **SampleTable**.

![Verifiera exempeltabellen](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verifiera exempeltabellen")

Verifiera tabellens innehåll genom att köra en urvalsfråga. Tabellen bör ha samma data som dataramen **renamedColumnsDF**.

![Verifiera exempeltabellens innehåll](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verifiera exempeltabellens innehåll")

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört självstudien kan du avsluta klustret. Välj **Kluster** till vänster på Azure Databricks-arbetsytan. Peka på ellipsen (...) under **Åtgärder** och välj ikonen **Avsluta** för att avsluta klustret.

![Stoppa ett Databricks-kluster](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt om du markerade kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I så fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie och lär dig hur du strömmar realtidsdata till Azure Databricks med hjälp av Azure Event Hubs.

> [!div class="nextstepaction"]
>[Strömma data till Azure Databricks med hjälp av Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
