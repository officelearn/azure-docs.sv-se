---
title: 'Självstudier: Lär dig hur du utför extraherings-, inläsnings- och överföringsåtgärder med hjälp av Azure Databricks'
description: I den här självstudien lär du dig hur du extraherar data från förhandsversionen av Data Lake Storage Gen2 till Azure Databricks, transformerar dessa data och läser in dem i Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4d0ff4941405f09c2231b9cde16f4e75e2b88b4b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251681"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks

I den här självstudien utför du en ETL-åtgärd (extrahera, transformera och läsa in data) med hjälp av Azure Databricks. Du extraherar data från Azure Data Lake Storage Gen2 till Azure Databricks, kör transformationer av data i Azure Databricks och läser sedan in dessa transformerade data i Azure SQL Data Warehouse.

Stegen i den här självstudiekursen använder SQL Data Warehouse-anslutningsappen så att Azure Databricks kan överföra data till Azure Databricks. Den här anslutningsappen använder i sin tur Azure Blob Storage som temporär lagring för de data som överförs mellan ett Azure Databricks-kluster och Azure SQL Data Warehouse.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta.
> * Skapa ett Spark-kluster i Azure Databricks.
> * Skapa ett filsystem och ladda upp data till Azure Data Lake Storage Gen2.
> * Skapa ett huvudnamn för tjänsten.
> * Extrahera data från Data Lake Store.
> * Transformera data med Azure Databricks.
> * Läs in data till Azure SQL Data Warehouse.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

