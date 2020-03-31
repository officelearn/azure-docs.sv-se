---
title: Diagnostikloggning för Azure Analysis Services | Microsoft-dokument
description: Beskriver hur du konfigurerar diagnostikloggning för Azure-resurser för att övervaka din Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266187"
---
# <a name="setup-diagnostic-logging"></a>Konfigurera diagnostisk loggning

En viktig del av en Analysis Services-lösning är att övervaka hur dina servrar fungerar. Med [Azure-resursloggar](../azure-monitor/platform/platform-logs-overview.md)kan du övervaka och skicka loggar till [Azure Storage,](https://azure.microsoft.com/services/storage/)strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och exportera dem till Azure [Monitor-loggar](../azure-monitor/azure-monitor-log-hub.md).

![Diagnostikloggning till lagrings-, händelsehubbar eller Azure Monitor-loggar](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Vad loggas?

Du kan välja kategorier **För motor,** **tjänst**och **mått.**

### <a name="engine"></a>Motor

Om du **väljer Motorloggar** alla [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Du kan inte välja enskilda händelser. 

|XEvent kategorier |Händelsenamn  |
|---------|---------|
|Säkerhetsgranskning    |   Granskning inloggning      |
|Säkerhetsgranskning    |   Granskning utloggning      |
|Säkerhetsgranskning    |   Granskningsservern startar och stoppar      |
|Lägesrapporter     |   Förloppsrapporten börjar      |
|Lägesrapporter     |   Förloppsrapporten avslutas      |
|Lägesrapporter     |   Lägesrapport aktuell      |
|Frågor     |  Frågan börjar       |
|Frågor     |   Frågan slutar      |
|Kommandon     |  Kommandot börjar       |
|Kommandon     |  Kommandot       |
|Fel & varningar     |   Fel      |
|Utforska     |   Upptäck      |
|Avisering     |    Avisering     |
|Session     |  Sessionsinitiering       |
|Lås    |  Dödläge       |
|Frågebearbetning     |   VertiPaq SE Fråga Börja      |
|Frågebearbetning     |   VertiPaq SE-fråga      |
|Frågebearbetning     |   VertiPaq SE-frågecachematchning      |
|Frågebearbetning     |   Direkt fråga börja      |
|Frågebearbetning     |  Direktfrågeslut       |

### <a name="service"></a>Tjänst

|Åtgärdsnamn  |Inträffar när  |
|---------|---------|
|ÅterupptaServer     |    Återuppta en server     |
|SuspendServer    |   Pausa en server      |
|DeleteServer (Ta bortservrar)     |    Ta bort en server     |
|Starta om Servern    |     Användaren startar om en server via SSMS eller PowerShell    |
|FåServerLogFiler    |    Serverloggen för användare exporterar via PowerShell     |
|ExporteraModel     |   Användaren exporterar en modell i portalen med öppna i Visual Studio     |

### <a name="all-metrics"></a>Alla mått

Kategorin Mått loggar samma [servermått](analysis-services-monitor.md#server-metrics) till tabellen AzureMetrics. Om du använder [frågeskalning](analysis-services-scale-out.md) och behöver separera mått för varje läsreplik använder du tabellen AzureDiagnostics i stället, där **OperationName** är lika med **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurera diagnostikloggning

### <a name="azure-portal"></a>Azure Portal

1. I [Azure portal](https://portal.azure.com) > server klickar du på **Diagnostikloggar** i den vänstra navigeringen och sedan på Aktivera **diagnostik**.

    ![Aktivera diagnostikloggning för Azure Cosmos DB i Azure-portalen](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. I **Diagnostikinställningar** anger du följande alternativ: 

    * **Namn**. Ange ett namn på de loggar som ska skapas.

    * **Arkivera till ett lagringskonto**. Om du vill använda det här alternativet behöver du ett befintligt lagringskonto att ansluta till. Se [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md). Följ instruktionerna för att skapa ett Resurshanteraren, allmänt konto, välj sedan ditt lagringskonto genom att gå tillbaka till den här sidan i portalen. Det kan ta några minuter innan nyligen skapade lagringskonton visas i den nedrullningsbara menyn.
    * **Strömma till ett händelsenav**. Om du vill använda det här alternativet behöver du ett befintligt namnområde och händelsehubb för händelsehubben att ansluta till. Mer information finns i [Skapa en namnrymd för en händelsehubb och en händelsehubb med Azure-portalen](../event-hubs/event-hubs-create.md). Gå sedan tillbaka till den här sidan i portalen för att välja namnområdet eventhub och principnamn.
    * **Skicka till Azure Monitor (Log Analytics-arbetsytan).** Om du vill använda det här alternativet använder du antingen en befintlig arbetsyta eller [skapar en ny arbetsytas](../azure-monitor/learn/quick-create-workspace.md) resurs i portalen. Mer information om hur du visar dina loggar finns [i Visa loggar i Log Analytics-arbetsytan](#view-logs-in-log-analytics-workspace) i den här artikeln.

    * **Motor**. Välj det här alternativet om du vill logga xEvents. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperioden för diagnostikloggarna. Loggar na läggs bort automatiskt när kvarhållningsperioden har gått ut.
    * **Service**. Välj det här alternativet om du vill logga servicenivåhändelser. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostikloggarna. Loggar na läggs bort automatiskt när kvarhållningsperioden har gått ut.
    * **Mått**. Välj det här alternativet om du vill lagra utförliga data i [Mått](analysis-services-monitor.md#server-metrics). Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostikloggarna. Loggar na läggs bort automatiskt när kvarhållningsperioden har gått ut.

3. Klicka på **Spara**.

    Om du får ett felmeddelande med texten \<"Det gick inte att uppdatera diagnostiken för arbetsytenamn>. Prenumerations-ID \<> registreras inte för att använda microsoft.insights." Följ [instruktionerna för felsökning av Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) för att registrera kontot och försök sedan igen med den här proceduren.

    Om du vill ändra hur diagnostikloggarna sparas när som helst i framtiden kan du gå tillbaka till den här sidan för att ändra inställningarna.

### <a name="powershell"></a>PowerShell

Här är de grundläggande kommandona för att komma igång. Om du vill ha steg-för-steg-hjälp om hur du konfigurerar loggning till ett lagringskonto med PowerShell läser du självstudien senare i den här artikeln.

Om du vill aktivera mått och diagnostikloggning med hjälp av PowerShell, använder du följande kommandon:

- Använd följande kommando om du vill aktivera lagring av diagnostikloggar på ett lagringskonto:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Ditt lagringskonto-ID är resurs-ID för det lagringskonto dit du vill skicka loggarna.

- Använd följande kommando om du vill aktivera direktuppspelning av diagnostikloggar till en händelsehubb:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera funktionen för att skicka diagnostikloggar till en Log Analytics-arbetsyta:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

### <a name="rest-api"></a>REST API

Lär dig hur du [ändrar diagnostikinställningar med hjälp av REST-API i Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Lär dig hur du [aktiverar diagnostikinställningar när resursen skapas med hjälp av en Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Hantera dina loggar

Loggar är vanligtvis tillgängliga inom ett par timmar efter att du har konfigurerat loggning. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Var noga med att ange en kvarhållningsperiod för så att gamla loggar tas bort från ditt lagringskonto.

## <a name="view-logs-in-log-analytics-workspace"></a>Visa loggar i Log Analytics-arbetsytan

Mått och serverhändelser är integrerade med xEvents i din Log Analytics-arbetsytas resurs för analys sida vid sida. Log Analytics-arbetsytan kan också konfigureras för att ta emot händelser från andra Azure-tjänster som ger en helhetssyn på diagnostikloggningsdata i din arkitektur.

Om du vill visa diagnostikdata öppnar **du Loggar** från den vänstra menyn i Log Analytics-arbetsytan.

![Alternativ för loggsökning i Azure-portalen](./media/analysis-services-logging/aas-logging-open-log-search.png)

Expandera **LogManagement** > **AzureDiagnostics**i frågeverktyget . AzureDiagnostics innehåller motor- och tjänsthändelser. Observera att en fråga skapas i farten. Fältet EventClass\_innehåller xEvent-namn, som kan se bekant ut om du har använt xEvents för lokal loggning. Klicka på **EventClass\_s** eller något av händelsenamnen och Logganalysarbetsytan fortsätter att skapa en fråga. Glöm inte att spara dina frågor så att du kan återanvända dem senare.

### <a name="example-queries"></a>Exempelfrågor

#### <a name="example-1"></a>Exempel 1

Följande fråga returnerar varaktigheter för varje slut-/uppdateringshändelse för en modelldatabas och server. Om skalas ut, resultaten delas ut efter replik eftersom repliknumret ingår i ServerName_s. Gruppering efter RootActivityId_g minskar antalet rader som hämtas från AZURE Diagnostics REST API och hjälper till att hålla sig inom de gränser som beskrivs i begränsningar för [logganalyshastighet](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Exempel 2

Följande fråga returnerar minne och QPU-förbrukning för en server. Om skalas ut, resultaten delas ut efter replik eftersom repliknumret ingår i ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Exempel 3

Följande fråga returnerar prestandadiskrarna för radläsning/sek Analysis Services-motorns prestanda för en server.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Det finns hundratals frågor som du kan använda. Mer information om frågor finns i [Komma igång med Azure Monitor-loggfrågor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med PowerShell

I den här snabbstudien skapar du ett lagringskonto i samma prenumerations- och resursgrupp som Analysis Service-servern. Du använder sedan Set-AzDiagnosticSetting för att aktivera diagnostikloggning och skicka utdata till det nya lagringskontot.

### <a name="prerequisites"></a>Krav
För att kunna slutföra den här självstudien måste du ha följande resurser:

* En befintlig Azure Analysis Services-server. Instruktioner om hur du skapar en serverresurs finns [i Skapa en server i Azure-portalen](analysis-services-create-server.md)eller Skapa en Azure Analysis [Services-server med powershell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Ansluta till dina prenumerationer

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer kan du behöva ange en som användes för att skapa Azure Key Vault. Skriv följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du sedan vill ange vilken prenumeration som är kopplad till det Azure Analysis Services-konto som du loggar skriver du:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer kopplade till ditt konto är det viktigt att du anger prenumerationen.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagringskonto för dina loggar, förutsatt att det finns i samma prenumeration som servern. För den här självstudien skapar du ett nytt lagringskonto som är dedikerat till Analysis Services-loggar. För att göra det enkelt lagrar du information om lagringskontot i en variabel med namnet **sa**.

Du kan också använda samma resursgrupp som den som innehåller Analysis Services-servern. Ersätt värden `awsales_resgroup` `awsaleslogs`för `West Central US` , och med dina egna värderingar:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifiera serverkontot för dina loggar

Ange kontonamnet till ett variabelnamn med namnet **konto**, där ResourceName är namnet på kontot.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivera loggning

Om du vill aktivera loggning använder du cmdleten Set-AzDiagnosticSetting tillsammans med variablerna för det nya lagringskontot, serverkontot och kategorin. Kör följande kommando genom att ställa in **flaggan -Aktiverad** flagga på **$true:**

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Utdata bör se ut ungefär som i följande exempel:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Den här utdata bekräftar att loggning nu är aktiverat för servern, vilket sparar information till lagringskontot.

Du kan också ange bevarandeprincip för dina loggar så att äldre loggar tas bort automatiskt. Ange till exempel bevarandeprincip med **flaggan BevarandeEnbart** för att **$true**och ange **parametern -RetentionInDays** till **90**. Loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [diagnostikloggning av Azure-resurser](../azure-monitor/platform/platform-logs-overview.md).

Se [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) i PowerShell hjälp.
