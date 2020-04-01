---
title: 'Självstudiekurs: Implementera datasjöinsamlingsmönstret för att uppdatera en Azure Databricks Delta-tabell | Microsoft-dokument'
description: Den här självstudien visar hur du använder en Event Grid-prenumeration, en Azure-funktion och ett Azure Databricks-jobb för att infoga rader med data i en tabell som lagras i Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303315"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Självstudiekurs: Implementera datasjöinsamlingsmönstret för att uppdatera en Databricks Delta-tabell

Den här självstudien visar hur du hanterar händelser i ett lagringskonto som har ett hierarkiskt namnområde.

Du ska skapa en liten lösning som gör det möjligt för en användare att fylla i en Databricks Delta-tabell genom att ladda upp en csv-fil (kommaavgränsade värden) som beskriver en försäljningsorder. Du skapar den här lösningen genom att ansluta ihop en Event Grid-prenumeration, en Azure-funktion och ett [jobb](https://docs.azuredatabricks.net/user-guide/jobs.html) i Azure Databricks.

I den här kursen ska du:

> [!div class="checklist"]
> * Skapa en Event Grid-prenumeration som anropar en Azure-funktion.
> * Skapa en Azure-funktion som tar emot ett meddelande från en händelse och kör sedan jobbet i Azure Databricks.
> * Skapa ett Databricks-jobb som infogar en kundorder i en Databricks Delta-tabell som finns i lagringskontot.

Vi skapar den här lösningen i omvänd ordning, med början på Azure Databricks-arbetsytan.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Skapa ett lagringskonto som har ett hierarkiskt namnområde (Azure Data Lake Storage Gen2). Den här självstudien `contosoorders`använder ett lagringskonto med namnet . Se till att ditt användarkonto har tilldelats rollen [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

  Se [Skapa ett Azure Data Lake Storage Gen2-konto](data-lake-storage-quickstart-create-account.md).

* Skapa ett huvudnamn för tjänsten. Se [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Det finns några saker som du måste göra när du utför stegen i den här artikeln.

  :heavy_check_mark: När du utför stegen i [avsnittet Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) i artikeln, se till att tilldela rollen Storage **Blob Data Contributor** till tjänstens huvudnamn.

  > [!IMPORTANT]
  > Se till att tilldela rollen i omfånget för Data Lake Storage Gen2-lagringskontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

  :heavy_check_mark: När du utför stegen i avsnittet [Hämta värden för inloggning i](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artikeln klistrar du in klient-ID-, app-ID- och lösenordsvärdena i en textfil. Du behöver dessa värden senare.

## <a name="create-a-sales-order"></a>Skapa en försäljningsorder

Skapa först en csv-fil som beskriver en försäljningsorder och sedan ladda upp filen till lagringskontot. Senare ska du använda data från den här filen för att fylla i den första raden i vår Databricks Delta-tabell.

1. Öppna Azure Storage Explorer. Navigera sedan till ditt lagringskonto och skapa en ny behållare med namnet **data**i avsnittet **Blob Containers** .

   ![datamapp](./media/data-lake-storage-events/data-container.png "datamapp")

   Mer information om hur du använder Storage Explorer finns i [Använda Azure Storage Explorer för att hantera data i ett Azure Data Lake Storage Gen2-konto](data-lake-storage-explorer.md).

2. Skapa en mapp med namnet **indata i** **databehållaren** .

3. Klistra in följande text i en textredigerare.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Spara filen på den lokala datorn och ge den namnet **data.csv**.

5. Ladda upp den här filen till **indatamappen** i Storage Explorer.  

## <a name="create-a-job-in-azure-databricks"></a>Skapa ett jobb i Azure Databricks

I det här avsnittet ska du utföra följande uppgifter:

* Skapa en Azure Databricks-arbetsyta.
* Skapa en anteckningsbok.
* Skapa och fyll i en Databricks Delta-tabell.
* Lägg till kod som infogar rader i tabellen Databricks Delta.
* Skapa ett jobb.

### <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. I Azure-portalen väljer du **Skapa en resurs** > **Analytics** > **Azure Databricks**.

    ![Databricks på Azure-portalen](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks på Azure-portalen")

2. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/data-lake-storage-events/new-databricks-service.png "Skapa en Azure Databricks-arbetsyta")

    Det tar några minuter att skapa arbetsytan. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

### <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. Gå till arbetsytan Azure Databricks som du skapade i [Azure-portalen](https://portal.azure.com)och välj sedan **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. Välj **Nytt** > **kluster**i portalen .

    ![Databricks på Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

    * Ange ett namn för klustret.
    * Se till att markera kryssrutan **Avsluta efter 120 minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.

4. Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Skapa en notebook-fil

1. Välj **Arbetsyta** i det vänstra fönstret. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa anteckningsbok i Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

2. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Python** som språk och välj sedan det Spark-kluster du skapade tidigare.

    ![Skapa anteckningsbok i Databricks](./media/data-lake-storage-events/new-databricks-notebook.png "Skapa anteckningsbok i Databricks")

    Välj **Skapa**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Skapa och fylla i en Databricks Delta-tabell

1. I anteckningsboken som du skapade kopierar och klistrar du in följande kodblock i den första cellen, men kör inte den här koden ännu.  

   Ersätt `appId` `password` `tenant` platshållarvärdena i det här kodblocket med de värden som du samlade in när du slutförde förutsättningarna för den här självstudien.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Den här koden skapar en widget med namnet **source_file**. Senare ska du skapa en Azure-funktion som anropar den här koden och skickar en filsökväg till den widgeten.  Den här koden autentiserar också tjänstens huvudnamn med lagringskontot och skapar vissa variabler som du ska använda i andra celler.

    > [!NOTE]
    > I en produktionsinställning bör du överväga att lagra din autentiseringsnyckel i Azure Databricks. Sedan lägger du till en lookup-nyckel i kodblocket i stället för autentiseringsnyckeln. <br><br>I stället för att använda den `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`här kodraden: använder `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`du följande kodrad: . <br><br>När du har slutfört den här självstudien läser du azure [data lake storage gen2-artikeln](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) på Azure Databricks webbplats för att se exempel på den här metoden.

2. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

3. Kopiera och klistra in följande kodblock i en annan cell och tryck sedan på **SKIFT + RETUR för** att köra koden i det här blocket.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Den här koden skapar tabellen Databricks Delta i ditt lagringskonto och läser sedan in några inledande data från csv-filen som du laddade upp tidigare.

4. När det här kodblocket har körts tar du bort kodblocket från anteckningsboken.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Lägga till kod som infogar rader i tabellen Databricks Delta

1. Kopiera och klistra in följande kodblock i en annan cell, men kör inte cellen.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Den här koden infogar data i en tillfällig tabellvy med hjälp av data från en csv-fil. Sökvägen till csv-filen kommer från indatawidgeten som du skapade i ett tidigare steg.

2. Lägg till följande kod om du vill sammanfoga innehållet i den tillfälliga tabellvyn med tabellen Databricks Delta.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Skapa ett jobb

Skapa ett jobb som kör anteckningsboken som du skapade tidigare. Senare skapar du en Azure-funktion som kör det här jobbet när en händelse utlöses.

1. Klicka på **Jobb**.

2. Klicka på Skapa **jobb**på sidan **Jobb.**

3. Ge jobbet ett namn och `upsert-order-data` välj sedan arbetsboken.

   ![Skapa ett jobb](./media/data-lake-storage-events/create-spark-job.png "Skapa ett jobb")

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Skapa en Azure-funktion som kör jobbet.

1. I det övre hörnet av Databricks arbetsytan väljer du personikonen och väljer sedan **Användarinställningar**.

   ![Hantera kontot](./media/data-lake-storage-events/generate-token.png "Användarinställningar")

2. Klicka på knappen **Generera ny token** och klicka sedan på knappen **Generera.**

   Se till att kopiera token till säker plats. Din Azure-funktion behöver den här token för att autentisera med Databricks så att den kan köra jobbet.
  
3. Välj knappen **Skapa en resurs** som finns i det övre vänstra hörnet av Azure-portalen och välj sedan Beräkna > **Funktionsapp**.

   ![Skapa en Azure-funktion](./media/data-lake-storage-events/function-app-create-flow.png "Skapa Azure-funktion")

4. På sidan **Skapa** i funktionsappen kontrollerar du att du väljer **.NET Core** för körningsstacken och ser till att konfigurera en Application Insights-instans.

   ![Konfigurera funktionsappen](./media/data-lake-storage-events/new-function-app.png "Konfigurera funktionsappen")

5. Klicka på **Konfiguration**på sidan **Översikt** i funktionsappen .

   ![Konfigurera funktionsappen](./media/data-lake-storage-events/configure-function-app.png "Konfigurera funktionsappen")

6. På sidan **Programinställningar** väljer du knappen **Ny programinställning** för att lägga till varje inställning.

   ![Lägga till konfigurationsinställning](./media/data-lake-storage-events/add-application-setting.png "Lägga till konfigurationsinställning")

   Lägg till följande inställningar:

   |Inställningsnamn | Värde |
   |----|----|
   |**DBX_INSTANCE**| Regionen för din databricks arbetsyta. Exempel: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Den personliga åtkomsttoken som du genererade tidigare. |
   |**DBX_JOB_ID**|Identifieraren för det löpande jobbet. I vårt fall är `1`detta värde .|
7. Klicka på knappen Ny funktion på funktionsappens **översiktssida.**

   ![Ny funktion](./media/data-lake-storage-events/new-function.png "Ny funktion")

8. Välj **Azure Event Grid-utlösare**.

   Installera tillägget **Microsoft.Azure.WebJobs.Extensions.EventGrid** om du uppmanas att göra det. Om du måste installera det måste du välja **Azure Event Grid-utlösare** igen för att skapa funktionen.

   Fönstret **Ny funktion** visas.

9. I fönstret **Ny funktion** namnger du funktionen **UpsertOrder**och klickar sedan på knappen **Skapa.**

10. Ersätt innehållet i kodfilen med den här koden och klicka sedan på knappen **Spara:**

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Den här koden tolkar information om lagringshändelsen som har tagits upp och skapar sedan ett meddelande om begäran med url för filen som utlöste händelsen. Som en del av meddelandet skickar funktionen ett värde till **den source_file** widget som du skapade tidigare. Funktionskoden skickar meddelandet till Databricks-jobbet och använder den token som du fick tidigare som autentisering.

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

I det här avsnittet skapar du en Event Grid-prenumeration som anropar Azure-funktionen när filer överförs till lagringskontot.

1. Klicka på knappen Lägg **till prenumerationsprenumerera för händelserutnät** på funktionskodssidan.

   ![Ny händelseprenumeration](./media/data-lake-storage-events/new-event-subscription.png "Ny händelseprenumeration")

2. På sidan **Skapa händelseprenumeration** namnger du prenumerationen och använder sedan fälten på sidan för att välja ditt lagringskonto.

   ![Ny händelseprenumeration](./media/data-lake-storage-events/new-event-subscription-2.png "Ny händelseprenumeration")

3. I **listrutan Filter till händelsetyper** väljer du de **skapade Blob-objekten**och **Blob-borttagna** händelser och klickar sedan på knappen **Skapa.**

## <a name="test-the-event-grid-subscription"></a>Testa prenumerationen på Event Grid

1. Skapa en `customer-order.csv`fil med namnet, klistra in följande information i filen och spara den på den lokala datorn.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. I Storage Explorer överför du filen till **indatamappen** för ditt lagringskonto.

   Om du laddar upp en fil väcks händelsen **Microsoft.Storage.BlobCreated.** Event Grid meddelar alla prenumeranter på den händelsen. I vårt fall är Azure-funktionen den enda prenumeranten. Azure-funktionen tolkar händelseparametrarna för att avgöra vilken händelse som inträffade. Den skickar sedan url:en till Databricks Job. Databricks-jobbet läser filen och lägger till en rad i tabellen Databricks Delta som finns i ditt lagringskonto.

3. Om du vill kontrollera om jobbet lyckades öppnar du databricks-arbetsytan, klickar på knappen **Jobb** och öppnar sedan jobbet.

4. Välj jobbet för att öppna jobbsidan.

   ![Spark jobb](./media/data-lake-storage-events/spark-job.png "Spark jobb")

   När jobbet är klart visas en slutförandestatus.

   ![Jobbet har slutförts](./media/data-lake-storage-events/spark-job-completed.png "Jobbet har slutförts")

5. I en ny arbetsbokscell kör du den här frågan i en cell för att se den uppdaterade deltatabellen.

   ```
   %sql select * from customer_data
   ```

   Den returnerade tabellen visar den senaste posten.

   ![Senaste posten visas i tabellen](./media/data-lake-storage-events/final_query.png "Senaste posten visas i tabellen")

6. Om du vill uppdatera posten `customer-order-update.csv`skapar du en fil med namnet , klistrar in följande information i filen och sparar den på den lokala datorn.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Denna csv-fil är nästan identisk med den föregående förutom `228` att `22`kvantiteten i ordern ändras från till .

7. I Storage Explorer överför du filen till **indatamappen** för ditt lagringskonto.

8. Kör `select` frågan igen om du vill se den uppdaterade deltatabellen.

   ```
   %sql select * from customer_data
   ```

   Den returnerade tabellen visar den uppdaterade posten.

   ![Uppdaterad post visas i tabellen](./media/data-lake-storage-events/final_query-2.png "Uppdaterad post visas i tabellen")

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för lagringskontot och sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Reagera på Blob Storage-händelser](storage-blob-event-overview.md)
