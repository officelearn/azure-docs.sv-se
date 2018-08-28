---
title: Azure Event Grid- och Event Hubs-integration
description: Beskriver hur du använder Azure Event Grid och Event Hubs för att migrera data till ett SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: 432436ee13519cf342313ad369c168ba764f9264
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616523"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Strömma stordata till ett datalager

Azure [Event Grid](overview.md) är en intelligent händelsedirigeringstjänst som skickar aviseringar från appar och tjänster. Tjänsten kan till exempel utlösa en Azure-funktion som bearbetar Event Hubs-data som har hämtats till Azure Blob Storage eller Data Lake Store och som migrerar data till andra lagringsplatser. Det här [exemplet med Event Hubs Capture och Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) visar hur du använder Event Hubs Capture med Event Grid för att sömlöst migrera Event Hubs-data från blobblagring till SQL Data Warehouse.

![Programöversikt](media/event-grid-event-hubs-integration/overview.png)

När data skickas till händelsehubben hämtar Capture data från dataströmmen och genererar lagringsblobar med dessa data i Avro-format. När Capture genererar bloben utlöser detta en händelse. Event Grid skickar ut data om händelsen till prenumeranter. I detta fall skickas händelseinformationen till slutpunkten för Azure Functions. Händelseinformationen innehåller sökvägen för den genererade bloben. Funktionen använder den URL:en för att hämta filen och skickar den till informationslagret.

Den här artikeln innehåller följande avsnitt:

* Distribuera följande infrastruktur:
  * Händelsehubb med Capture aktiverat
  * Lagringskonto för filerna från Capture
  * Azure App Service-plan som värd för funktionsappen
  * Funktionsapp för bearbetning av händelsen
  * SQL Server som värd för informationslagret
  * SQL Data Warehouse för lagring av migrerade data
* Skapa en tabell i informationslagret
* Lägga till kod i funktionsappen
* Prenumerera på händelsen
* Köra appen som skickar data till händelsehubben
* Visa migrerade data i informationslagret

## <a name="about-the-event-data"></a>Om händelsedata

Event Grid distribuerar händelsedata till prenumeranterna. Följande exempel visar händelsedata för genereringen av en Capture-fil. Notera särskilt egenskapen `fileUrl` i objektet `data`. Funktionsappen hämtar det här värdet och använder det för att hämta Capture-filen.

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

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna slutföra den här självstudiekursen:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Visual studio 2017 version 15.3.2 eller senare](https://www.visualstudio.com/vs/) med arbetsbelastningar för .NET-skrivbordsutveckling, Azure-utveckling, ASP.NET- och webbutveckling, Node.js-utveckling och Python-utveckling.
* [Exempelprojektet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) nedladdat på datorn.

## <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen

För enkelhetens skull distribuerar du den nödvändiga infrastrukturen med en Resource Manager-mall. Om du vill visa de resurser som distribueras visar du [mallen](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json).

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

Ange ett lösenordsvärde när du uppmanas att göra det.

## <a name="create-a-table-in-sql-data-warehouse"></a>Skapa en tabell i SQL Data Warehouse

Lägg till en tabell i informationslagret genom att köra skriptet [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Kör skriptet med Visual Studio eller Frågeredigeraren på portalen.

Skriptet som du kör är:

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

## <a name="publish-the-azure-functions-app"></a>Publicera Azure Functions-appen

1. Öppna [exempelprojektet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) i Visual Studio 2017 (15.3.2 eller senare).

1. Högerklicka på **FunctionEGDWDumper** i Solution Explorer och välj **Publicera**.

   ![Publicera funktionsapp](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Välj **Azure-funktionsapp** och **Välj befintlig**. Välj **Publicera**.

   ![Målfunktionsapp](media/event-grid-event-hubs-integration/pick-target.png)

1. Välj den funktionsapp som du distribuerade via mallen. Välj **OK**.

   ![Välj funktionsapp](media/event-grid-event-hubs-integration/select-function-app.png)

1. När profilen har konfigurerats i Visual Studio väljer du **Publicera**.

   ![Välj Publicera](media/event-grid-event-hubs-integration/select-publish.png)

När du har publicerat funktionen är du redo att prenumerera på händelsen.

## <a name="subscribe-to-the-event"></a>Prenumerera på händelsen

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj din resursgrupp och funktionsapp.

   ![Visa funktionsapp](media/event-grid-event-hubs-integration/view-function-app.png)

1. Välj funktionen.

   ![Välj funktion](media/event-grid-event-hubs-integration/select-function.png)

1. Välj **Lägg till Event Grid-prenumeration**.

   ![Lägg till en prenumeration](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Ge Event Grid-prenumerationen ett namn. Använd **Event Hubs-namnområden** som händelsetyp. Ange värden för att välja din instans av Event Hubs-namnområdet. Lämna prenumerantens slutpunkt som det angivna värdet. Välj **Skapa**.

   ![Skapa en prenumeration](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Kör appen för att generera data

Nu har du slutfört konfigurationen av händelsehubben, SQL-informationslagret, Azure-funktionsappen och händelseprenumerationen. Lösningen är redo att migrera data från händelsehubben till informationslagret. Innan du kör ett program som genererar data för händelsehubben måste du konfigurera några värden.

1. Välj händelsehubbens namnområde på portalen. Välj **Anslutningssträngar**.

   ![Välj Anslutningssträngar](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Välj **RootManageSharedAccessKey**

   ![Välj nyckel](media/event-grid-event-hubs-integration/show-root-key.png)

3. Kopiera **Anslutningssträng – primär nyckel**

   ![Kopiera nyckel](media/event-grid-event-hubs-integration/copy-key.png)

4. Gå tillbaka till Visual Studio-projektet. Öppna **program.cs** i projektet WindTurbineDataGenerator.

5. Ersätt de två konstanta värdena. Använd det kopierade värdet för **EventHubConnectionString**. Använd händelsehubbnamnet **hubdatamigration**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skapa lösningen. Kör programmet WindTurbineGenerator.exe. Vänta några minuter och fråga sedan tabellen i informationslagret efter migrerade data.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* En introduktion till Event Hubs Capture finns i [Enable Event Hubs Capture using the Azure portal](../event-hubs/event-hubs-capture-enable-through-portal.md) (Aktivera Event Hubs Capture med hjälp av Azure Portal).
* Mer information om hur du konfigurerar och kör exemplet finns i [Event Hubs Capture- och Event Grid-exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).