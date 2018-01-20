---
title: "Azure händelse rutnät och Händelsehubbar-integrering"
description: "Beskriver hur du använder Azure Event rutnät och Händelsehubbar för att migrera data till ett SQL Data Warehouse"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: b315bd77a47a6f106c5768da56828a5169de5fe9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Dataströmmen stordata i ett data warehouse

Azure [händelse rutnätet](overview.md) är en intelligent händelse routning-tjänst som gör det möjligt att ta hänsyn till meddelanden från program och tjänster. Den [Event Hubs avbilda och händelsen rutnätet exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) visar hur du använder Azure Event Hubs avbilda med Azure händelse rutnät sömlöst migrera data från en händelsehubb till ett SQL Data Warehouse.

![Programöversikt](media/event-grid-event-hubs-integration/overview.png)

När data skickas till händelsehubben hämtar data från dataströmmen och genererar storage-blobbar med data i Avro-formatet. När avbildningen genererar blob, utlöser en händelse. Händelsen rutnätet distribuerar data om händelsen till prenumeranter. I så fall skickas informationen om händelsen till Azure Functions-slutpunkten. Händelsedata som innehåller sökvägen till den genererade blobben. URL: en använder funktionen för att hämta filen och skicka det till datalagret.

I den här artikeln får du:

* Distribuera följande infrastruktur:
  * Event hub med avbilda aktiverad
  * Lagringskontot för filerna från avbildning
  * Azure app service-plan som värd för appen funktion
  * Funktionsapp för bearbetning av händelsen
  * SQL Server som värd för datalagret
  * SQL Data Warehouse för att lagra migrerade data
* Skapa en tabell i datalagret
* Lägg till kod i funktionen appen
* Prenumerera på händelsen
* Kör app som skickar data till händelsehubben
* Visa migrerade data i datalagret

## <a name="about-the-event-data"></a>Om informationen om händelsen

Händelsen rutnätet distribuerar händelsedata prenumeranter. I följande exempel visar händelsedata för att skapa en avbildning-fil. I synnerhet Observera den `fileUrl` egenskap i den `data` objekt. Funktionen appen hämtar det här värdet och används för att hämta filen avbildning.

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

## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Visual studio 2017 Version 15.3.2 eller större](https://www.visualstudio.com/vs/) med arbetsbelastningar för: .NET skrivbord utveckling, Azure-utveckling, ASP.NET och web utveckling, Node.js utveckling och utveckling av Python.
* Den [EventHubsCaptureEventGridDemo exempelprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) hämtas till datorn.

## <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen

För att förenkla den här artikeln kan distribuera du den nödvändiga infrastrukturen med en Resource Manager-mall. Om du vill visa de resurser som har distribuerats på [mallen](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). I förhandsversionen händelse rutnätet stöder **westus2** och **westcentralus** regioner. Använd någon av dessa regioner för resursgruppens plats.

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Ange ett lösenordsvärde när du tillfrågas.

## <a name="create-a-table-in-sql-data-warehouse"></a>Skapa en tabell i SQL Data Warehouse

Lägg till en tabell till ditt data warehouse genom att köra den [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) skript. Kör skriptet med Visual Studio eller frågeredigeraren i portalen.

Skriptet ska köras är:

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

## <a name="publish-the-azure-functions-app"></a>Publicera appen Azure Functions

1. Öppna den [EventHubsCaptureEventGridDemo exempelprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) i Visual Studio-2017 (15.3.2 eller högre).

2. I Solution Explorer högerklickar du på **FunctionDWDumper**, och välj **publicera**.

   ![Publicera funktionsapp](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Välj **Azure Funktionsapp** och **Välj befintlig**. Välj **OK**.

   ![Mål-funktionsapp](media/event-grid-event-hubs-integration/pick-target.png)

4. Välj funktionen appen som du har distribuerat via mallen. Välj **OK**.

   ![Välj funktionsapp](media/event-grid-event-hubs-integration/select-function-app.png)

5. När profilen har konfigurerats för Visual Studio, markera **publicera**.

   ![Välj publicera](media/event-grid-event-hubs-integration/select-publish.png)

6. När du har publicerat funktionen, gå till den [Azure-portalen](https://portal.azure.com/). Välj appen resurs grupp och funktion.

   ![Visa funktionsapp](media/event-grid-event-hubs-integration/view-function-app.png)

7. Välj funktionen.

   ![Välj funktionen](media/event-grid-event-hubs-integration/select-function.png)

8. Hämta URL för funktionen. När du skapar händelseprenumerationen måste denna URL.

   ![Hämta funktionswebbadress](media/event-grid-event-hubs-integration/get-function-url.png)

9. Kopiera värdet.

   ![Kopiera URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Prenumerera på händelsen

Du kan använda Azure CLI eller portalen för att prenumerera på händelsen. Den här artikeln visar båda metoderna.

### <a name="portal"></a>Portalen

1. Välj namnområdet Händelsehubbar **händelse rutnätet** till vänster.

   ![Välj händelse rutnätet](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Lägga till en händelseprenumeration.

   ![Lägg till händelseprenumeration](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Ange värden för händelseprenumerationen. Använd Azure Functions-URL som du kopierade. Välj **Skapa**.

   ![Ange värden för prenumeration](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Azure CLI

Kör följande kommandon för att prenumerera på händelsen (som kräver version 2.0.24 eller senare av Azure CLI):

```azurecli-interactive
namespaceid=$(az resource show --namespace Microsoft.EventHub --resource-type namespaces --name <your-EventHubs-namespace> --resource-group rgDataMigrationSample --query id --output tsv)
az eventgrid event-subscription create \
  --resource-id $namespaceid \
  --name captureEventSub \
  --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Kör appen för att generera data

Du har konfigurerat din händelsehubb, SQL data warehouse, Azure funktionsapp och händelseprenumerationen. Lösningen är redo att migrera data från event hub till datalagret. Innan du kör ett program som genererar data för händelsehubb, måste du konfigurera några värden.

1. Välj namnområdet händelse hubb i portalen. Välj **anslutningssträngar**.

   ![Välj anslutningssträngar](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Välj **RootManageSharedAccessKey**

   ![Markera nyckel](media/event-grid-event-hubs-integration/show-root-key.png)

3. Kopiera **anslutningssträngen - primärnyckel**

   ![Kopiera nyckeln](media/event-grid-event-hubs-integration/copy-key.png)

4. Gå tillbaka till Visual Studio-projekt. Öppna i projektet WindTurbineDataGenerator **program.cs**.

5. Ersätt två konstanta värden. Använd kopierade värdet för **EventHubConnectionString**. Använd händelsehubbens namn för **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skapa lösningen. Kör WindTurbineGenerator.exe program. Fråga tabellen i datalagret för migrerade data efter ett par minuter.

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* En introduktion till Event Hubs avbilda finns [aktivera Event Hubs avbilda med Azure-portalen](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Läs mer om att installera och köra exemplet [Event Hubs avbilda och händelsen rutnätet exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).