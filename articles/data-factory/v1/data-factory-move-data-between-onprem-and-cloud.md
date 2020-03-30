---
title: Flytta data – datahanteringsgateway
description: Konfigurera en datagateway för att flytta data mellan lokala och molnet. Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982204"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Flytta data mellan lokala källor och molnet med Data Management Gateway
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [kopiera data mellan lokalt och i molnet med Data Factory](../tutorial-hybrid-copy-powershell.md).

Den här artikeln innehåller en översikt över dataintegration mellan lokala datalager och molndatalager med Data Factory. Den bygger på [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln och andra data fabrik kärnkoncept artiklar: [datauppsättningar](data-factory-create-datasets.md) och [rörledningar](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway för datahantering
Du måste installera Data Management Gateway på den lokala datorn för att kunna flytta data till/från ett lokalt datalager. Gatewayen kan installeras på samma dator som datalagret eller på en annan dator så länge gatewayen kan ansluta till datalagret.

> [!IMPORTANT]
> Mer information om Data Management Gateway finns i artikeln Data [Management Gateway.](data-factory-data-management-gateway.md)

Följande genomgång visar hur du skapar en datafabrik med en pipeline som flyttar data från en lokal **SQL Server-databas** till en Azure-blob-lagring. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Genomgång: kopiera lokala data till molnet
I den här genomgången gör du följande steg:

1. Skapa en datafabrik.
2. Skapa en datahanteringsgateway.
3. Skapa länkade tjänster för käll- och sinkdatalager.
4. Skapa datauppsättningar för att representera in- och utdata.
5. Skapa en pipeline med en kopieringsaktivitet för att flytta data.

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för handledningen
Innan du påbörjar den här genomgången måste du ha följande förutsättningar:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i artikeln [Om kostnadsfri utvärderingsversion.](https://azure.microsoft.com/pricing/free-trial/)
* **Azure Storage-konto**. Du använder blob-lagringen som ett **mål-/sink-datalager** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
* **SQL Server**. Du använder en lokal SQL Server-databas som **källdata** i den här självstudien.

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure Data Factory-instans med namnet **ADFTutorialOnPremDF**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Skapa en resurs,** klicka på **Intelligens + analys**och klicka på Data **Factory**.

   ![Nytt->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. På sidan **Ny datafabrik** anger du **ADFTutorialOnPremDF** för namnet.

    ![Lägg till i Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om felet visas: **Datafabriksnamnet "ADFTutorialOnPremDF" inte är tillgängligt**ändrar du namnet på datafabriken (till exempel ditt namnADFTutorialOnPremDF) och försöker skapa igen. Använd det här namnet i stället för ADFTutorialOnPremDF när du utför återstående steg i den här självstudien.
   >
   > Namnet på datafabriken kan registreras som ett **DNS-namn** i framtiden och blir därmed offentligt synligt.
   >
   >
4. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
5. Välj befintlig **resursgrupp** eller skapa en resursgrupp. Skapa en resursgrupp med namnet **ADFTutorialResourceGroup**för självstudien.
6. Klicka på **Skapa** på sidan **Ny datafabrik.**

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
   >
   >
7. När skapandet är klart visas sidan **Data Factory** som visas i följande bild:

   ![Startsida för datafabrik](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Skapa gateway
1. På sidan **Data Factory** klickar du på Författare **och distribuerar** panel för att starta **redigeraren** för datafabriken.

    ![Ikonen Författare och distribution](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. I Data Factory Editor klickar du på **... Mer** om verktygsfältet och klicka sedan på **Ny datagateway**. Du kan också högerklicka på **Datagateways** i trädvyn och klicka på **Ny datagateway**.

   ![Ny datagateway i verktygsfältet](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. På sidan **Skapa** anger du **adftutorialgateway** för **namnet**och klickar på **OK**.     

    ![Sidan Skapa gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > I den här genomgången skapar du den logiska gatewayen med endast en nod (lokal Windows-dator). Du kan skala ut en datahanteringsgateway genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet dataförflyttningsjobb som kan köras samtidigt på en nod. Den här funktionen är också tillgänglig för en logisk gateway med en enda nod. Mer information finns [i Scaling data management gateway i Azure Data](data-factory-data-management-gateway-high-availability-scalability.md) Factory-artikeln.  
4. Klicka på Installera **direkt på**den här datorn på sidan **Konfigurera** . Den här åtgärden hämtar installationspaketet för gatewayen, installerar, konfigurerar och registrerar gatewayen på datorn.  

   > [!NOTE]
   > Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.
   >
   > Om du använder Chrome, går du till [Chrome Web Store](https://chrome.google.com/webstore/), söker efter nyckelordet ”ClickOnce”, väljer ett av ClickOnce-tilläggen och installerar det.
   >
   > Gör samma sak för Firefox (installera tillägg). Klicka på **Knappen Öppna meny** i verktygsfältet **(tre vågräta linjer** i det övre högra hörnet), klicka på Tillägg , sök med nyckelordet ClickOnce, välj ett av ClickOnce-tilläggen och installera det. **Add-ons**    
   >
   >

    ![Gateway - Konfigurera sida](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    På så sätt är det enklaste sättet (ett klick) att ladda ner, installera, konfigurera och registrera gatewayen i ett enda steg. Du kan se att Konfigurationshanteraren för **Microsoft Data Management Gateway** är installerat på datorn. Du hittar också den körbara **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Du kan också hämta och installera gatewayen manuellt med hjälp av länkarna på den här sidan och registrera den med hjälp av nyckeln som visas i textrutan **NY NYCKEL.**

    Se [datahanteringsgatewayartikeln](data-factory-data-management-gateway.md) för all information om gatewayen.

   > [!NOTE]
   > Du måste vara administratör på den lokala datorn för att kunna installera och konfigurera datahanteringsgatewayen. Du kan lägga till ytterligare användare i den lokala **Windows-gruppen Data Management Gateway Users.** Medlemmarna i den här gruppen kan använda konfigurationshanteraren för datahanteringsgateway för att konfigurera gatewayen.
   >
   >
5. Vänta ett par minuter eller vänta tills följande meddelande visas:

    ![Gateway installationen lyckades](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Starta **Configuration Manager-programmet för datahantering** gateway på datorn. Skriv **Datahanteringsgateway** i **sökfönstret** för att komma åt det här verktyget. Du hittar också den körbara **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Konfigurationshanteraren för gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Bekräfta att `adftutorialgateway is connected to the cloud service` du ser meddelandet. Statusfältet längst ned visar **Ansluten till molntjänsten** tillsammans med en **grön bock**.

    På fliken **Start** kan du också göra följande:

   * **Registrera** en gateway med en nyckel från Azure-portalen med hjälp av knappen Registrera.
   * **Stoppa** värdtjänsten för datahanteringsgateway som körs på gateway-datorn.
   * **Schemalägg uppdateringar** som ska installeras vid en viss tidpunkt på dagen.
   * Visa när gatewayen **senast uppdaterades**.
   * Ange vid vilken tidpunkt en uppdatering av gatewayen kan installeras.
8. Växla till fliken **Inställningar.** Certifikatet som anges i avsnittet **Certifikat** används för att kryptera/dekryptera autentiseringsuppgifter för det lokala datalagret som du anger på portalen. (valfritt) Klicka på **Ändra** om du vill använda ditt eget certifikat i stället. Som standard använder gatewayen certifikatet som genereras automatiskt av tjänsten Data Factory.

    ![Konfiguration av gatewaycertifikat](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Du kan också göra följande åtgärder på fliken **Inställningar:**

   * Visa eller exportera certifikatet som används av gatewayen.
   * Ändra https-slutpunkten som används av gatewayen.    
   * Ange att en HTTP-proxy ska användas av gatewayen.     
9. (valfritt) Växla till fliken **Diagnostik,** kontrollera alternativet **Aktivera utförlig loggning** om du vill aktivera utförlig loggning som du kan använda för att felsöka eventuella problem med gatewayen. Loggningsinformationen finns i **Loggboken** under **Noden Datahanteringsgated** -> för program och tjänster loggar**in datahantering.**

    ![Fliken Diagnostik](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Du kan också utföra följande åtgärder på fliken **Diagnostik:**

   * Använd avsnittet **Testa anslutning** till en lokal datakälla med hjälp av gatewayen.
   * Klicka på **Visa loggar** om du vill visa loggen för datahanteringsgateway i ett loggboksfönster.
   * Klicka på **Skicka loggar** om du vill ladda upp en zip-fil med loggar från de senaste sju dagarna till Microsoft för att underlätta felsökningen av dina problem.
10. På fliken Diagnostik i avsnittet **Testa anslutning** väljer du **SqlServer** för typen av datalager, anger namnet på databasservern, **databasens** namn, anger autentiseringstyp, anger användarnamn och lösenord och klickar på **Testa** för att testa om gatewayen kan ansluta till databasen.
11. Växla till webbläsaren och klicka på **OK** på sidan **Konfigurera** i **Azure-portalen**och sedan på sidan **Ny datagateway.**
12. Du bör se **adftutorialgateway** under **Data Gateways** i trädvyn till vänster.  Om du klickar på den bör du se den associerade JSON.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **SqlServerLinkedService**. **SqlServerLinkedService** länkar en lokal SQL Server-databas och **AzureStorageLinkedService-länkade** tjänsten länkar ett Azure-blob-arkiv till datafabriken. Du skapar en pipeline senare i den här genomgången som kopierar data från den lokala SQL Server-databasen till Azure blob-arkivet.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Lägga till en länkad tjänst i en lokal SQL Server-databas
1. Klicka på **Nytt datalager** i verktygsfältet i **Data Factory Editor**och välj SQL **Server**.

   ![Ny SQL Server-länkad tjänst](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Gör följande i **JSON-redigeraren** till höger:

   1. För **gatewayName**anger du **adftutorialgateway**.    
   2. Gör följande i **connectionString:**    

      1. För **servernamn**anger du namnet på den server som är värd för SQL Server-databasen.
      2. För **databasnamn**anger du namnet på databasen.
      3. Klicka på **Knappen Kryptera** i verktygsfältet. Du ser programmet Autentiseringsuppgifter.

         ![Autentiseringshanteraren-programmet](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. I dialogrutan **Inställningsautentiseringsuppgifter** anger du autentiseringstyp, användarnamn och lösenord och klickar på **OK**. Om anslutningen lyckas lagras de krypterade autentiseringsuppgifterna i JSON och dialogrutan stängs.
      5. Stäng den tomma webbläsarfliken som startade dialogrutan om den inte stängs automatiskt och gå tillbaka till fliken med Azure-portalen.

         På gateway-datorn **krypteras** dessa autentiseringsuppgifter med hjälp av ett certifikat som datafabrikstjänsten äger. Om du vill använda certifikatet som är associerat med datahanteringsgatewayen i stället läser du Ange autentiseringsuppgifter på ett säkert sätt.    
   3. Klicka på **Distribuera** i kommandofältet om du vill distribuera den SQL Server-länkade tjänsten. Du bör se den länkade tjänsten i trädvyn.

      ![SQL Server-länkad tjänst i trädvyn](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Lägga till en länkad tjänst för ett Azure-lagringskonto
1. Klicka på **Nytt datalager** i kommandofältet i **Data Factory Editor**och klicka på Azure **storage**.
2. Ange namnet på ditt Azure-lagringskonto för **kontonamnet**.
3. Ange nyckeln för ditt Azure-lagringskonto för **kontonyckeln**.
4. Klicka på **Distribuera** om du vill distribuera **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage). Innan du skapar datauppsättningar gör du följande steg (detaljerade anvisningar kommer i listan):

* Skapa en tabell med namnet **emp** i SQL Server-databasen du la till som en länkad tjänst till datafabriken och infoga ett par exempelposter i tabellen.
* Skapa en blobcontainer med namnet **adftutorial** i Azure Blob Storage-kontot du la till som en länkad tjänst i datafabriken.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Förbereda lokal SQL Server för självstudien
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

1. Klicka **på** **... Mer**klickar du på **Ny datauppsättning** i kommandofältet och klickar på **SQL Server-tabellen**.
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

   * **är** inställd på **SqlServerTable**.
   * **tableName** är inställt på **emp**.
   * **linkedServiceName** är inställt på **SqlServerLinkedService** (du hade skapat den länkade tjänsten tidigare i den här genomgången.).
   * För en indatauppsättning som inte genereras av en annan pipeline i Azure Data Factory måste du ange **extern** till **true**. Det betecknar indata produceras externt till Azure Data Factory-tjänsten. Du kan också ange eventuella externa dataprinciper med hjälp av **elementet externalData** i avsnittet **Princip.**    

   Mer information om JSON-egenskaper finns i [Flytta data till/från SQL Server.](data-factory-sqlserver-connector.md)
3. Klicka på **Distribuera** i kommandofältet om du vill distribuera datauppsättningen.  

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata

1. Klicka på **Ny datauppsättning** i kommandofältet i **Data Factory Editor**och klicka på Azure **Blob storage**.
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

   * **är** inställd på **AzureBlob**.
   * **linkedServiceName** är inställt på **AzureStorageLinkedService** (du hade skapat den länkade tjänsten i steg 2).
   * **folderPath** är inställd på **adftutorial/outfromonpremdf** där outfromonpremdf är mappen i adftutorial-behållaren. Skapa **adftutorial-behållaren** om den inte redan finns.
   * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar ett utdatasegment varje timme i **emp-tabellen** i Azure SQL Database.

   Om du inte anger något **filNamn** för en **utdatatabell**namnges de genererade `Data.<Guid>.txt` filerna i **mappenPath** i följande format: (till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av partitionedBy-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv.

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

    Mer information om JSON-egenskaper finns i [Flytta data till/från Azure Blob Storage.](data-factory-azure-blob-connector.md)
3. Klicka på **Distribuera** i kommandofältet om du vill distribuera datauppsättningen. Bekräfta att du ser båda datauppsättningarna i trädvyn.  

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du en **pipeline** med en **kopieringsaktivitet** som använder **EmpOnPremSQLTable** som indata och **OutputBlobTable** som utdata.

1. I Data Factory Editor klickar du på **... Mer**och klicka på **Ny pipeline**.
2. Ersätt JSON i den högra rutan med följande text:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
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

   * I avsnittet Aktiviteter finns det bara aktivitet vars **typ** är inställd på **Kopiera**.
   * **Indata** för aktiviteten är inställd på **EmpOnPremSQLTable** och **utdata** för aktiviteten är inställd på **OutputBlobTable**.
   * I avsnittet **typeProperties** anges **SqlSource** som **källtyp** och **BlobSink** anges som **sink-typ**.
   * SQL-frågan `select * from emp` har angetts för **sqlReaderQuery-egenskapen** **i SqlSource**.

   Både start- och slutdatum måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen.

   Om du inte anger värdet för **slutegenskapen** beräknas det som "**start + 48 timmar**". Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut**egenskapen.

   Du definierar den tidslängd under vilken datasegmenten bearbetas baserat på de **tillgänglighetsegenskaper** som har definierats för varje Azure Data Factory-datauppsättning.

   I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.        
3. Klicka på **Distribuera** i kommandofältet om du vill distribuera datauppsättningen (tabellen är en rektangulär datauppsättning). Bekräfta att pipelinen visas i trädvyn under **noden Pipelines.**  
4. Klicka nu på **X** två gånger för att stänga sidan för att komma tillbaka till **sidan Data Factory** för **ADFTutorialOnPremDF**.

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, datauppsättningar och en pipeline och schemalagt pipelinen.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visa datafabriken i en diagramvy
1. Klicka på **diagrampanelen** på startsidan för **datafabriken ADFTutorialOnPremDF** i **Azure-portalen.** :

    ![Diagram länk](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Du bör se ett diagram som liknar följande bild:

    ![Diagramvy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Du kan zooma in, zooma ut, zooma till 100 %, zooma för att passa, automatiskt placera rörledningar och datauppsättningar och visa härstamningsinformation (högdagrar uppströms och nedströms objekt för markerade objekt).  Du kan dubbelklicka på ett objekt (datauppsättning för indata/utdata eller pipeline) för att se egenskaper för det.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda Azure-portalen för att övervaka vad som händer i en Azure-datafabrik. Du kan också använda PowerShell-cmdletar för att övervaka datauppsättningar och pipelines. Mer information om övervakning finns i [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md).

1. Dubbelklicka på **EmpOnPremSQLTable**i diagrammet .  

    ![EmpOnPremSQLTable-segment](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Observera att alla datasegment upp är i **klart** tillstånd eftersom pipelinens varaktighet (starttid till sluttid) är tidigare. Det beror också på att du har infogat data i SQL Server-databasen och det är där hela tiden. Kontrollera att inga segment visas i avsnittet **Problemsegment** längst ned. Om du vill visa alla segment klickar du på **Visa mer** längst ned i listan med segment.
3. Klicka nu på **OutputBlobTable**på sidan **Datamängder** .

    ![OputputBlobTable skivor](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klicka på ett datasegment i listan så bör du se sidan **Datasegment.** Du ser aktivitetskörningar för segmentet. Du ser bara en aktivitet kör vanligtvis.  

    ![Blad för datasegment](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**.
5. Klicka på **aktiviteten som körs** från listan längst ned om du vill se information om **aktivitetskörning**.

   ![Informationssida för aktivitetskörning](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Du skulle se information som dataflöde, varaktighet och den gateway som används för att överföra data.
6. Klicka på **X** för att stänga alla sidor tills du
7. komma tillbaka till hemsidan för **ADFTutorialOnPremDF**.
8. (valfritt) Klicka på **Pipelines,** klicka på **ADFTutorialOnPremDF**och öka detaljnivån **(Förbrukad)** eller utdatauppsättningar (**Producerad**).
9. Använd verktyg som [Utforskaren](https://storageexplorer.com/) i Microsoft Storage för att kontrollera att en blob/fil skapas för varje timme.

   ![Azure Lagringsutforskaren](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Nästa steg
* Se [datahanteringsgatewayartikeln](data-factory-data-management-gateway.md) för all information om datahanteringsgatewayen.
* Se [Kopiera data från Azure Blob till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) om du vill veta mer om hur du använder Kopiera aktivitet för att flytta data från ett källdatalager till ett sink-datalager.