> [!div class="checklist"]
> * Skapa ett Azure SQL-informationslager, skapa en brandväggsregel på servernivå och anslut till servern som serveradministratör. Gå till [Snabbstart: Skapa ett Azure SQL-informationslager](../../sql-data-warehouse/create-data-warehouse-portal.md).
> * Skapa en databashuvudnyckel för Azure SQL-informationslagret. Se [Skapa en databashuvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
> * Skapa ett Azure Data Lake Storage Gen2-konto. Se [Skapa ett Azure Data Lake Storage Gen2-konto](data-lake-storage-quickstart-create-account.md).
> * Skapa ett Azure Blob Storage-konto och en container i det. Gå till [Snabbstart: Skapa ett Azure Blob Storage-konto](storage-quickstart-blobs-portal.md).
> * Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

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

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Skapa ett Spark-kluster i Azure Databricks

1. På Azure-portalen går du till Databricks-arbetsytan som du skapade och väljer **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

4. Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    * Ange ett namn för klustret.

    * I den här artikeln ska du skapa ett kluster med **5.1**-körningen.

    * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Om klustret inte används anger du en varaktighet (i minuter) för att avsluta klustret.

    * Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-file-system-and-upload-sample-data"></a>Skapa ett filsystem och ladda upp exempeldata

Först skapar du ett filsystem i ditt Data Lake Storage Gen2-konto. Sedan kan du ladda upp en exempeldatafil till Data Lake Store. Du kan använda den här filen senare i Azure Databricks om du vill köra vissa transformationer.

1. Ladda ned exempeldatafilen [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) till det lokala filsystemet.

2. Från [Azure-portalen](https://portal.azure.com/) går du till det Data Lake Storage Gen2-konto som du skapade som en förutsättning för den här självstudien.

3. På sidan **Översikt** i lagringskontot väljer du **Öppna i Explorer**.

   ![Öppna Storage Explorer](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer.png "Öppna Storage Explorer")

4. Öppna Storage Explorer genom att välja **Öppna Azure Storage Explorer**.

   ![Öppna Storage Explorer för andra gången](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer-2.png "Öppna Storage Explorer för andra gången")

   Storage Explorer öppnas. Du kan skapa ett filsystem och ladda upp exempeldata med hjälp av vägledningen i det här ämnet: [Snabbstart: Använda Azure Storage Explorer för att hantera data i ett Azure Data Lake Storage Gen2-konto](data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa ett huvudnamn för tjänsten genom att följa anvisningarna i det här avsnittet: [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Det finns några saker som du måste göra när du utför stegen i den här artikeln.

:heavy_check_mark: När du utför stegen i avsnittet [Skapa ett Azure Active Directory-program](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) i artikeln måste du ange slutpunkts-URI:n som du nyss tog fram i fältet **Inloggnings-URL** i dialogrutan **Skapa**.

:heavy_check_mark: När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) i artikeln måste du tilldela programmet till **deltagarrollen för bloblagring**.

:heavy_check_mark: När du utför stegen i avsnittet [Hämta värden för att logga in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i artikeln klistrar du in värdena för klient-ID, program-ID och autentiseringsnyckel i en textfil. Du kommer att behöva dem snart.
Först måste du skapa en anteckningsbok på Azure Databricks-arbetsytan och köra kodfragment för att skapa filsystemet i lagringskontot.

## <a name="extract-data-from-the-data-lake-store"></a>Extrahera data från Data Lake Store

I det här avsnittet skapar du en anteckningsbok på Azure Databricks-arbetsytan och kör sedan kodavsnitt för att extrahera data från Data Lake Store till Azure Databricks.

1. På [Azure-portalen](https://portal.azure.com) går du till Azure Databricks-arbetsytan som du skapade och väljer **Starta arbetsyta**.

2. Välj **Arbetsyta** till vänster. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa en anteckningsbok i Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Scala** som språk och välj sedan det Spark-kluster som du skapade tidigare.

    ![Ange information för en anteckningsbok i Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Ange information för en anteckningsbok i Databricks")

4. Välj **Skapa**.

5. Kopiera och klistra in följande kodblock i den första cellen.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. I det här kodblocket ersätter du platshållarvärdena `application-id`, `authentication-id` och `tenant-id` med de värden som du hämtade när du genomförde stegen i [Set aside storage account configuration](#config) (Spara undan konfiguration av lagringskonto). Ersätt platshållarvärdet `storage-account-name` med namnet på ditt lagringskonto.

7. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

8. Du kan nu läsa in json-exempelfilen som en dataram i Azure Databricks. Klistra in följande kod i en ny cell. Ersätt platshållarna inom hakparentes med dina värden.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Ersätt platshållarvärdet `file-system-name` med det namn som du gav filsystemet i Storage Explorer.

   * Ersätt platshållaren `storage-account-name` med namnet på ditt lagringskonto.

9. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

10. Kör följande kod om du vill se dataramens innehåll:

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

## <a name="transform-data-in-azure-databricks"></a>Transformera data med Azure Databricks

Filen **small_radio_json.json** med exempelrådata fångar målgruppen för en radiostation och har flera olika kolumner. I det här avsnittet ska du transformera data så att endast specifika kolumner hämtas från datamängden.

1. Börja med att bara hämta kolumnerna **firstName**, **lastName**, **gender**, **location** och **level** från den dataram som du skapade.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
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

2. Du kan transformera dessa data ytterligare genom att t.ex. ändra namnet på kolumnen **nivå** till **prenumerationstyp**.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

I det här avsnittet kommer du att överföra de data du transformerade till Azure SQL Data Warehouse. Du använder Azure SQL Data Warehouse-anslutningen för Azure Databricks för att överföra en dataram direkt som en tabell i ett SQL-informationslager.

Som tidigare nämnts använder SQL Data Warehouse-anslutningen Azure Blob Storage som temporär lagring vid överföring av data mellan Azure Databricks och Azure SQL Data Warehouse. Så du börjar med att tillhandahålla den konfiguration som ska ansluta till lagringskontot. Du måste redan ha skapat kontot som en del av förhandskraven för den här artikeln.

1. Ange konfigurationen så att du får åtkomst till Azure Storage-kontot från Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val authenticationKey =  "<authentication-key>"
   ```

2. Ange en tillfällig mapp som ska användas när data flyttas mellan Azure Databricks och Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blob-container-name + "@" + blobStorage +"/tempDirs"
   ```

3. Kör följande fragment om du vill lagra åtkomstnycklar för Azure Blob Storage i konfigurationen. Den här åtgärden gör att du inte behöver lagra åtkomstnyckeln i anteckningsboken som oformaterad text.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, authenticationKey)
   ```

4. Ange värdena för att ansluta till Azure SQL Data Warehouse-instansen. Innan du börjar måste du skapa ett SQL-informationslager.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Kör följande kodfragment för att läsa in den transformerande dataramen **renamedColumnsDf** som en tabell i ett SQL-informationslager. Det här kodfragmentet skapar en tabell med namnet **SampleTable** i SQL-databasen.

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

6. Anslut till SQL-databasen och kontrollera att du ser en databas med namnet **SampleTable**.

   ![Verifiera exempeltabellen](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verifiera exempeltabellen")

7. Verifiera tabellens innehåll genom att köra en urvalsfråga. Tabellen bör ha samma data som dataramen **renamedColumnsDF**.

    ![Verifiera exempeltabellens innehåll](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verifiera exempeltabellens innehåll")

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört självstudien kan du avsluta klustret. Välj **Kluster** till vänster på Azure Databricks-arbetsytan. Peka på ellipsen (...) under **Åtgärder** och välj ikonen **Avsluta** för att avsluta klustret.

![Stoppa ett Databricks-kluster](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt om du markerade kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I så fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie och lär dig hur du strömmar realtidsdata till Azure Databricks med hjälp av Azure Event Hubs.

> [!div class="nextstepaction"]
>[Strömma data till Azure Databricks med hjälp av Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
