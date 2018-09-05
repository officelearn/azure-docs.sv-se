---
title: Migrera Azure Event Hubs-data till SQL Data Warehouse | Microsoft Docs
description: I den här självstudien lär du dig att samla in data från din händelsehubb till ett SQL Data Warehouse med en Azure-funktion som utlöses av ett Event Grid (händelserutnät).
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.date: 08/27/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 9673a7bff8e2d22764be28abef807434c53cc552
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145161"
---
# <a name="process-and-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Bearbeta och migrera insamlade Event Hubs-data till ett SQL Data Warehouse med Event Grid och Azure Functions

Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) är det enklaste sättet att automatiskt leverera strömmade data i Event Hubs till Azure Blob-lagring eller Azure Data Lake-lagring. Du kan sedan bearbeta och leverera data till andra lagringsmål, som SQL Data Warehouse eller Cosmos DB. I den här självstudien lär du dig att samla in data från din händelsehubb till ett SQL Data Warehouse med en Azure-funktion som utlöses av ett [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) (händelserutnät).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Först måste du skapa en händelsehubb med funktionen **Capture** aktiverad, och ange en Azure Blob-lagringsplats som mål. Data som genereras av WindTurbineGenerator strömmas till händelsehubben och samlas automatiskt in till Azure Storage som Avro-filer. 
*   Sedan skapar du en Azure Event Grid-prenumeration med Event Hubs-namnområdet som källa och Azure Function-slutpunkten som mål.
*   När en ny Avro-fil skickas till blob-lagring i Azure Storage av funktionen Event Hubs Capture, meddelar Event Grid blob-lagringens URI till Azure Function. Därefter migreras data av Function från blob-lagringen till ett SQL Data Warehouse.

I de här självstudierna gör du följande: 

