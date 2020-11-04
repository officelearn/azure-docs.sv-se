---
title: 'Självstudie: Skicka Event Hubs data till data lagret – Event Grid'
description: 'Självstudie: beskriver hur du använder Azure Event Grid och Event Hubs för att migrera data till en Azure Synapse-analys. Den använder en Azure-funktion för att hämta en Capture-fil.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4fb26bf92e6af1fd9e97f3b9434b4ab5e76316b3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305268"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Självstudie: strömma Big data till ett informations lager
Azure [Event Grid](overview.md) är en intelligent tjänst för händelsedirigering som innebär att du kan agera på aviseringar (händelser) från appar och tjänster. Tjänsten kan till exempel utlösa en Azure-funktion som bearbetar Event Hubs-data som har hämtats till en Azure-blobblagring eller Azure Data Lake Storage och som migrerar datan till andra lagringsplatser. Det här [Event Hubs och event Grid integrations exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) visar hur du använder Event Hubs med event Grid för att sömlöst migrera insamlade Event Hubs data från Blob Storage till en Azure Synapse Analytics (tidigare SQL Data Warehouse).

![Programöversikt](media/event-grid-event-hubs-integration/overview.png)

Diagrammet visar arbetsflödet för lösningen som du skapar i självstudien: 

1. Data som skickas till en Azure-händelsehubb samlas i en Azure-blobblagring.
2. När datainsamlingen är klar skapas en händelse som skickas till ett Azure-händelserutnät. 
3. Händelserutnätet vidarebefordrar händelsedatan till en Azure-funktionsapp.
4. Funktionsappen använder blobb-URL:en i händelsedatan till att hämta blobben från lagringen. 
5. Function-appen migrerar BLOB-data till en Azure Synapse-analys. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Använd en Azure Resource Manager mall för att distribuera infrastrukturen: en Event Hub, ett lagrings konto, en Function-app, en Synapse-analys.
> * Skapar en tabell i informationslagret.
> * Lägger till kod i funktionsappen.
> * Prenumererar på händelsen. 
> * Kör appen som skickar data till händelsehubben.
> * Visar migrerade data i informationslagret.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du behöver följande för att kunna slutföra den här självstudiekursen:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual studio 2019](https://www.visualstudio.com/vs/) med arbets belastningar för: .net Skriv bords utveckling, Azure-utveckling, ASP.net och webb utveckling, Node.js utveckling och python-utveckling.
* Ladda ned [exempelprojektet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) till din dator.

## <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen
I det här steget distribuerar du den infrastruktur som krävs med en [Resource Manager-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). När du distribuerar mallen skapas följande resurser:

* Händelsehubb med Capture-funktionen aktiverad.
* Lagringskonto för hämtade filer. 
* Apptjänstplan som är värd för funktionsappen
* Funktionsapp för bearbetning av händelsen
* SQL Server som värd för informationslagret
* Azure Synapse Analytics för lagring av migrerade data

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Starta Azure Cloud Shell i Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj **Cloud Shell** -knappen överst.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. Du ser att Cloud Shell öppnas längst ned i webbläsaren.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Om du i Cloud Shell ser ett alternativ för att välja mellan **Bash** och **PowerShell** , väljer du **Bash**. 
5. Om du använder Cloud Shell för första gången, skapar du ett lagringskonto genom att välja **Skapa lagring**. Det måste finnas ett Azure-lagringskonto i Azure Cloud Shell för att vissa filer ska kunna lagras. 

    ![Skärm bild som visar dialog rutan "du har ingen lagrings monterad" med knappen "skapa lagring" vald.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Vänta tills Cloud Shell har initierats. 

    ![Skapa lagring för Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Använda Azure CLI

1. Skapa en Azure-resursgrupp genom att köra följande CLI-kommando: 
    1. Kopiera och klistra in följande kommando i Cloud Shell-fönstret. Ändra resurs gruppens namn och plats om du vill.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Tryck på **RETUR**. 

        Här är ett exempel:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Distribuera alla resurser som nämns i föregående avsnitt (händelsehubben, lagrings konto, Functions-app, Azure Synapse Analytics) genom att köra följande CLI-kommando: 
    1. Kopiera och klistra in kommandot i Cloud Shell-fönstret. Alternativt kan du kopiera och klistra in i ett redigeringsprogram, ange värden och sedan kopiera kommandot till Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Ange värden för följande entiteter:
        1. Namn på den resursgrupp som du skapade tidigare.
        2. Namn på händelsehubbens namnområde. 
        3. Namn på händelsehubben. Du kan lämna värdet som det är (hubdatamigration).
        4. Namn på SQL-servern.
        5. Namn på SQL-användaren och lösenordet. 
        6. Namn på Azure Synapse Analytics
        7. Namn på lagringskontot. 
        8. Namn på funktionsappen. 
    3.  Tryck på **Retur-tangenten** i Cloud Shell-fönstret för att köra kommandot. Den här processen kan ta en stund eftersom du skapar många resurser. Kontrollera att det inte finns några fel i kommandoresultatet. 
    

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

1. Växla till PowerShell-läget i Azure Cloud Shell. Välj nedåtpilen i det övre vänstra hörnet av Azure Cloud Shell och välj **PowerShell**.

    ![Växla till PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Skapa en Azure-resursgrupp genom att köra följande kommando: 
    1. Kopiera och klistra in följande kommando i Cloud Shell-fönstret.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Ange ett namn för **resurs gruppen**.
    3. Tryck på Retur-tangenten. 
3. Distribuera alla resurser som nämns i föregående avsnitt (händelsehubben, lagrings konto, Functions-app, Azure Synapse Analytics) genom att köra följande kommando:
    1. Kopiera och klistra in kommandot i Cloud Shell-fönstret. Alternativt kan du kopiera och klistra in i ett redigeringsprogram, ange värden och sedan kopiera kommandot till Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Ange värden för följande entiteter:
        1. Namn på den resursgrupp som du skapade tidigare.
        2. Namn på händelsehubbens namnområde. 
        3. Namn på händelsehubben. Du kan lämna värdet som det är (hubdatamigration).
        4. Namn på SQL-servern.
        5. Namn på SQL-användaren och lösenordet. 
        6. Namn på Azure Synapse Analytics
        7. Namn på lagringskontot. 
        8. Namn på funktionsappen. 
    3.  Tryck på **Retur-tangenten** i Cloud Shell-fönstret för att köra kommandot. Den här processen kan ta en stund eftersom du skapar många resurser. Kontrollera att det inte finns några fel i kommandoresultatet. 

### <a name="close-the-cloud-shell"></a>Stäng Cloud Shell 
Stäng Cloud Shell genom att välja knappen **Cloud Shell** i portalen (eller) **X** i det övre högra hörnet i Cloud Shell-fönstret. 

### <a name="verify-that-the-resources-are-created"></a>Kontrollera att resurserna har skapats

1. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn. 
2. Filtrera listan över resursgrupper genom att ange namnet på resursgruppen i sökrutan. 
3. Välj din resursgrupp i listan.

    ![Välj din resursgrupp](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Kontrollera att du ser följande resurser i resursgruppen:

    ![Resurser i resursgruppen](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Skapa en tabell i Azure Synapse Analytics
Skapa en tabell i informationslagret genom att köra skriptet [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Du kan köra skriptet med Visual Studio eller Frågeredigeraren i portalen. Följande steg visar hur du använder Frågeredigeraren: 

1. I listan över resurser i resurs gruppen väljer du din **dedikerade SQL-pool**. 
2. På sidan Azure Synapse Analytics väljer du **Frågeredigeraren (för hands version)** på den vänstra menyn. 

    ![Azure Synapse Analytics-sida](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Ange namnet på **användare** och **lösenord** för SQL-servern och välj **OK**. Du kan behöva lägga till din klient-IP-adress i brand väggen för att kunna logga in på SQL Server. 

    ![SQL-serverautentisering](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Kopiera och kör följande SQL-skript i frågefönstret: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![Köra SQL-fråga](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Låt den här fliken eller fönstret vara öppet så att du kan kontrollera att data har skapats i slutet av självstudien. 

### <a name="update-the-function-runtime-version"></a>Uppdatera funktionens körnings version

1. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn.
2. Välj den resurs grupp där Function-appen finns. 
3. Välj Function-appen av typen **App Service** i listan över resurser i resurs gruppen.
4. Välj **konfiguration** under **Inställningar** på den vänstra menyn. 
5. Växla till fliken **funktionens körnings inställningar** i den högra rutan. 
5. Uppdatera **Kör tids versionen** till **~ 3**. 

    ![Uppdatera funktion runtime-version](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Publicera Azure Functions-appen

1. Starta Visual Studio.
2. Öppna lösningen **EventHubsCaptureEventGridDemo.sln** som du laddade ned från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) som en del av förutsättningarna.
3. Högerklicka på **FunctionEGDWDumper** i Solution Explorer och välj **Publicera**.

   ![Publicera funktionsapp](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Om du ser följande skärm väljer du **Starta**. 

   ![Skärm bild som visar Visual Studios med knappen "starta" i publicerings avsnittet.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. I dialog rutan **publicera** väljer du **Azure** for **target** och **sedan nästa**. 

   ![Knappen Starta publicering](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Välj **Azure-Funktionsapp (Windows)** och välj **Nästa**. 

   ![Välj Azure Funktionsapp-Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. På fliken **Functions-instans** väljer du din Azure-prenumeration, expanderar resurs gruppen och väljer sedan Function-appen och väljer sedan **Slutför**. Du måste logga in på ditt Azure-konto om du inte redan har gjort det. 

   ![Välj din Function-app](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. I avsnittet **tjänst beroenden** väljer du **Konfigurera**.
9. På sidan **Konfigurera beroende** väljer du det lagrings konto som du skapade tidigare och väljer sedan **Nästa**. 
10. Behåll inställningarna för anslutnings strängens namn och värde och välj **sedan nästa**.
11. Avmarkera alternativet för **hemligheter** och välj sedan **Slutför**.  
8. När profilen har konfigurerats i Visual Studio väljer du **Publicera**.

   ![Välj Publicera](media/event-grid-event-hubs-integration/select-publish.png)

När du har publicerat funktionen är du redo att prenumerera på händelsen.

## <a name="subscribe-to-the-event"></a>Prenumerera på händelsen

1. I en ny flik eller ett nytt fönster med en webbläsare går du till [Azure Portal](https://portal.azure.com).
2. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn. 
3. Filtrera listan över resursgrupper genom att ange namnet på resursgruppen i sökrutan. 
4. Välj din resursgrupp i listan.

    ![Välj din resursgrupp](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Välj App Service plan (inte App Service) i listan över resurser i resurs gruppen. 
5. På sidan App Service-plan väljer du **Appar** i den vänstra menyn och sedan funktionsappen. 

    ![Välj din funktionsapp](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expandera funktionsappen, expandera funktioner och välj sedan din funktion. 
7. Välj **Lägg till Event Grid-prenumeration** i verktygsfältet. 

    ![Välj Azure-funktion](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Gör följande på sidan **Skapa en Event Grid-prenumeration** : 
    1. På sidan **INFORMATION OM HÄNDELSEPRENUMERATION** anger du ett namn för prenumerationen (till exempel: captureEventSub). Välj sedan **Skapa**. 
    2. I avsnittet **INFORMATION OM ÄMNE** gör du följande:
        1. Välj **Event Hubs namnrum** för **ämnes typer**. 
        2. Välj din Azure-prenumeration.
        2. Välj Azure-resursgrupp.
        3. Välj din Event Hubs namn område.
    3. I avsnittet **händelse typer** bekräftar du att den **skapade infångstfilen** är markerad för **filter till händelse typer**. 
    4. I avsnittet **information om slut punkts information** bekräftar du att **slut punkts typen** har angetts till **Azure Function** och att **slut punkten** har angetts till Azure-funktionen. 
    
        ![Skapa Event Grid-prenumeration](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Kör appen för att generera data
Du är klar med konfigurationen av händelsehubben, Azure Synapse Analytics, Azure Function-appen och händelse prenumerationen. Innan du kör ett program som genererar data för händelsehubben måste du konfigurera några värden.

1. Gå till resursgruppen som du gjorde tidigare i Azure Portal. 
2. Välj Event Hubs-namnområde.
3. På sidan **Event Hubs-namnområde** väljer du **Principer för delad åtkomst** i den vänstra menyn.
4. Välj **RootManageSharedAccessKey** i listan med principer. 
5. Välj kopieringsknappen intill textrutan **Primärnyckel för anslutningssträng**. 

    ![Anslutningssträng för händelsehubbens namnområde](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Gå tillbaka till Visual Studio-lösningen. 
2. Gå till projektet WindTurbineDataGenerator och öppna **program.cs**.
5. Ersätt de två konstanta värdena. Använd det kopierade värdet för **EventHubConnectionString**. Använd händelsehubbnamnet **hubdatamigration**. Om du använde ett annat namn för händelsehubben, anger du det namnet. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skapa lösningen. Kör **WindTurbineGenerator.exe** -programmet. 
7. Vänta några minuter och fråga sedan tabellen i informationslagret efter migrerade data.

    ![Frågeresultat](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Händelsedata som genererats av händelsehubben
Event Grid distribuerar händelsedata till prenumeranterna. I följande exempel visar genererade händelsedata när data som strömmas via en händelsehubb hämtas i en blobb. Observera särskilt att egenskapen `fileUrl` i `data`-objektet pekar på blobben i lagringen. Funktionsappen använder den här URL:en för att hämta blobbfilen med insamlade data.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Nästa steg

* Läs om skillnaderna mellan Azures meddelandetjänster i [Välj mellan Azure-tjänster som levererar meddelanden](compare-messaging-services.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* En introduktion till Event Hubs Capture finns i [Enable Event Hubs Capture using the Azure portal](../event-hubs/event-hubs-capture-enable-through-portal.md) (Aktivera Event Hubs Capture med hjälp av Azure Portal).
* Mer information om hur du konfigurerar och kör exemplet finns i [Event Hubs Capture- och Event Grid-exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
