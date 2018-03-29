---
title: Azure DB Cosmos-diagnostikloggning | Microsoft Docs
description: Använd den här självstudiekursen för att komma igång med Azure Cosmos DB loggning.
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mimig
ms.openlocfilehash: 3b7c9f1acd79a2f170ecead9dedd200ad37d9388
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure DB Cosmos-diagnostikloggning

När du börjar använda en eller flera Azure Cosmos DB-databaser, kanske du vill övervaka hur och när databaserna används. Den här artikeln innehåller en översikt över de loggar som är tillgängliga på Azure-plattformen. Du lär dig att aktivera diagnostikloggning för övervakning att skicka loggar för [Azure Storage](https://azure.microsoft.com/services/storage/), så att strömma loggarna [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och hur du exporterar loggar till [Azure logganalys ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Loggar som är tillgängliga i Azure

Innan vi om hur du övervakar Azure Cosmos DB kontot tydliggöra vi några saker om loggning och övervakning. Det finns olika typer av loggar på Azure-plattformen. Det finns [Azure aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), händelser, övervakning av pulsslag, operations loggar och så vidare. Det finns en mängd olika loggar. Du kan se en fullständig lista över loggar i [Azure logganalys](https://azure.microsoft.com/en-us/services/log-analytics/) i Azure-portalen. 

Följande bild visar de olika typerna av Azure loggar som är tillgängliga:

![Olika typer av Azure-loggar](./media/logging/azurelogging.png)

I bild av **beräkningsresurser** representerar Azure-resurser som du kan komma åt Microsoft gäst-OS. Till exempel virtuella Azure-datorer, virtuella skala uppsättningar, Azure Container Service och så vidare är övervägt beräkningsresurser. Beräkna resurser generera aktivitetsloggar och diagnostikloggar programloggar. Mer information finns i den [Azure-övervakning: beräkningsresurser](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset) artikel.

Den **icke-beräkningsresurser** finns resurser som du inte kan komma åt det underliggande Operativsystemet och arbeta direkt med resursen. Nätverkssäkerhetsgrupper, Logikappar och så vidare. Azure Cosmos-DB är en beräkningsresurser. Du kan visa loggar för icke-beräkningsresurser i aktivitetsloggen eller aktiverar diagnostikloggar i portalen. Mer information finns i den [Azure-övervakning: icke-beräkning resurser](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else) artikel.

Aktivitetsloggen registrerar åtgärder på en prenumerationsnivå för Azure Cosmos DB. Åtgärder som ListKeys och skriva DatabaseAccounts loggas. Diagnostikloggar ger mer detaljerad loggning och att du loggar DataPlaneRequests (skapa, läsa, fråga och så vidare) och MongoRequests.


I den här artikeln fokuserar vi på Azure-aktivitetsloggen, Azure diagnostikloggar och Azure mått. Vad är skillnaden mellan dessa tre loggar? 

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen

Azure-aktivitetsloggen är en prenumerationslogg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Aktivitetsloggen rapporterar kontroll-plan händelser för dina prenumerationer under den administrativa kategorin. Du kan använda aktivitetsloggen för att fastställa den ”vad som, och när” för någon skrivåtgärd (PUT, POST, ta bort) för de resurser i din prenumeration. Du kan också få status för åtgärden och andra relevanta egenskaper. 

Aktivitetsloggen skiljer sig från diagnostikloggar. Aktivitetsloggen ger data om åtgärder på en resurs från utsidan (den _kontrollplan_). I Azure DB som Cosmos-kontexten skapa kontrollplan operations inkludera samling, lista nycklar, ta bort nycklar, lista databas och så vidare. Diagnostik orsakat av en resurs och ange information om handhavandet av den här resursen (den _dataplan_). Några exempel på plan dataåtgärder i diagnostiska loggen är Delete, Insert och ReadFeed.

Aktivitetsloggar (kontroll plan operations) kan vara bättre till sin natur och kan innehålla den fullständiga e-postadressen anroparen anroparen IP-adress, resurs, åtgärdsnamn, TenantId och mer. Aktivitetsloggen innehåller flera [kategorier](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) av data. Fullständig information om scheman av dessa kategorier finns [Azure-aktivitetsloggen Händelseschema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostikloggar kan dock vara begränsad till sin natur som personligt identifierbar information data ofta tas bort från de här loggarna. Du kanske IP-adressen för anroparen, men den senaste octant tas bort.

### <a name="azure-metrics"></a>Azure mått

[Azure mått](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics) viktigaste typ av Azure telemetridata (kallas även _prestandaräknare_) som genereras av mest Azure-resurser. Mått kan du visa information om dataflöde, lagring, konsekvens, tillgänglighet och svarstiden för dina Azure DB som Cosmos-resurser. Mer information finns i [övervakning och felsökning med mått i Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Azure diagnostikloggar orsakat av en resurs och ger omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen. Resursnivå diagnostikloggar också skilja sig från OS gästnivå diagnostikloggar. Gästoperativsystem diagnostiska loggar samlas in av en agent som körs i en virtuell dator eller andra stöd för resurstypen. Resursnivå diagnostikloggar kräver ingen agent och avbilda resurs-specifika data från själva Azure-plattformen. Gäst-OS-nivå diagnostikloggar samla in data från operativsystemet och program som körs på en virtuell dator.

![Diagnostikloggning till lagring, Händelsehubbar eller logganalys](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Vad är inloggad med Azure diagnostikloggar?

* Alla autentiserade backend-förfrågningar (TCP/REST) över alla API: er är inloggad, inklusive misslyckade begäranden på grund av behörigheter för åtkomst, systemfel eller felaktiga begäranden. Stöd för användarinitierad diagram, Cassandra och tabellen API-begäranden är inte tillgänglig för närvarande.
* Åtgärder på databasen, bland annat CRUD-åtgärder på alla dokument, behållare och databaser.
* Åtgärder på nycklar, vilket innefattar att skapa, ändra eller ta bort nycklarna.
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Till exempel begäranden som inte har ett ägartoken eller har fel format eller har upphört att gälla eller har en ogiltig token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Aktivera loggning i Azure-portalen

Om du vill aktivera diagnostikloggning, måste du ha följande resurser:

* En befintlig Azure Cosmos DB kontot, databas och behållare. Anvisningar om hur du skapar de här resurserna finns [skapa ett databaskonto med hjälp av Azure portal](create-sql-api-dotnet.md#create-a-database-account), [Azure CLI-exempel](cli-samples.md), eller [PowerShell-exempel](powershell-samples.md).

Om du vill aktivera diagnostiska loggar in på Azure-portalen, gör du följande steg:

1. I den [Azure-portalen](https://portal.azure.com), i din Azure Cosmos DB konto, Välj **diagnostikloggar** i det vänstra navigeringsfönstret och välj sedan **aktivera diagnostiken**.

    ![Aktivera diagnostikloggning för Azure Cosmos DB i Azure-portalen](./media/logging/turn-on-portal-logging.png)

2. I den **diagnostikinställningar** gör du följande: 

    * **Namnet**: Ange ett namn för loggfilerna för att skapa.

    * **Arkivet till ett lagringskonto**: Om du vill använda det här alternativet om du behöver ett befintligt lagringskonto för att ansluta till. Om du vill skapa ett nytt lagringskonto i portalen finns [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md) och följ instruktionerna för att skapa en Azure Resource Manager, allmänna konto. Återvänd sedan till den här sidan i portalen för att markera ditt lagringskonto. Det kan ta några minuter för nyskapade lagringskonton ska visas i den nedrullningsbara menyn.
    * **Dataströmmen till en händelsehubb**: Om du vill använda det här alternativet om du behöver en befintlig Händelsehubbar namnområde och händelsen hubb att ansluta till. Om du vill skapa ett namnområde för Händelsehubbar finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md). Återvänd sedan till den här sidan i portalen för att välja Händelsehubbar namnområde och principen.
    * **Skicka till logganalys**: Om du vill använda det här alternativet Använd en befintlig arbetsyta eller skapa en ny logganalys-arbetsyta genom att följa stegen för att [skapa en ny arbetsyta](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) i portalen. Mer information om hur du visar loggarna i logganalys finns [visa loggar i logganalys](#view-in-loganalytics).
    * **Logga DataPlaneRequests**: Välj det här alternativet för att logga backend-begäranden från den underliggande Azure Cosmos DB distribuerade plattformen för SQL, diagram, MongoDB, Cassandra och tabellen API-konton. Om du arkivera till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.
    * **Logga MongoRequests**: Välj det här alternativet för att logga förfrågningar som initierats av användaren från Azure Cosmos DB klientdelen betjänar MongoDB API-konton. Om du arkivera till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.
    * **Mått begäranden**: Välj det här alternativet för att lagra utförliga data i [Azure mått](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Om du arkivera till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.

3. Välj **Spara**.

    Om du får ett felmeddelande som säger ”det gick inte att uppdatera diagnostik för \<arbetsytans namn >. Prenumerationen \<prenumerations-id > är inte registrerad för att använda microsoft.insights ”, följer den [felsöka Azure-diagnostik](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instruktioner för att registrera kontot och försök sedan den här proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden tillbaka till den här sidan om du vill ändra diagnostiska logginställningar för ditt konto.

## <a name="turn-on-logging-by-using-azure-cli"></a>Aktivera loggning med hjälp av Azure CLI

Om du vill aktivera mätvärden och diagnostikloggning med hjälp av Azure CLI, använder du följande kommandon:

- Använd följande kommando för att aktivera lagring av diagnostiska loggar i ett lagringskonto:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Den `resourceId` är namnet på Azure DB som Cosmos-konto. Den `storageId` är namnet på lagringskontot som du vill skicka loggar.

- Om du vill aktivera strömning av diagnostiska loggar till en händelsehubb, Använd följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Den `resourceId` är namnet på Azure DB som Cosmos-konto. Den `serviceBusRuleId` är en sträng med formatet:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Använd följande kommando om du vill aktivera skicka diagnostiska loggar till en logganalys-arbetsyta:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

Om du vill aktivera diagnostikloggning med hjälp av PowerShell behöver du Azure Powershell med en lägsta version av 1.0.1.

Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Om du redan har installerat Azure PowerShell och inte vet vilken version från PowerShell-konsolen typen `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Login-AzureRmAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell hämtar alla prenumerationer som är associerade med det här kontot, och som standard, använder den första som.

Om du har mer än en prenumeration kan behöva du ange specifika prenumerationen som användes för att skapa din Azure key vault. Om du vill visa prenumerationer för ditt konto, skriver du följande kommando:

```powershell
Get-AzureRmSubscription
```

Om du vill ange den prenumeration som är associerad med Azure DB som Cosmos-konto som du loggar, skriver du följande kommando:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har mer än en prenumeration som är kopplad till ditt konto, är det viktigt att ange den prenumeration som du vill använda.
>
>

Mer information om hur du konfigurerar Azure PowerShell finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar
Men du kan använda ett befintligt lagringskonto för dina loggar i den här självstudiekursen, skapa vi ett nytt lagringskonto som är dedikerad till Azure Cosmos DB loggar. Av praktiska skäl kan vi lagra kontoinformation för lagring i en variabel med namnet **sa**.

För att ytterligare underlätta hantering i den här självstudiekursen kommer använder vi samma resursgrupp som det som innehåller Azure DB som Cosmos-databasen. Ersätt värdena för den **ContosoResourceGroup**, **contosocosmosdblogs**, och **norra centrala USA** parametrar, i tillämpliga fall:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Om du vill använda ett befintligt lagringskonto måste kontot använda samma prenumeration som Azure DB som Cosmos-prenumeration. Kontot måste också använda Resource Manager-distributionsmodellen, i stället för den klassiska distributionsmodellen.
>
>

### <a id="identify"></a>Identifiera Azure DB som Cosmos-konto för loggarna
Ange namnet på Azure DB som Cosmos-kontot till en variabel med namnet **konto**, där **ResourceName** är namnet på Azure DB som Cosmos-konto.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Aktivera loggning
Aktivera loggning för Azure Cosmos DB med den `Set-AzureRmDiagnosticSetting` med variabler för det nya lagringskontot, Azure DB som Cosmos-konto och kategorin som ska aktiveras för loggning. Kör följande kommando och ange den **-aktiverad** flaggan till **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Utdata för kommandot bör likna följande exempel:

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

Utdata från kommandot bekräftar att loggning har nu aktiverats för databasen och informationen sparas i ditt lagringskonto.

Du kan också ange bevarandeprincipen för loggar, så att äldre loggar tas bort automatiskt. Till exempel ange bevarandeprincip med den **- RetentionEnabled** -flaggan inställd på **$true**. Ange den **- RetentionInDays** parameter till **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Komma åt loggarna
Azure Cosmos-DB-loggarna för den **DataPlaneRequests** kategori lagras i den **insights-loggar-data-plan-begäranden** behållare i storage-konto som du angav. 

Börja med att skapa en variabel för behållarens namn. Variabeln används i hela genomgången.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Om du vill visa en lista över blobbar i den här behållaren, skriver du:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Utdata för kommandot bör likna följande exempel:

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

Som du kan se dessa utdata kan följa en namngivningskonvention blobar: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom samma lagringskonto kan användas för att samla in loggar för olika resurser, kan du använda fullständiga resurs-ID i blob-namnet för åtkomst och ladda ned de specifika blobbar som du behöver. Innan vi gör det kan upp vi hur du laddar ned alla blobar.

Börja med att skapa en mapp som du vill ladda ned blobbarna till. Exempel:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Sedan hämta en lista över alla blobar:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Skicka den här listan via den `Get-AzureStorageBlobContent` kommando för att hämta blobbarna i målmappen:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

När du kör den här andra kommandot, den **/** avgränsare i blobbnamnen skapar en fullständig mappstruktur under målmappen. Den här mappstrukturen används för att ladda ned och lagra blobar som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera databaser och vill hämta loggar för en databas med namnet **CONTOSOCOSMOSDB3**, använder du kommandot:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Om du har flera resursgrupper och om du vill hämta loggar för en resursgrupp kan använda kommandot `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Om du vill hämta alla loggar för månaden som juli 2017 använda kommandot `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Du kan också köra följande kommandon:

* Om du vill fråga status för diagnostikinställningar för din databas resurs, använder du kommandot `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Inaktivera loggning av den **DataPlaneRequests** kategori för din databas konto resurs, använder du kommandot `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Blobbar som returneras i var och en av de här frågorna lagras som text och formateras som en JSON-blob som visas i följande kod:

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

## <a name="manage-your-logs"></a>Hantera dina loggar

Diagnostikloggar görs tillgängliga i ditt konto i två timmar från den tidpunkt då Azure DB som Cosmos-åtgärden utfördes. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standard Azure access control metoder för att skydda dina loggar och begränsa som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Kvarhållningsperioden för begäranden av plan som arkiveras till ett lagringskonto har konfigurerats i portalen när den **loggen DataPlaneRequests** inställningen är markerad. Om du vill ändra den här inställningen finns [aktiverar du loggning i Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Visa loggfiler i logganalys

Om du har valt den **skicka till logganalys** när du har aktiverat diagnostikloggning, diagnostiska data från din samling vidarebefordras till logganalys inom två timmar. När du tittar på logganalys omedelbart efter att du aktiverar loggning finns inte några data. Bara vänta två timmar och försök igen. 

Innan du visa loggarna och se om logganalys-arbetsytan har uppgraderats för att använda nya Log Analytics-frågespråket. Om du vill kontrollera, öppna den [Azure-portalen](https://portal.azure.com)väljer **logganalys** längst till vänster, välj sedan namnet på arbetsytan som visas i nästa bild. Den **OMS-arbetsytan** visas:

![Logganalys i Azure-portalen](./media/logging/azure-portal.png)

Om du ser följande meddelande på den **OMS-arbetsytan** sidan ditt arbetsområde har inte uppgraderats om du vill använda det nya språket. Mer information om hur du uppgraderar till nya frågespråket finns [uppgradera Azure logganalys-arbetsytan till ny logg sökning](../log-analytics/log-analytics-log-search-upgrade.md). 

![Logganalys uppgradera meddelande](./media/logging/upgrade-notification.png)

Om du vill visa dina diagnostiska data i logganalys, öppna den **loggen Sök** sida från den vänstra menyn eller **Management** område på sidan som visas i följande bild:

![Sök Loggalternativ i Azure-portalen](./media/logging/log-analytics-open-log-search.png)

Nu när du har aktiverat datainsamling kan köra följande loggen Sök exempel med hjälp av nya frågespråket för att se de 10 senaste loggarna `AzureDiagnostics | take 10`.

![Exempel loggen Sök efter de 10 senaste loggarna](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Frågor

Här följer några ytterligare frågor som du kan ange i den **loggen Sök** rutan för att övervaka Azure DB som Cosmos-behållare. De här frågorna arbeta med den [nytt språk](../log-analytics/log-analytics-log-search-upgrade.md). 

Läs om innebörden av data som returneras av varje logg sökning i [tolka Azure Cosmos DB loggarna](#interpret).

* Fråga efter alla diagnostiska loggar från Azure Cosmos-databasen för en angiven tidsperiod:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Om du vill fråga efter 10 mest senast loggade händelser:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Fråga efter alla åtgärder, grupperade efter åtgärdstyp:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Fråga efter alla åtgärder, grupperade efter **resurs**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Fråga efter alla användaraktivitet, grupperade efter resurs:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Det här kommandot är för en aktivitetsloggen, inte en diagnostiska logg.

* Fråga som åtgärder tar längre tid än 3 millisekunder:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Fråga för vilken agent körs åtgärder:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Fråga efter när långvariga åtgärder utfördes:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Mer information om hur du använder det nya loggen Sök-språket finns [förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Tolka loggarna

Diagnostiska data, som lagras i Azure Storage- och Log Analytics använder ett liknande schema. 

I följande tabell beskrivs innehållet i varje loggpost.

| Azure Storage-fältet eller egenskapen | Log Analytics-egenskap | Beskrivning |
| --- | --- | --- |
| **time** | **TimeGenerated** | Datum och tid (UTC) när åtgärden utfördes. |
| **resourceId** | **Resurs** | Azure DB som Cosmos-konto som loggar är aktiverad.|
| **Kategori** | **Kategori** | För Azure Cosmos DB loggar **DataPlaneRequests** är bara tillgängliga värde. |
| **operationName** | **OperationName** | Namnet på åtgärden. Det här värdet kan vara något av följande åtgärder: skapa, uppdatera, Läs-, ReadFeed, ta bort, ersätta, Execute, SqlQuery, fråga, JSQuery, Head, HeadFeed eller Upsert.   |
| **Egenskaper** | Saknas | Innehållet i det här fältet beskrivs i de rader som följer. |
| **ActivityId** | **activityId_g** | Unikt GUID för den loggade åtgärden. |
| **userAgent** | **userAgent_s** | En sträng som anger klientanvändaragent som utför förfrågan. Formatet är {användarnamn för agenten} / {version}.|
| **resourceType** | **resourceType** | Typ av resurs som används. Det här värdet kan vara något av följande resurstyper: databas, samling, dokument, bifogad fil, användare, behörighet, StoredProcedure, utlösare, UserDefinedFunction eller erbjudandet. |
| **statusCode** | **statusCode_s** | Svarsstatus för åtgärden. |
| **requestResourceId** | **Resurs-ID** | ResourceId som gäller för begäran. Värdet kan peka databaseRid, collectionRid eller documentRid beroende på operation utförs.|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **requestCharge** | **requestCharge_s** | Antalet RUs som används av åtgärden |
| **collectionRid** | **collectionId_s** | Unikt ID för samlingen.|
| **Varaktighet** | **duration_s** | Varaktighet för åtgärden i tick. |
| **requestLength** | **requestLength_s** | Längden på begäran, i byte. |
| **responseLength** | **responseLength_s** | Längden i svaret, i byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är tomt när [resurs token](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet som pekar på resurs-ID för användaren. |

## <a name="next-steps"></a>Nästa steg

- För att förstå hur du aktiverar loggning och mått och logga kategorier som stöds av olika Azure-tjänster kan du läsa både den [översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [översikt av Azure diagnostikloggar ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artiklar.
- Läs artiklarna att lära dig om händelsehubbar:
   - [Vad är Händelsehubbar i Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Läs [hämta mätvärden och diagnostiska loggar från Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Läs [förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md).
