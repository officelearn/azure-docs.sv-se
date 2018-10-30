---
title: Flytta data - Gateway för datahantering | Microsoft Docs
description: Konfigurera en gateway för att flytta data mellan lokalt och molnet. Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 3094075607f096200ce1fb7ad499776bdcd3e093
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233607"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Flytta data mellan lokala källor och molnet med Data Management Gateway
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [kopiera data mellan lokalt och i molnet med Data Factory](../tutorial-hybrid-copy-powershell.md).

Den här artikeln innehåller en översikt över dataintegration mellan lokala datalager och molndatalager med hjälp av Data Factory. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln och andra data factory grundläggande begrepp artiklar: [datauppsättningar](data-factory-create-datasets.md) och [pipelines](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway för datahantering
Du måste installera Data Management Gateway på din lokala dator att flytta data till/från ett lokalt datalager. Gatewayen kan installeras på samma dator som datalagret eller på en annan dator så länge gatewayen kan ansluta till datalagret.

> [!IMPORTANT]
> Se [Data Management Gateway](data-factory-data-management-gateway.md) nedan för information om Data Management Gateway. 

Den här genomgången visar hur du skapar en datafabrik med en pipeline som flyttar data från en lokal **SQL Server** databas till Azure blob storage. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Genomgång: kopiera lokala data till molnet
I den här genomgången kan du göra följande: 

1. Skapa en datafabrik.
2. Skapa en gateway för datahantering. 
3. Skapa länkade tjänster för datalager för källa och mottagare.
4. Skapa datauppsättningar som representerar inkommande och utgående data.
5. Skapa en pipeline med en kopieringsaktivitet för att flytta data.

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för självstudien
Innan du börjar den här genomgången, måste du ha följande krav:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Se den [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/) nedan för information.
* **Azure Storage-konto**. Du kan använda bloblagringen som en **destination/mottagare** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).
* **SQL Server**. Du använder en lokal SQL Server-databas som **källdata** i den här självstudien. 

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure Data Factory-instans med namnet **ADFTutorialOnPremDF**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapa en resurs**, klickar du på **information + analys**, och klicka på **Data Factory**.

   ![Nytt->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. I den **ny datafabrik** anger **ADFTutorialOnPremDF** för namnet.

    ![Lägg till på startsidan](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **datafabriksnamnet ”ADFTutorialOnPremDF” är inte tillgänglig**, ändra namnet på datafabriken (till exempel yournameADFTutorialOnPremDF) och försöker skapa igen. Använd det här namnet i stället för ADFTutorialOnPremDF när du utför stegen i den här självstudien.
   >
   > Namnet på datafabriken kan registreras som en **DNS** namn i framtiden och blir publicaly visas.
   >
   >
4. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
5. Välj befintlig **resursgrupp** eller skapa en resursgrupp. För den här självstudien skapar du en resursgrupp med namnet: **ADFTutorialResourceGroup**.
6. Klicka på **skapa** på den **ny datafabrik** sidan.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
   >
   >
7. När datafabriken har skapats visas den **Data Factory** sidan som visas i följande bild:

   ![Startsida för data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Skapa gateway
1. I den **Data Factory** klickar du på **författare och distribuera** att starta den **redigeraren** för data factory.

    ![Ikonen Författare och distribution](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. I Data Factory-redigeraren, klicka på **... Mer** i verktygsfältet och klicka sedan på **ny personlig gateway**. Alternativt kan du högerklicka på **Datagatewayer** i trädvyn och klicka på **ny personlig gateway**.

   ![Ny personlig gateway i verktygsfältet](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. I den **skapa** anger **adftutorialgateway** för den **namn**, och klicka på **OK**.     

    ![Skapa Gateway-sida](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > I den här genomgången skapar du en logisk gateway med endast en nod (den lokala Windows-dator). Du kan skala ut en data management gateway genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet data movement jobb som kan köras samtidigt på en nod. Den här funktionen finns även för en logisk gateway med en enda nod. Se [skalning gateway för datahantering i Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) nedan för information.  
4. I den **konfigurera** klickar du på **installera direkt på den här datorn**. Den här åtgärden hämtar installationspaketet för gatewayen, installerar, konfigurerar och registrerar gatewayen på datorn.  

   > [!NOTE]
   > Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.
   >
   > Om du använder Chrome, går du till [Chrome Web Store](https://chrome.google.com/webstore/), söker efter nyckelordet ”ClickOnce”, väljer ett av ClickOnce-tilläggen och installerar det.
   >
   > Gör likadant för Firefox (installera tillägget). Klicka på **öppna menyn** i verktygsfältet (**tre horisontella linjer** i det övre högra hörnet), klickar du på **tillägg**, Sök efter nyckelordet ”ClickOnce”, väljer du något av de ClickOnce-tilläggen och installera den.    
   >
   >

    ![Gateway – konfigurera](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    På så sätt är det enklaste sättet (klick) att hämta, installera, konfigurera och registrera gatewayen i ett enda steg. Du kan se den **Microsoft Data Management Gateway Configuration Manager** programmet installeras på datorn. Du kan också hitta den körbara filen **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Du kan också hämta och installera gatewayen manuellt med hjälp av länkarna på den här sidan och registrera den med hjälp av den nyckel som visas i den **ny nyckel** textrutan.

    Se [Data Management Gateway](data-factory-data-management-gateway.md) för alla information om gatewayen.

   > [!NOTE]
   > Du måste vara administratör på den lokala datorn för att installera och konfigurera Data Management Gateway har. Du kan lägga till fler användare att de **Data Management Gateway användare** lokala Windows-gruppen. Medlemmarna i den här gruppen kan använda verktyget för Data Management Gateway Configuration Manager för att konfigurera gatewayen.
   >
   >
5. Vänta några minuter eller vänta tills du ser följande meddelande:

    ![Gatewayinstallationen är klar](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Starta **Data Management Gateway Configuration Manager** på datorn. I den **Search** fönster, Skriv in **Data Management Gateway** att komma åt det här verktyget. Du kan också hitta den körbara filen **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Kontrollera att du ser `adftutorialgateway is connected to the cloud service` meddelande. Statusfältet längst ned visar **är ansluten till Molntjänsten** tillsammans med en **grön bock**.

    På den **Start** och du kan också utföra följande åtgärder:

   * **Registrera** en gateway med en nyckel från Azure-portalen med hjälp av knappen registrera dig.
   * **Stoppa** Data Management Gatewaytjänsten som körs på din gateway-dator.
   * **Schemalägga uppdateringar av** som ska installeras i en viss tid på dagen.
   * Visa när gatewayen har **senast uppdaterad**.
   * Ange tiden då en uppdatering för gateway kan installeras.
8. Växla till fliken **Settings** (Inställningar). Certifikatet som anges i den **certifikat** används för att kryptera/dekryptera autentiseringsuppgifterna för det lokala datalagret som du anger på portalen. (valfritt) Klicka på **ändra** att använda ditt eget certifikat i stället. Som standard använder gatewayen det certifikat som genereras automatiskt av Data Factory-tjänsten.

    ![Gateway-Certifikatkonfiguration](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Du kan också utföra följande åtgärder på den **inställningar** fliken:

   * Visa eller exportera certifikatet som används av gatewayen.
   * Ändra HTTPS-slutpunkt som används av gatewayen.    
   * Ange en HTTP-proxy som ska användas av gatewayen.     
9. (valfritt) Växla till den **diagnostik** fliken, kontrollera den **aktivera utförlig loggning** om du vill aktivera utförlig loggning som du kan använda för att felsöka eventuella problem med gatewayen. Loggningsinformation finns i **Loggboken** under **applikationer och tjänsteloggar** -> **Data Management Gateway** noden.

    ![Fliken Diagnostik](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Du kan också utföra följande åtgärder i den **diagnostik** fliken:

   * Använd **Testanslutningen** avsnittet till en lokal datakälla med hjälp av gatewayen.
   * Klicka på **visa loggar** att se Data Management Gateway-loggen i Loggboken-fönstret.
   * Klicka på **skicka loggar** att överföra en zip-fil med loggarna för senaste sju dagarna till Microsoft för att underlätta felsökning av ditt problem.
10. På den **diagnostik** fliken den **Testanslutningen** väljer **SqlServer** för typ av datalagret, ange namnet på databasservern, namnet på databasen, Ange autentiseringstyp och ange användarnamn och lösenord, klicka **testa** att testa om gatewayen kan ansluta till databasen.
11. Växla till webbläsaren och i den **Azure-portalen**, klickar du på **OK** på den **konfigurera** sidan och klicka sedan på den **ny personlig gateway** sidan.
12. Du bör se **adftutorialgateway** under **Datagatewayer** i trädvyn till vänster.  Om du klickar på den, bör du se associerade JSON.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **SqlServerLinkedService**. Den **SqlServerLinkedService** länkar en lokal SQL Server-databas och **AzureStorageLinkedService** länkad tjänst länkar ett Azure-blobblagring till datafabriken. Du kan skapa en pipeline senare i den här genomgången som kopierar data från en lokal SQL Server-databas till Azure-blobblagring.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Lägg till en länkad tjänst till en lokal SQL Server-databas
1. I den **Data Factory-redigeraren**, klickar du på **Nytt datalager** i verktygsfältet och väljer **SQL Server**.

   ![Ny SQL Server-länkade tjänst](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. I den **JSON-redigerare** till höger, gör du följande:

   1. För den **gatewayName**, ange **adftutorialgateway**.    
   2. I den **connectionString**, gör följande:    

      1. För **servername**, anger du namnet på den server som är värd för SQL Server-databasen.
      2. För **databasename**, anger du namnet på databasen.
      3. Klicka på **Encrypt** i verktygsfältet. Du kan se programmet hanteraren för autentiseringsuppgifter.

         ![Autentiseringsuppgifter Manager-program](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. I den **ange autentiseringsuppgifter** dialogrutan Ange autentiseringstyp, användarnamn och lösenord och klicka på **OK**. Om anslutningen lyckas krypterade autentiseringsuppgifter lagras i JSON och dialogrutan stängs.
      5. Stäng tom webbläsarfliken som startas i dialogrutan om den inte automatiskt är stängd och gå tillbaka till fliken med Azure-portalen.

         Gateway-datorn är dessa autentiseringsuppgifter **krypterade** genom att använda ett certifikat som äger Data Factory-tjänsten. Om du vill använda det certifikat som är associerad med Data Management Gateway i stället kan du läsa [ange autentiseringsuppgifter på ett säkert sätt](#set-credentials-and-security).    
   3. Klicka på **distribuera** i kommandofältet för att distribuera SQL Server-länkade tjänst. Du bör se den länkade tjänsten i trädvyn.

      ![SQL Server-länkade tjänsten i trädvyn](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Lägg till en länkad tjänst för ett Azure storage-konto
1. I den **Data Factory-redigeraren**, klickar du på **Nytt datalager** till kommandofältet och klicka på **Azure storage**.
2. Ange namnet på ditt Azure storage-konto för den **kontonamn**.
3. Ange nyckeln för ditt Azure storage-konto för den **kontonyckel**.
4. Klicka på **distribuera** att distribuera den **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage). Innan du skapar datauppsättningar gör du följande steg (detaljerade anvisningar kommer i listan):

* Skapa en tabell med namnet **emp** i SQL Server-databasen du la till som en länkad tjänst till datafabriken och infoga ett par exempelposter i tabellen.
* Skapa en blobcontainer med namnet **adftutorial** i Azure Blob Storage-kontot du la till som en länkad tjänst i datafabriken.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Förbereda en lokal SQL Server för självstudien
1. I databasen som du angav för den lokala länkade SQL Server-tjänsten (**SqlServerLinkedService**) använder du följande SQL-skript för att skapa tabellen **emp** i databasen.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Infoga vissa exempel i tabellen:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Skapa indatauppsättning

1. I **Data Factory-redigeraren**, klicka på **... Mer**, klickar du på **ny datauppsättning** till kommandofältet och klicka på **SQL Server-tabell**.
2. Ersätt JSON i den högra rutan med följande text:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Observera följande punkter:

   * **typ** är inställd på **SqlServerTable**.
   * **tableName** är inställd på **emp**.
   * **linkedServiceName** är inställd på **SqlServerLinkedService** (du skapade den här länkade tjänsten tidigare i den här genomgången.).
   * För en indatauppsättning som inte skapades av en annan pipeline i Azure Data Factory, måste du ställa in **externa** till **SANT**. Den anger indata skapas utanför Azure Data Factory-tjänsten. Alternativt kan du ange alla principer för externa data med hjälp av den **externalData** elementet i den **princip** avsnittet.    

   Se [flytta data till/från SQL Server](data-factory-sqlserver-connector.md) mer information om JSON-egenskaper.
3. Klicka på **distribuera** i kommandofältet för att distribuera datauppsättningen.  

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata

1. I den **Data Factory-redigeraren**, klickar du på **ny datauppsättning** till kommandofältet och klicka på **Azure Blob storage**.
2. Ersätt JSON i den högra rutan med följande text:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Observera följande punkter:

   * **typ** är inställd på **AzureBlob**.
   * **linkedServiceName** är inställd på **AzureStorageLinkedService** (du skapade den här länkade tjänsten i steg 2).
   * **folderPath** är inställd på **adftutorial/outfromonpremdf** där outfromonpremdf är mappen i behållaren adftutorial. Skapa den **adftutorial** behållaren om den inte redan finns.
   * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar en utdatasektor varje timme i den **emp** tabell i Azure SQL-databasen.

   Om du inte anger en **fileName** för en **utdatatabell**, genereras filer i den **folderPath** namnges i följande format: Data.<Guid>.txt (till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ange **folderPath** och **fileName** dynamiskt utifrån den **SliceStart** tid, Använd egenskapen partitionedBy. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Se [flytta data till och från Azure Blob Storage](data-factory-azure-blob-connector.md) mer information om JSON-egenskaper.
3. Klicka på **distribuera** i kommandofältet för att distribuera datauppsättningen. Kontrollera att du ser både datauppsättningarna i trädvyn.  

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du en **pipeline** med en **Kopieringsaktiviteten** som använder **EmpOnPremSQLTable** som indata och **OutputBlobTable** som utdata.

1. I Data Factory-redigeraren, klicka på **... More (Mer)** och sedan på **Ny pipeline.
2. Ersätt JSON i den högra rutan med följande text:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag.
   >
   >

   Observera följande punkter:

   * I aktivitetsavsnittet finns endast aktivitet vars **typ** är inställd på **kopiera**.
   * **Indata** för aktiviteten är inställd på **EmpOnPremSQLTable** och **utdata** för aktiviteten är inställd på **OutputBlobTable**.
   * I den **typeProperties** avsnittet **SqlSource** har angetts som den **källtyp** och ** BlobSink ** har angetts som den **mottagare typ**.
   * SQL-fråga `select * from emp` har angetts för den **sqlReaderQuery** egenskapen för **SqlSource**.

   Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen.

   Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut**egenskapen.

   Du definierar hur länge som datasektorer bearbetas baserat på den **tillgänglighet** egenskaper som har definierats för varje Azure Data Factory-datauppsättningen.

   I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.        
3. Klicka på **distribuera** i kommandofältet för att distribuera datauppsättningen (tabellen är en rektangulär datauppsättning). Bekräfta att pipelinen som visas i trädvyn under **Pipelines** noden.  
4. Klicka på **X** två gånger för att stänga sidan för att gå tillbaka till den **Data Factory** för den **ADFTutorialOnPremDF**.

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, datauppsättningar och en pipeline och schemalagt denna pipeline.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visa datafabriken i en diagramvy
1. I den **Azure-portalen**, klickar du på **Diagram** panelen på startsidan för den **ADFTutorialOnPremDF** data factory. :

    ![Diagram-länk](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Du bör se ett diagram som liknar följande bild:

    ![Diagramvy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Du kan zooma in, Zooma ut, Zooma till 100%, Zooma och anpassa, automatiskt placera pipelines och datauppsättningar och visa härkomstinformation (markerar överordnade och underordnade objekt för de valda objekten).  Du kan dubbelklicka på ett objekt (indata/utdata-datauppsättning eller pipeline) för att visa egenskaperna för den.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda Azure-portalen för att övervaka vad som händer i en Azure-datafabrik. Du kan också använda PowerShell-cmdletar för att övervaka datauppsättningar och pipelines. Mer information om övervakning finns i [övervaka och hantera Pipelines](data-factory-monitor-manage-pipelines.md).

1. I diagrammet, dubbelklickar du på **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable sektorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Lägg märke till att alla data skär upp **redo** tillstånd eftersom pipeline-varaktighet (starttiden för sluttid) är i förflutna. Det är också eftersom du har infogat data i SQL Server-databasen och det är det hela tiden. Kontrollera att inga sektorer visas i den **problemsektorer** avsnittet längst ned. Om du vill visa alla segment, klickar du på **se mer** längst ned i listan över sektorer.
3. Nu, i den **datauppsättningar** klickar du på **OutputBlobTable**.

    ![OputputBlobTable sektorer](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klicka på någon av datasektorerna i listan och du bör se den **Datasektor** sidan. Du ser aktivitetskörningar för sektorn. Du ser bara en aktivitet som kör vanligtvis.  

    ![Datasektorblad](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**.
5. Klicka på den **aktivitetskörning** i listan längst ned för att se **aktivitetskörningsinformation**.

   ![Aktiviteten kör informationssida](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Du ser information, till exempel dataflöde, varaktighet och den gateway som används för att överföra data.
6. Klicka på **X** att Stäng alla sidor tills du
7. Gå tillbaka till startsidan för den **ADFTutorialOnPremDF**.
8. (valfritt) Klicka på **Pipelines**, klickar du på **ADFTutorialOnPremDF**, och öka detaljnivån indatatabellerna (**förbrukade**) eller utdatauppsättningar (**producerat**).
9. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) att kontrollera att en blobfil skapas för varje timme.

   ![Azure Lagringsutforskaren](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Nästa steg
* Se [Data Management Gateway](data-factory-data-management-gateway.md) för alla information om Data Management Gateway.
* Se [kopiera data från Azure-Blob till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) mer information om hur du använder Kopieringsaktiviteten för att flytta data från källans datalager till mottagarens datalager.