> [!div class="checklist"]
> * Distribuera infrastrukturen
> * Publicera kod till en Functions-app
> * Skapa en Event Grid-prenumeration från Functions-appen
> * Strömma exempeldata till Event Hub. 
> * Verifiera insamlade data i SQL Data Warehouse

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Visual Studio 2017 version 15.3.2 eller senare](https://www.visualstudio.com/vs/). Vid installationen kontrollerar du att du installerar följande arbetsbelastningar: .NET-skrivbordsutveckling, Azure-utveckling, ASP.NET- och webbutveckling, Node.js-utveckling och Python-utveckling
- Ladda ned [Git-exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo). Lösningsexemplet innehåller följande komponenter:
    - *WindTurbineDataGenerator* – en enkel utgivare som skickar exempeldata från en vindturbin till en Capture-aktiverad händelsehubb
    - *FunctionDWDumper* – en Azure-funktion som tar emot ett Event Grid-meddelande när en Avro-fil hämtas till blob-lagring i Azure Storage. Den tar emot blobbens URI-sökväg, läser innehållet och skickar data till ett SQL Data Warehouse.

### <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen
Använd Azure PowerShell eller Azure CLI för att distribuera den infrastruktur som behövs för den här självstudien med hjälp av den här [Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Den här mallen skapar följande resurser:

-   Event Hub med funktionen Capture aktiverad
-   Lagringskonto för insamlade händelsedata
-   Azure App Service-plan som värd för funktionsappen
-   Funktionsapp för bearbetning av insamlade händelsefiler
-   SQL Server som värd för informationslagret
-   SQL Data Warehouse för lagring av migrerade data

Följande avsnitt innehåller Azure CLI- och Azure PowerShell-kommandon för distribution av den infrastruktur som krävs för den här självstudien. Uppdatera namnen på följande objekt innan du kör kommandona: 

- Azure-resursgrupp 
- Resursgruppens område eller region
- Event Hubs-namnområde
- Händelsehubb
- Azure SQL-server
- SQL-användare (och lösenord)
- Azure SQL-databas
- Azure Storage 
- Azure Functions App

Det tar en stund för de här skripten att skapa alla Azure-artefakter. Vänta tills skriptet har slutförts innan du fortsätter. Om distributionen av någon anledning misslyckas tar du bort resursgruppen, åtgärdar det rapporterade problemet och kör sedan kommandot igen. 

#### <a name="azure-cli"></a>Azure CLI
Om du vill distribuera mallen med Azure CLI kör du följande kommandon:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Om du vill distribuera mallen med PowerShell kör du följande kommandon:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Skapa en tabell i SQL Data Warehouse 
Skapa en tabell i SQL-informationslagret genom att köra skriptet [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) med [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) eller Frågeredigeraren i portalen. 

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

## <a name="publish-code-to-the-functions-app"></a>Publicera kod till Functions-appen

1. Öppna lösningen *EventHubsCaptureEventGridDemo.sln* i Visual Studio 2017 (15.3.2 eller senare). 

1. Högerklicka på *FunctionEGDWDumper* i Solution Explorer och välj **Publicera**.

   ![Publicera funktionsapp](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Välj **Azure-funktionsapp** och **Välj befintlig**. Välj **Publicera**.

   ![Målfunktionsapp](./media/store-captured-data-data-warehouse/pick-target.png)

1. Välj den funktionsapp som du distribuerade via mallen. Välj **OK**.

   ![Välj funktionsapp](./media/store-captured-data-data-warehouse/select-function-app.png)

1. När profilen har konfigurerats i Visual Studio väljer du **Publicera**.

   ![Välj Publicera](./media/store-captured-data-data-warehouse/select-publish.png)

När du har publicerat funktionen är du klar att prenumerera på insamlingshändelsen från Event Hubs!


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Skapa en Event Grid-prenumeration från Functions-appen
 
1. Gå till [Azure-portalen](https://portal.azure.com/). Välj din resursgrupp och funktionsapp.

   ![Visa funktionsapp](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Välj funktionen.

   ![Välj funktion](./media/store-captured-data-data-warehouse/select-function.png)

1. Välj **Lägg till Event Grid-prenumeration**.

   ![Lägg till en prenumeration](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Ge Event Grid-prenumerationen ett namn. Använd **Event Hubs-namnområden** som händelsetyp. Ange värden för att välja din instans av Event Hubs-namnområdet. Lämna prenumerantens slutpunkt som det angivna värdet. Välj **Skapa**.

   ![Skapa en prenumeration](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generera exempeldata  
Nu har du konfigurerat Event Hub, SQL-informationslagret, Azure-funktionsappen och Event Grid-prenumerationen. Du kan köra WindTurbineDataGenerator.exe för att generera dataströmmar till Event Hub när du har uppdaterat anslutningssträngen och namnet på din händelsehubb i källkoden. 

1. Välj händelsehubbens namnområde på portalen. Välj **Anslutningssträngar**.

   ![Välj Anslutningssträngar](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Välj **RootManageSharedAccessKey**

   ![Välj nyckel](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Kopiera **Anslutningssträng – primär nyckel**

   ![Kopiera nyckel](./media/store-captured-data-data-warehouse/copy-key.png)

4. Gå tillbaka till Visual Studio-projektet. Gå till projektet *WindTurbineDataGenerator* och öppna *program.cs*.

5. Uppdatera värdena för **EventHubConnectionString** och **EventHubName** med anslutningssträngen och namnet på din händelsehubb. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Kompilera lösningen och kör sedan programmet WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Verifiera insamlade data i informationslagret
Vänta några minuter och skicka sedan en fråga till tabellen i informationslagret. Du kan se att de data som genererats av WindTurbineDataGenerator har strömmats till din Event Hub, samlats in i en Azure Storage-container och sedan migrerats till SQL Data Warehouse-tabellen av Azure Function.  

## <a name="next-steps"></a>Nästa steg 
Du kan använda kraftfulla verktyg för datavisualisering med ditt informationslager för att få värdefull kunskap.

Den här artikeln visar hur du använder [Power BI med SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi)



