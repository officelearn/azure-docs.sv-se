---
title: Azure DB Cosmos-diagnostikloggning | Microsoft Docs
description: "Använd den här självstudiekursen för att komma igång med Azure Cosmos DB loggning."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: mimig
ms.openlocfilehash: 14dce37a953984d3e1ce80b813f386210e85d261
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure DB Cosmos-diagnostikloggning

När du har börjat använda en eller flera Azure Cosmos DB-databaser, kanske du vill övervaka hur och när databaserna används. Diagnostiska loggar in Azure Cosmos DB kan du utföra denna övervakning. Genom att aktivera diagnostikloggning kan du skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och/eller exportera dem till [logganalys](https://azure.microsoft.com/services/log-analytics/), vilket är en del av [ Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

![Diagnostikloggning till lagring, Händelsehubbar eller Operations Management Suite via logganalys](./media/logging/azure-cosmos-db-logging-overview.png)

Använd den här kursen och kom igång med Azure Cosmos DB loggning via Azure-portalen, CLI eller PowerShell.

## <a name="what-is-logged"></a>Vad är inloggad?

* Alla autentiserade REST API för SQL-begäranden är inloggad, som innehåller misslyckade begäranden på grund av behörigheter för åtkomst, systemfel eller felaktiga begäranden. Stöd för MongoDB, diagram och tabell-API: er är inte tillgänglig.
* Åtgärder på själva databasen, som innehåller CRUD-åtgärder på alla dokument, behållare och databaser.
* Åtgärder på nycklar, vilket innefattar att skapa, ändra eller ta bort de här nycklarna.
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Till exempel förfrågningar som inte har någon ägartoken, som är felaktiga, som har upphört att gälla eller som har en ogiltig token.

## <a name="prerequisites"></a>Krav
Den här kursen behöver du följande resurser:

* En befintlig Azure Cosmos DB kontot, databas och behållare. Anvisningar om hur du skapar de här resurserna finns [skapa ett databaskonto i Azure Portal](create-sql-api-dotnet.md#create-a-database-account), [CLI prover](cli-samples.md), eller [PowerShell-exempel](powershell-samples.md).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Aktivera loggning i Azure-portalen

1. I den [Azure-portalen](https://portal.azure.com), i din Azure Cosmos DB konto, klickar du på **diagnostikloggar** i det vänstra navigeringsfönstret och klicka sedan på **aktivera diagnostiken**.

    ![Aktivera diagnostikloggning för Azure Cosmos DB i Azure-portalen](./media/logging/turn-on-portal-logging.png)

2. I den **diagnostikinställningar** gör följande: 

    * **Namn**. Ange ett namn för loggfilerna för att skapa.

    * **Arkivet till ett lagringskonto**. Om du vill använda det här alternativet behöver du ett befintligt lagringskonto för att ansluta till. Om du vill skapa ett nytt lagringskonto i portalen finns [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md) och följ instruktionerna för att skapa en Resource Manager allmänna konto. Återgå sedan till den här sidan i portalen för att markera ditt lagringskonto. Det kan ta några minuter för nyskapade lagringskonton ska visas i den nedrullningsbara menyn.
    * **Dataströmmen till en händelsehubb**. Om du vill använda det här alternativet behöver du en befintlig Event Hub-namnområde och händelsen hubb att ansluta till. Om du vill skapa ett namnområde för Händelsehubbar finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md). Återgå sedan till den här sidan i portalen för att välja Event Hub namnområde och principen.
    * **Skicka till logganalys**.     Om du vill använda det här alternativet Använd en befintlig arbetsyta eller skapa en ny logganalys-arbetsyta genom att följa stegen för att [skapa en ny arbetsyta](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) i portalen. Mer information om hur du visar loggarna i logganalys finns [visa loggar i logganalys](#view-in-loganalytics).
    * **Logga DataPlaneRequests**. Välj det här alternativet för att logga diagnostik för SQL, diagram och tabellen API-konton. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Logga MongoRequests**. Välj det här alternativet för att logga diagnostik för MongoDB-API-konton. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Mått begäranden**. Välj det här alternativet för att lagra utförliga data i [Azure mått](../monitoring-and-diagnostics/monitoring-supported-metrics.md#cosmosdb). Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.

3. Klicka på **Spara**.

    Om du får ett felmeddelande som säger ”det gick inte att uppdatera diagnostik för \<arbetsytans namn >. Prenumerationen \<prenumerations-id > är inte registrerad för att använda microsoft.insights ”. Följ den [felsöka Azure-diagnostik](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instruktioner för att registrera kontot, och försök sedan den här proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden, kan du återgå till den här sidan vid när som helst om du vill ändra diagnostiska logginställningar för ditt konto.

## <a name="turn-on-logging-using-cli"></a>Aktivera loggning med hjälp av CLI

Om du vill aktivera mätvärden och diagnostikloggning med hjälp av Azure CLI, använder du följande kommandon:

- Använd följande kommando för att aktivera lagring av diagnostiska loggar i ett Lagringskonto:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Den `resourceId` är namnet på Azure DB som Cosmos-konto. Den `storageId` är namnet på lagringskontot som du vill skicka loggar.

- Om du vill aktivera strömning av diagnostiska loggar till en Händelsehubb, Använd följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Den `resourceId` är namnet på Azure DB som Cosmos-konto. Den `serviceBusRuleId` är en sträng med formatet:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Använd följande kommando för att aktivera sändning av diagnostiska loggar till en logganalys-arbetsyta:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

## <a name="turn-on-logging-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

Om du vill aktivera loggning med hjälp av PowerShell behöver du Azure Powershell med en lägsta version av 1.0.1.

Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Om du redan har installerat Azure PowerShell och inte vet vilken version, från PowerShell-konsolen, Skriv `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Login-AzureRmAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer kan du behöva ange en som användes för att skapa Azure Key Vault. Skriv följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzureRmSubscription
```

Om du vill ange den prenumeration som är associerad med Azure DB som Cosmos-kontot du loggar in, skriver du sedan:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är kopplade till ditt konto är det viktigt att ange prenumerationen.
>
>

Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar
Men du kan använda ett befintligt lagringskonto för dina loggar, i den här självstudiekursen skapar vi ett nytt lagringskonto som är dedikerad till Azure Cosmos DB loggar. För enkelhetens skull Vi lagrar information om lagringskonto i en variabel med namnet **sa**.

För ytterligare enklare hantering i den här självstudiekursen använder vi samma resursgrupp som det innehåller vår Azure Cosmos-DB-databas. Ersätt värdena för ContosoResourceGroup contosocosmosdblogs och 'Norra centrala USA' för egna värden i tillämpliga fall:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Om du väljer att använda ett befintligt lagringskonto, den måste använda samma prenumeration som Azure DB som Cosmos-prenumeration och den måste använda Resource Manager-distributionsmodellen, i stället för den klassiska distributionsmodellen.
>
>

### <a id="identify"></a>Identifiera Azure DB som Cosmos-konto för loggarna
Ange namnet på Azure DB som Cosmos-kontot till en variabel med namnet **konto**, där ResourceName är namnet på Azure DB som Cosmos-konto.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Aktivera loggning
Använd cmdlet Set-AzureRmDiagnosticSetting tillsammans med variabler för det nya lagringskontot, Azure DB som Cosmos-konto och den kategori som du vill aktivera loggarna om du vill aktivera loggning för Azure Cosmos DB. Kör följande kommando, ange den **-aktiverad** flaggan till **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Utdata för kommandot bör likna följande:

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

Det här bekräftar att loggning har nu aktiverats för databasen, spara information till ditt lagringskonto.

Du kan också ange bevarandeprincip för loggar så att äldre loggar tas bort automatiskt. Ange till exempel bevarandeprincip genom att ange flaggan **- RetentionEnabled** till **$true** och ange parametern **- RetentionInDays** till **90** så att de loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Komma åt loggarna
Azure Cosmos-DB-loggarna för **DataPlaneRequests** kategori lagras i den **insights-loggar-data-plan-begäranden** behållare i storage-konto som du angav. 

Börja med att skapa en variabel för behållarens namn. Detta kommer att användas i resten av genomgången.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Om du vill visa alla blobbar i den här behållaren skriver du:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Följande utdata returneras för detta:

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

Som du kan se dessa utdata kan följa en namngivningskonvention blobar:`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom samma lagringskonto kan användas för att samla in loggar för olika resurser, är fullständiga resurs-ID i blobbnamnet mycket användbart för att komma åt eller hämta bara blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Börja med att skapa en mapp som du vill ladda ned blobbarna till. Till exempel:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Hämta sedan en lista över alla blobbar:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Skicka den här listan via ”Get-AzureStorageBlobContent” för att ladda ned blobbarna till målmappen:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

När du kör den här andra kommandot, den  **/**  avgränsare i blobbnamnen skapar en fullständig mappstruktur under målmappen. Den här mappstrukturen används för att ladda ned och lagra blobar som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera databaser och vill hämta loggar för en databas med namnet du CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Om du vill hämta alla loggar för månaden som juli 2017 använder `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Följande gäller också:

* Fråga status för diagnostikinställningar för din databas resurs:`Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Inaktivera loggning av **DataPlaneRequests** kategori för din databas konto resurs:`Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Blobbar som returneras i var och en av de här frågorna lagras som text, formateras som en JSON-blob som visas i följande kod. 

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

Mer information om data i varje JSON-blob, se [tolka Azure Cosmos DB loggarna](#interpret).

## <a name="managing-your-logs"></a>Hantera dina loggar

Loggar görs tillgängliga i ditt konto två timmar från den tidpunkt som Azure DB som Cosmos-åtgärden utfördes. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Kvarhållningsperioden för begäranden av plan arkiveras till ett lagringskonto har konfigurerats i portalen när **loggen DataPlaneRequests** är markerad. Om du vill ändra den här inställningen finns [aktiverar du loggning i Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Visa loggfiler i logganalys

Om du har valt den **skicka till logganalys** alternativet när du har aktiverat loggning diagnostikdata från din samling vidarebefordras till logganalys inom två timmar. Det innebär att om du tittar på logganalys omedelbart efter det att loggning inte visas några data. Bara vänta två timmar och försök igen. 

Innan du visar loggar vill du och se om logganalys-arbetsytan har uppgraderats för att använda nya Log Analytics-frågespråket. Du kan kontrollera detta genom att öppna den [Azure-portalen](https://portal.azure.com), klickar du på **logganalys** på långt till vänster, välj sedan namnet på arbetsytan som visas i följande bild. Den **OMS-arbetsytan** visas som visas i följande bild.

![Logganalys i Azure-portalen](./media/logging/azure-portal.png)

Om du ser följande meddelande på den **OMS-arbetsytan** sidan ditt arbetsområde har inte uppgraderats om du vill använda det nya språket. Mer information om hur du uppgraderar till nya frågespråket finns [uppgradera Azure logganalys-arbetsytan till ny logg sökning](../log-analytics/log-analytics-log-search-upgrade.md). 

![Uppgradera meddelande om log analytics](./media/logging/upgrade-notification.png)

Om du vill visa dina diagnostiska data i logganalys öppna loggen söksidan från den vänstra menyn eller området på sidan som visas i följande bild.

![Logga Sökalternativ i Azure-portalen](./media/logging/log-analytics-open-log-search.png)

Nu när du har aktiverat datainsamling, kör följande loggen Sök exempel använder det nya språket i fråga för att se de senaste tio loggarna `AzureDiagnostics | take 10`.

![Exempel ta 10 log-sökning](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Frågor

Här följer några ytterligare frågor som du kan ange i den **loggen Sök** rutan för att övervaka Azure DB som Cosmos-behållare. De här frågorna arbeta med den [nytt språk](../log-analytics/log-analytics-log-search-upgrade.md). 

Läs om innebörden av data som returneras av varje logg sökning i [tolka Azure Cosmos DB loggarna](#interpret).

* Alla diagnostiska loggar från Azure Cosmos-databasen för den angivna tidsperioden.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Tio senaste loggade händelser.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Alla åtgärder, grupperade efter åtgärdstyp.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Alla åtgärder, grupperade efter resurs.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* All användaraktivitet, grupperade efter resurs. Observera att detta är ett aktivitetsloggen, inte en diagnostiska logg.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Vilka åtgärder ta längre tid än 3 millisekunder.

    ```
    AzureDiagnostics | where toint(duration_s) > 3000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Vilken agent körs åtgärderna.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* När utfördes långvariga åtgärder.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Mer information om hur du använder det nya loggen Sök-språket finns [förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Tolka loggarna

Diagnostiska data som lagras i Azure Storage- och Log Analytics använder ett mycket lik schema. 

I följande tabell beskrivs innehållet i varje loggpost.

| Azure Storage-fältet eller egenskapen | Log Analytics-egenskap | Beskrivning |
| --- | --- | --- |
| time | TimeGenerated | Datum och tid (UTC) när åtgärden utfördes. |
| resourceId | Resurs | Azure DB som Cosmos-konto som loggar är aktiverad.|
| category | Kategori | För Azure Cosmos DB loggar är DataPlaneRequests bara tillgängliga värde. |
| operationName | OperationName | Namnet på åtgärden. Det här värdet kan vara något av följande åtgärder: skapa, uppdatera, Läs-, ReadFeed, ta bort, ersätta, Execute, SqlQuery, fråga, JSQuery, Head, HeadFeed eller Upsert.   |
| properties | Saknas | Innehållet i det här fältet beskrivs i följande rader. |
| ActivityId | activityId_g | Unikt GUID för den loggade åtgärden. |
| UserAgent | userAgent_s | En sträng som anger klientanvändaragent begäran utfördes. Formatet är {användarnamn för agenten} / {version}.|
| resourceType | ResourceType | Typ av resurs som används. Det här värdet kan vara något av följande resurstyper: databas, samling, dokument, bifogad fil, användare, behörighet, StoredProcedure, utlösare, UserDefinedFunction eller erbjudandet. |
| statuskod |statusCode_s | Svarsstatus för åtgärden. |
| requestResourceId | Resurs-ID | Resurs-ID som rör begäran, kan peka databaseRid, collectionRid eller documentRid beroende på den åtgärd som utförs.|
| clientIpAddress | clientIpAddress_s | Klientens IP-adress. |
| requestCharge | requestCharge_s | Antalet RUs som används av åtgärden |
| collectionRid | collectionId_s | Unikt ID för samlingen.|
| Varaktighet | duration_s | Varaktighet för åtgärden i skalstreck. |
| requestLength | requestLength_s | Längden på begäran, i byte. |
| responseLength | responseLength_s | Längden i svaret, i byte.|
| resourceTokenUserRid | resourceTokenUserRid_s | Detta är inte tom när [resurs token](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering och pekar på resurs-ID för användaren. |

## <a name="next-steps"></a>Nästa steg

- För att få en bättre förståelse av inte bara hur du aktiverar loggning men mått och logga kategorier som stöds av olika Azure services läsa både den [översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [översikt av Azure Diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artiklar.
- Läs artiklarna att lära dig om händelsehubbar:
   - [Vad är Händelsehubbar i Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Läs [hämta mätvärden och diagnostiska loggar från Azure Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Läs [förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md)
