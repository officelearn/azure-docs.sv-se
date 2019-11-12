---
title: Diagnostikloggning i Azure Cosmos DB
description: Lär dig mer om olika sätt att logga och övervaka data som lagras i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: bdbc50983708327cf5d3857282c92fcab1c28b09
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930537"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Diagnostikloggning i Azure Cosmos DB 

När du har startat en eller flera Azure Cosmos-databaser kanske du vill övervaka hur och när dina databaser används. Den här artikeln innehåller en översikt över de loggar som är tillgängliga på Azure-plattformen. Du får lära dig hur du aktiverar diagnostikloggning för övervakning för att skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), hur du strömmar loggar till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och hur du exporterar loggar till [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Loggar som är tillgängliga i Azure

Innan vi pratar om hur du övervakar ditt Azure Cosmos DB-konto ska vi förklara några saker om loggning och övervakning. Det finns olika typer av loggar på Azure-plattformen. Det finns [Azure-aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure-diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure-mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), händelser, pulsslags övervakning, drift loggar och så vidare. Det finns en mängd olika av loggarna. Du kan se den fullständiga listan över loggar i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) i Azure Portal. 

Följande bild visar de olika typerna av Azure-loggar som är tillgängliga:

![Olika typer av Azure-loggar](./media/logging/azurelogging.png)

I avbildningen representerar **beräknings resurserna** de Azure-resurser som du kan använda för att få åtkomst till Microsofts gäst operativ system. Till exempel betraktas Azure Virtual Machines, skalnings uppsättningar för virtuella datorer, Azure Container Service och så vidare, beräknings resurser. Beräknings resurser genererar aktivitets loggar, diagnostikloggar och program loggar. Mer information finns i [övervaknings data källorna i Azure](../azure-monitor/platform/data-sources.md) -artikeln.

**Icke-Compute-resurser** är resurser där du inte kan komma åt det underliggande operativ systemet och arbeta direkt med resursen. Till exempel nätverks säkerhets grupper, Logic Apps och så vidare. Azure Cosmos DB är en icke-beräknings resurs. Du kan visa loggar för icke-Compute-resurser i aktivitets loggen eller aktivera alternativet diagnostiska loggar i portalen. Mer information finns [i data källor i Azure Monitor](../azure-monitor/platform/data-sources.md) artikel.

Aktivitets loggen registrerar åtgärderna på en prenumerations nivå för Azure Cosmos DB. Åtgärder som Listnycklar, Write DatabaseAccounts och More loggas. Diagnostiska loggar ger mer detaljerad loggning och gör att du kan logga DataPlaneRequests (skapa, läsa, fråga och så vidare) och MongoRequests.


I den här artikeln fokuserar vi på Azures aktivitets logg, Azure-diagnostikloggar och Azure-mått. Vad är skillnaden mellan dessa tre loggar? 

### <a name="azure-activity-log"></a>Azure-aktivitetslogg

Azure aktivitets loggen är en prenumerations logg som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Aktivitets loggen rapporterar kontroll Plans händelser för dina prenumerationer under kategorin administration. Du kan använda aktivitets loggen för att fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på resurserna i din prenumeration. Du kan också förstå status för åtgärden och andra relevanta egenskaper. 

Aktivitets loggen skiljer sig från diagnostikloggar. Aktivitets loggen innehåller information om åtgärder på en resurs utanför ( _kontroll planet_). I Azure Cosmos DB-kontexten inkluderar kontroll Plans åtgärder skapa behållare, lista nycklar, ta bort nycklar, list databas och så vidare. Diagnostikloggar genereras av en resurs och ger information om hur resursen fungerar ( _data planet_). Några exempel på data Plans åtgärderna i Diagnostic-loggen är Delete-, INSERT-och ReadFeed.

