---
title: 'Självstudie: Utföra ETL-åtgärder med Azure Databricks'
description: Lär dig hur du extraherar data från Data Lake Storage Gen2 till Azure Databricks, transformerar dessa data och läser in dem i Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 02/15/2019
ms.openlocfilehash: 6ec32a40cea4f95d9225134cfb36d4930245d1c5
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750607"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks

I den här självstudien utför du en ETL-åtgärd (extrahera, transformera och läsa in data) med hjälp av Azure Databricks. Du extraherar data från Azure Data Lake Storage Gen2 till Azure Databricks, kör transformationer av data i Azure Databricks och läser sedan in dessa transformerade data i Azure SQL Data Warehouse.

Stegen i den här självstudiekursen använder SQL Data Warehouse-anslutningsappen så att Azure Databricks kan överföra data till Azure Databricks. Den här anslutningsappen använder i sin tur Azure Blob Storage som temporär lagring för de data som överförs mellan ett Azure Databricks-kluster och Azure SQL Data Warehouse.

Följande bild visar programflödet:

![Azure Databricks med Data Lake Store och SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks med Data Lake Store och SQL Data Warehouse")

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Databricks-tjänst.
> * Skapa ett Spark-kluster i Azure Databricks.
> * Skapa ett filsystem i Data Lake Storage Gen2-kontot.
> * Ladda upp exempeldata till Azure Data Lake Storage Gen2-kontot.
> * Skapa ett huvudnamn för tjänsten.
> * Extrahera data från Azure Data Lake Storage Gen2-kontot.
> * Transformera data med Azure Databricks.
> * Läs in data till Azure SQL Data Warehouse.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför de här uppgifterna innan du startar självstudien:

* Skapa ett Azure SQL-informationslager, skapa en brandväggsregel på servernivå och anslut till servern som serveradministratör. Gå till [Snabbstart: Skapa ett Azure SQL-informationslager](../sql-data-warehouse/create-data-warehouse-portal.md).

* Skapa en databashuvudnyckel för Azure SQL-informationslagret. Se [Skapa en databashuvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Skapa ett Azure Blob Storage-konto och en container i det. Få dessutom åtkomst till lagringskontot genom att hämta åtkomstnyckeln. Gå till [Snabbstart: Skapa ett Azure Blob Storage-konto](../storage/blobs/storage-quickstart-blobs-portal.md).

* Skapa ett Azure Data Lake Storage Gen2-lagringskonto. Se [Skapa ett Azure Data Lake Storage Gen2-konto](../storage/blobs/data-lake-storage-quickstart-create-account.md).

*  Skapa ett huvudnamn för tjänsten. Se [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Det finns några saker som du måste göra när du utför stegen i den här artikeln.

   * När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) i artikeln ska du tilldela rollen **Storage Blob Data-deltagare** till tjänstens huvudnamn.

     > [!IMPORTANT]
     > Se till att tilldela rollen i omfånget för Data Lake Storage Gen2-lagringskontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

   * När du utför stegen i avsnittet [Hämta värden för att logga in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i artikeln klistrar du in värdena för klient-ID, program-ID och autentiseringsnyckel i en textfil. Du kommer att behöva dem snart.

* Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Samla in den information som du behöver

Se till att du slutför kraven för den här självstudien.

   Innan du börjar bör du ha följande information:

   :heavy_check_mark:  Databasens namn, databasserverns namn, användarnamn och lösenord för ditt Azure SQL-informationslager.

   :heavy_check_mark:  Åtkomstnyckeln för ditt Blog Storage-konto.

   :heavy_check_mark:  Namnet på ditt Data Lake Storage Gen2-lagringskonto.

   :heavy_check_mark:  Klientorganisations-ID för din prenumeration.

   :heavy_check_mark:  Program-ID för den app som du registrerade med Azure Active Directory (AD Azure).

   :heavy_check_mark:  Autentiseringsnyckeln för den app som du registrerade med Azure AD.

## <a name="create-an-azure-databricks-service"></a>Skapa en Azure Databricks-tjänst

I det här avsnittet skapar du en Azure Databricks-tjänst i Azure Portal.

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure-portalen.

    ![Databricks på Azure-portalen](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks på Azure-portalen")

2. Ange följande värden under **Azure Databricks-tjänst** för att skapa en Databricks-tjänst:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan.        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **USA, västra 2**.  För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).      |
    |**Prisnivå**     |  Välj **standard**.     |

3. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

4. Det tar några minuter att skapa kontot. När kontot skapas visas panelen **Skicka distribution för Azure Databricks** till höger på portalen. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

    ![Distributionspanel för Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Distributionspanel för Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Skapa ett Spark-kluster i Azure Databricks

1. Gå till Databricks-tjänsten du skapade i Azure Portal och välj **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

4. Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    * Ange ett namn för klustret.

    * I den här artikeln ska du skapa ett kluster med **5.1**-körningen.

    * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Om klustret inte används anger du en varaktighet (i minuter) för att avsluta klustret.

    * Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Skapa ett filsystem i Azure Data Lake Storage Gen2-kontot

I det här avsnittet skapar du en anteckningsbok på Azure Databricks-arbetsytan och kör sedan kodavsnitt för att konfigurera lagringskontot

1. Gå till Azure Databricks-tjänsten du skapade i [Azure Portal](https://portal.azure.com) och välj **Starta arbetsyta**.

2. Välj **Arbetsyta** till vänster. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa en anteckningsbok i Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Scala** som språk och välj sedan det Spark-kluster som du skapade tidigare.

    ![Ange information för en anteckningsbok i Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Ange information för en anteckningsbok i Databricks")

4. Välj **Skapa**.

5. Kopiera och klistra in följande kodblock i den första cellen.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. I det här kodblocket ersätter du platshållarvärdena `application-id`, `authentication-id`, `tenant-id` och `storage-account-name` i det här kodblocket med de värden som du hämtade när du slutförde förutsättningarna för den här självstudien. Ersätt platshållarvärdet `file-system-name` med det namn som du vill ge filsystemet.

   * `application-id` och `authentication-id` kommer från den app som du registrerade med Active Directory som en del av skapandet av ett tjänsthuvudnamn.

   * `tenant-id` kommer från din prenumeration.

   * `storage-account-name` är namnet på ditt Azure Data Lake Storage Gen2-lagringskonto.

7. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Mata in exempeldata i Azure Data Lake Storage Gen2-kontot

Innan du börjar med det här avsnittet måste du slutföra följande krav:

Ange följande kod i en cell i en arbetsbok:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Kör koden genom att trycka på **SKIFT + RETUR** i cellen.

I en ny cell nedanför denna anger du följande kod, och ersätter värdena inom hakparentes med samma värden som du använde tidigare:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Kör koden genom att trycka på **SKIFT + RETUR** i cellen.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrahera data från Azure Data Lake Storage Gen2-kontot

1. Du kan nu läsa in json-exempelfilen som en dataram i Azure Databricks. Klistra in följande kod i en ny cell. Ersätt platshållarna inom hakparentes med dina värden.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Ersätt platshållarvärdet `file-system-name` med det namn som du gav filsystemet i Storage Explorer.

   * Ersätt platshållaren `storage-account-name` med namnet på ditt lagringskonto.

2. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

3. Kör följande kod om du vill se dataramens innehåll:

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

Som tidigare nämnts använder SQL Data Warehouse-anslutningen Azure Blob Storage som temporär lagring vid överföring av data mellan Azure Databricks och Azure SQL Data Warehouse. Så du börjar med att tillhandahålla den konfiguration som ska ansluta till lagringskontot. Du måste redan ha skapat kontot som en del av de nödvändiga förutsättningarna för den här artikeln.

1. Ange konfigurationen så att du får åtkomst till Azure Storage-kontot från Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Ange en tillfällig mapp som ska användas när data flyttas mellan Azure Databricks och Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Kör följande fragment om du vill lagra åtkomstnycklar för Azure Blob Storage i konfigurationen. Den här åtgärden gör att du inte behöver lagra åtkomstnyckeln i anteckningsboken som oformaterad text.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
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
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
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

   ![Verifiera exempeltabellen](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verifiera exempeltabellen")

7. Verifiera tabellens innehåll genom att köra en urvalsfråga. Tabellen bör ha samma data som dataramen **renamedColumnsDF**.

    ![Verifiera exempeltabellens innehåll](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verifiera exempeltabellens innehåll")

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört självstudien kan du avsluta klustret. Välj **Kluster** till vänster på Azure Databricks-arbetsytan. Peka på ellipsen (...) under **Åtgärder** och välj ikonen **Avsluta** för att avsluta klustret.

![Stoppa ett Databricks-kluster](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt om du markerade kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I så fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure Databricks-tjänst
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa en anteckningsbok i Azure Databricks
> * Extrahera data från ett Data Lake Storage Gen2-konto
> * Transformera data med Azure Databricks
> * Läs in data till Azure SQL Data Warehouse

Gå vidare till nästa självstudiekurs och lär dig mer om att strömma realtidsdata i Azure Databricks med Azure Event Hubs.

> [!div class="nextstepaction"]
>[Strömma data i Azure Databricks med Event Hubs](databricks-stream-from-eventhubs.md)