Aktivitets loggar (kontroll Plans åtgärder) kan vara rikare och kan innehålla den fullständiga e-postadressen för anroparen, anroparens IP-adress, resurs namn, åtgärds namn, TenantId med mera. Aktivitets loggen innehåller flera data [Kategorier](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) . Fullständig information om scheman för dessa kategorier finns i [händelse schema för Azure aktivitets logg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostiska loggar kan dock vara restriktiva eftersom personliga data ofta tas bort från dessa loggar. Du kanske har IP-adressen för anroparen, men den sista octant tas bort.

### <a name="azure-metrics"></a>Azure-mått

[Azure-mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) har den viktigaste typen av Azure-telemetridata (kallas även _prestanda räknare_) som genereras av de flesta Azure-resurser. Med mått kan du Visa information om data flöde, lagring, konsekvens, tillgänglighet och svars tid för dina Azure Cosmos DB-resurser. Mer information finns i [övervakning och fel sökning med mått i Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure Diagnostic-loggar

Azure Diagnostic-loggar genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Dessa loggar samlas in per begäran. Innehållet i dessa loggar varierar beroende på resurs typ. Diagnostikloggar på resurs nivå skiljer sig också från diagnostikloggar på gäst operativ system nivå. Diagnostikloggar för gäst operativ system samlas in av en agent som körs i en virtuell dator eller annan resurs typ som stöds. Diagnostikloggar på resurs nivå kräver ingen agent och samlar inte in resurs information från själva Azure-plattformen. Gäst operativ loggar på operativ system nivå fångar data från det operativ system och program som körs på en virtuell dator.

![Diagnostisk loggning till lagring, Event Hubs eller Azure Monitor loggar](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Vad loggas med Azure-diagnostikloggar?

* Alla autentiserade Server dels begär Anden (TCP/REST) över alla API: er loggas, inklusive misslyckade förfrågningar till följd av åtkomst behörigheter, systemfel eller felaktiga begär Anden. Stöd för användarinitierade diagram, Cassandra och Tabell-API begär Anden är inte tillgängliga för närvarande.
* Åtgärder i själva databasen, inklusive CRUD-åtgärder på alla dokument, behållare och databaser.
* Åtgärder för konto nycklar, bland annat att skapa, ändra eller ta bort nycklar.
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Till exempel begär Anden som inte har en Bearer-token eller som har fel format eller som upphör att gälla, eller har en ogiltig token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Aktivera loggning i Azure Portal

Använd följande steg för att aktivera diagnostisk loggning i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Navigera till ditt Azure Cosmos-konto. Öppna fönstret **diagnostikinställningar** och välj sedan alternativet för att **lägga till diagnostiska** inställningar.

    ![Aktivera diagnostisk loggning för Azure Cosmos DB i Azure Portal](./media/logging/turn-on-portal-logging.png)

1. Fyll i formuläret med följande information på sidan **diagnostikinställningar** : 

    * **Namn**: Ange ett namn för loggarna som ska skapas.

    * Du kan lagra loggarna till följande tjänster:

      * **Arkivera till ett lagrings konto**: om du vill använda det här alternativet behöver du ett befintligt lagrings konto för att ansluta till. Information om hur du skapar ett nytt lagrings konto i portalen finns i artikeln [skapa ett lagrings konto](../storage/common/storage-create-storage-account.md) . Gå sedan tillbaka till fönstret diagnostiska inställningar i Azure Cosmos db i portalen för att välja ditt lagrings konto. Det kan ta några minuter för nyligen skapade lagrings konton att visas på den nedrullningsbara menyn.

      * **Strömma till en Event Hub**: om du vill använda det här alternativet måste du ha ett befintligt Event Hubs-namnområde och händelsehubben för att kunna ansluta till. Om du vill skapa en Event Hubs namnrymd, se [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md). Gå sedan tillbaka till den här sidan i portalen för att välja namn området för Händelsehubben och princip namnet.

      * **Skicka till Log Analytics**: om du vill använda det här alternativet använder du antingen en befintlig arbets yta eller skapar en ny Log Analytics arbets yta genom att följa stegen för att [skapa en ny arbets yta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) i portalen. 

   * Du kan logga följande data:

      * **DataPlaneRequests**: Välj det här alternativet om du vill logga backend-begäranden till alla API: er som innehåller SQL-, Graph-, MongoDB-, Cassandra-och tabell-API konton i Azure Cosmos dB. Om du försöker arkivera till ett lagrings konto kan du välja kvarhållningsperioden för diagnostikloggar. Loggarna raderas automatiskt när kvarhållningsperioden upphör att gälla. Följande JSON-data är ett exempel på utdata som loggas med DataPlaneRequests. Nyckel egenskaper att observera är: Requestcharge, statusCode, clientIPaddress och partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Välj det här alternativet om du vill logga användarinitierade begär Anden från klient delen för att betjäna begär anden till Azure Cosmos DB s API för MongoDB. MongoDB-begäranden visas i MongoRequests och DataPlaneRequests. Om du försöker arkivera till ett lagrings konto kan du välja kvarhållningsperioden för diagnostikloggar. Loggarna raderas automatiskt när kvarhållningsperioden upphör att gälla. Följande JSON-data är ett exempel på utdata som loggas med MongoRequests. Viktiga egenskaper att observera är: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Välj det här alternativet om du vill logga frågetexten som kördes.  Följande JSON-data är ett exempel på utdata som loggas med QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: den här loggen rapporterar statistik över partitionsnyckel. För närvarande representeras statistiken med lagrings storleken (KB) för partitionens nycklar. Loggen genereras mot de första tre partitionsuppsättningar som upptar merparten data lagring.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Mått begär Anden**: Välj det här alternativet om du vill lagra utförliga data i [Azure-mått](../azure-monitor/platform/metrics-supported.md). Om du försöker arkivera till ett lagrings konto kan du välja kvarhållningsperioden för diagnostikloggar. Loggarna raderas automatiskt när kvarhållningsperioden upphör att gälla.

3. Välj **Spara**.

    Om du får ett fel meddelande om att det inte gick att uppdatera diagnostiken för \<arbets ytans namn >. Prenumerations \<prenumerations-ID > har inte registrerats för att använda Microsoft. Insights, "Följ [fel söknings Azure-diagnostik](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) -instruktionerna för att registrera kontot och gör sedan om proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden går du tillbaka till den här sidan för att ändra inställningarna för diagnostikloggar för ditt konto.

## <a name="turn-on-logging-by-using-azure-cli"></a>Aktivera loggning med hjälp av Azure CLI

Använd följande kommandon om du vill aktivera mått och diagnostikloggning med Azure CLI:

- Om du vill aktivera lagring av diagnostikloggar i ett lagrings konto använder du följande kommando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` är namnet på det Azure Cosmos DB kontot. Resursen har formatet "/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/< Azure_Cosmos_account_name >" `storage-account` är namnet på det lagrings konto som du vill skicka loggarna till. Du kan logga andra loggar genom att uppdatera kategori parameter värden till "MongoRequests" eller "DataPlaneRequests". 

- Använd följande kommando för att aktivera strömning av diagnostikloggar till en Event Hub:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` är namnet på det Azure Cosmos DB kontot. `event-hub-rule` är regel-ID för Event Hub. 

- Om du vill aktivera sändning av diagnostikloggar till en Log Analytics arbets yta använder du följande kommando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

Om du vill aktivera diagnostisk loggning med hjälp av PowerShell behöver du Azure PowerShell med en lägsta version av 1.0.1.

Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Om du redan har installerat Azure PowerShell och inte vet versionen från PowerShell-konsolen skriver du `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell hämtar alla prenumerationer som är associerade med det här kontot och använder som standard det första.

Om du har mer än en prenumeration kanske du måste ange den angivna prenumerationen som användes för att skapa ditt Azure Key Vault. Om du vill se prenumerationerna för ditt konto skriver du följande kommando:

```powershell
Get-AzSubscription
```

Ange sedan följande kommando för att ange den prenumeration som är associerad med det Azure Cosmos DB konto som du loggar in:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har mer än en prenumeration som är kopplad till ditt konto är det viktigt att ange den prenumeration som du vill använda.
>
>

Mer information om hur du konfigurerar Azure PowerShell finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar
Även om du kan använda ett befintligt lagrings konto för dina loggar, skapar vi ett nytt lagrings konto som är dedikerat för Azure Cosmos DB loggar i den här självstudien. För enkelhetens skull lagrar vi lagrings konto informationen i en variabel med namnet **sa**.

I den här självstudien använder vi samma resurs grupp som den som innehåller Azure Cosmos-databasen för att under lätta hanteringen. Ersätt dina värden för parametrarna **ContosoResourceGroup**, **Contosocosmosdblogs**och **norra centrala USA** , i tillämpliga fall:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Om du väljer att använda ett befintligt lagrings konto måste kontot använda samma prenumeration som Azure Cosmos DB prenumerationen. Kontot måste också använda distributions modellen för Resource Manager i stället för den klassiska distributions modellen.
>
>

### <a id="identify"></a>Identifiera Azure Cosmos DB konto för loggarna
Ange Azure Cosmos DB konto namn till en variabel med namnet **konto**, där **resourceName** är namnet på det Azure Cosmos DB kontot.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Aktivera loggning
Om du vill aktivera loggning för Azure Cosmos DB använder du cmdleten `Set-AzDiagnosticSetting` med variabler för det nya lagrings kontot, Azure Cosmos DB-konto och den kategori som ska aktive ras för loggning. Kör följande kommando och ange flaggan **-Enabled** till **$True**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Kommandots utdata bör likna följande exempel:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Utdata från kommandot bekräftar att loggning nu har Aktiver ATS för din databas och att informationen sparas till ditt lagrings konto.

Om du vill kan du också ange bevarande principen för loggarna, så att äldre loggar tas bort automatiskt. Ange till exempel bevarande principen med flaggan **-RetentionEnabled** inställd på **$True**. Ange parametern **-RetentionInDays** till **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Komma åt loggarna
Azure Cosmos DB loggar för kategorin **DataPlaneRequests** lagras i behållaren **Insights-logs-DataPlaneRequests** i det lagrings konto som du har angett. 

Börja med att skapa en variabel för containerns namn. Variabeln används i genom gången.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Om du vill visa en lista över alla blobar i den här behållaren skriver du:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Kommandots utdata bör likna följande exempel:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Som du kan se från dessa utdata följer Blobbarna en namngivnings konvention: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom samma lagrings konto kan användas för att samla in loggar för flera resurser kan du använda det fullständigt kvalificerade resurs-ID: t i BLOB-namnet för att komma åt och hämta de angivna blobbar som du behöver. Innan vi gör det tar vi upp hur du laddar ned alla blobbar.

Börja med att skapa en mapp som du vill ladda ned blobbarna till. Exempel:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Sedan hämtar du en lista över alla blobbar:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Skicka den här listan via kommandot `Get-AzStorageBlobContent` för att ladda ned Blobbarna till målmappen:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

När du kör det här andra kommandot skapar **/** avgränsaren i BLOB-namnen en fullständig mappstruktur under målmappen. Den här mappstrukturen används för att ladda ned och lagra Blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera databaser och vill hämta loggar för bara en databas med namnet **CONTOSOCOSMOSDB3**, använder du kommandot:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Om du har flera resurs grupper och bara vill hämta loggar för en enda resurs grupp, använder du kommandot `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Om du vill ladda ned alla loggar för den månad juli 2017 använder du kommandot `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Du kan också köra följande kommandon:

* Använd kommandot `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`för att fråga om status för diagnostiska inställningar för databas resursen.
* Om du vill inaktivera loggning av **DataPlaneRequests** -kategorin för databas konto resursen använder du kommandot `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


De blobbar som returneras i var och en av dessa frågor lagras som text och formateras som en JSON-BLOB, som du ser i följande kod:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Mer information om data i varje JSON-BLOB finns i [tolka dina Azure Cosmos DB loggar](#interpret).

## <a name="manage-your-logs"></a>Hantera dina loggar

Diagnostikloggar görs tillgängliga i ditt konto i två timmar från den tidpunkt då Azure Cosmos DB åtgärden gjordes. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd Azures standard metoder för åtkomst kontroll för att skydda dina loggar och begränsa vem som har åtkomst till dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Kvarhållningsperioden för data Plans begär Anden som arkiveras till ett lagrings konto konfigureras i portalen när inställningen **Logga DataPlaneRequests** är markerad. Om du vill ändra inställningen, se [Aktivera loggning i Azure Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Visa loggar i Azure Monitor-loggar

Om du har valt alternativet **Skicka till Log Analytics** när du aktiverade diagnostisk loggning vidarebefordras diagnostikdata från din behållare till Azure Monitor loggar inom två timmar. När du tittar på Azure Monitor loggar direkt efter att du har aktiverat loggning ser du inga data. Vänta bara två timmar och försök igen. 

Innan du visar loggarna kan du kontrol lera om din Log Analytics arbets yta har uppgraderats för att använda det nya Kusto-frågespråket. Om du vill kontrol lera detta öppnar du [Azure Portal](https://portal.azure.com), väljer **Log Analytics arbets ytor** längst till vänster och väljer sedan namnet på arbets ytan som visas i nästa bild. Sidan **Log Analytics arbets yta** visas:

![Azure Monitor loggar i Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.  

Om du ser följande meddelande på sidan **Log Analytics arbets yta** har din arbets yta inte uppgraderats för att använda det nya språket. Mer information om hur du uppgraderar till det nya frågespråket finns i [uppgradera din Azure Log Analytics-arbetsyta till ny loggs ökning](../log-analytics/log-analytics-log-search-upgrade.md). 

![Azure Monitor loggar uppgraderings meddelande](./media/logging/upgrade-notification.png)

Om du vill visa dina diagnostikdata i Azure Monitor loggar öppnar du sidan **loggs ökning** på den vänstra menyn eller i **hanterings** ytan på sidan, som du ser i följande bild:

![Loggs öknings alternativ i Azure Portal](./media/logging/log-analytics-open-log-search.png)

Nu när du har aktiverat data insamling kör du följande loggs öknings exempel med hjälp av det nya frågespråket för att se de 10 senaste loggarna `AzureDiagnostics | take 10`.

![Exempel på loggs ökning för de 10 senaste loggarna](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="cosmosdb-log-analytics-queries-in-azure-monitor"></a>CosmosDB Log Analytics frågor i Azure Monitor

Här följer några ytterligare frågor som du kan ange i rutan **loggs ökning** som hjälper dig att övervaka dina Azure Cosmos-behållare. Dessa frågor fungerar med det [nya språket](../log-analytics/log-analytics-log-search-upgrade.md).  

Information om betydelsen av de data som returneras av varje loggs ökning finns i [tolka dina Azure Cosmos DB loggar](#interpret).

* Fråga efter alla diagnostikloggar från Azure Cosmos DB under en angiven tids period:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Fråga efter de 10 senast loggade händelserna:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* För att fråga efter alla åtgärder, grupperat efter åtgärds typ:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* För att fråga efter alla åtgärder, grupperat efter **resurs**:

    ```
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

* För att fråga efter all användar aktivitet, grupperad efter resurs:

    ```
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
    > [!NOTE]
    > Det här kommandot gäller för en aktivitets logg, inte en diagnostisk logg.

* Så här hämtar du alla frågor som är större än 100 ru: er kopplade till data från DataPlaneRequests och QueryRunTimeStatistics

    ```
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```
    
      

* Fråga efter vilka åtgärder tar längre tid än 3 millisekunder:

    ```
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Fråga efter vilken agent som kör åtgärderna:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* För att fråga efter när tids krävande åtgärder utfördes:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | render timechart
    ```
    
* Så här hämtar du nyckel statistik för att utvärdera snedheten för de tre översta partitionerna för databas konto:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    
   
    ```
    

Mer information om hur du använder det nya logg Sök språket finns i [förstå loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Tolka dina loggar

Diagnostiska data som lagras i Azure Storage och Azure Monitor loggar använder ett liknande schema. 

I följande tabell beskrivs innehållet i varje loggpost.

| Azure Storage fält eller egenskap | Azure Monitor loggar egenskap | Beskrivning |
| --- | --- | --- |
| **tid** | **TimeGenerated** | Datum och tid (UTC) när åtgärden utfördes. |
| **resourceId** | **Resurs** | Azure Cosmos DB konto för vilka loggar är aktiverade.|
| **kategori** | **Kategori** | **DataPlaneRequests** är det enda tillgängliga värdet för Azure Cosmos DB loggar. |
| **operationName** | **OperationName** | Åtgärdens namn. Det här värdet kan vara någon av följande åtgärder: skapa, uppdatera, läsa, ReadFeed, ta bort, ersätta, köra, SqlQuery, fråga, JSQuery, Head, HeadFeed eller upsert.   |
| **egenskaperna** | Saknas | Innehållet i det här fältet beskrivs i raderna som följer. |
| **activityId** | **activityId_g** | Unikt GUID för den loggade åtgärden. |
| **userAgent** | **userAgent_s** | En sträng som anger den klient användar agent som utför begäran. Formatet är {user agent Name}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Typ av resurs som används. Det här värdet kan vara någon av följande resurs typer: databas, behållare, dokument, bilaga, användare, behörighet, StoredProcedure, utlösare, UserDefinedFunction eller erbjudande. |
| **statusCode** | **statusCode_s** | Åtgärdens svars status. |
| **requestResourceId** | **ResourceId** | Det resourceId som hör till begäran. Värdet kan peka på databaseRid, collectionRid eller documentRid beroende på vilken åtgärd som utförts.|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **requestCharge** | **requestCharge_s** | Antalet ru: er som används av åtgärden |
| **collectionRid** | **collectionId_s** | Samlingens unika ID.|
| **giltighet** | **duration_s** | Åtgärdens varaktighet i millisekunder. |
| **requestLength** | **requestLength_s** | Längden på begäran, i byte. |
| **responseLength** | **responseLength_s** | Svarets längd i byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är icke-tomt när [resurs-token](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet pekar på användarens resurs-ID. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar loggning och även de mått och logg kategorier som stöds av de olika Azure-tjänsterna finns [i både översikten över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [Översikt över artiklar om Azure Diagnostic-loggar](../azure-monitor/platform/resource-logs-overview.md) .
- Läs de här artiklarna om du vill veta mer om Event Hub:
   - [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Läs [hämtnings statistik och diagnostikloggar från Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Läs [förstå loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-search-new.md).
