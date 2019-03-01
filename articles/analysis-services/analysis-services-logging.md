---
title: Diagnostisk loggning för Azure Analysis Services | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Diagnostisk loggning för Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2303d385d3d688050a8d82c07e78a68588f41e88
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010930"
---
# <a name="setup-diagnostic-logging"></a>Konfigurera Diagnostisk loggning

En viktig del av alla Analysis Services-lösningar övervakar hur dina servrar utför. Med [diagnostikloggar för Azure-resurs](../azure-monitor/platform/diagnostic-logs-overview.md), du kan övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [Azure Övervaka loggar](../azure-monitor/azure-monitor-log-hub.md).

![Diagnostisk loggning till lagring, Event Hubs eller Azure Monitor-loggar](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Vad loggas?

Du kan välja **motorn**, **Service**, och **mått** kategorier.

### <a name="engine"></a>Motorn

Att välja **motorn** loggar alla [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Du kan inte välja enskilda händelser. 

|XEvent-kategorier |Händelsenamn  |
|---------|---------|
|Säkerhetsgranskning    |   Granska inloggning      |
|Säkerhetsgranskning    |   Granska utloggning      |
|Säkerhetsgranskning    |   Granska Server startar och stoppar      |
|Rapporter     |   Förlopp rapporten börjar      |
|Rapporter     |   Utvecklingsrapporten slutar      |
|Rapporter     |   Förlopp rapporten aktuella      |
|Frågor     |  Frågan börjar       |
|Frågor     |   Frågan slutar      |
|Kommandon     |  Kommandot börjar       |
|Kommandon     |  Kommandot slutar       |
|Fel och varningar     |   Fel      |
|Utforska     |   Identifiera slutet      |
|Avisering     |    Avisering     |
|Session     |  Sessionsinitiering       |
|Lås    |  Deadlock       |
|Frågebearbetning     |   VertiPaq SE-fråga börjar      |
|Frågebearbetning     |   VertiPaq SE-fråga slutet      |
|Frågebearbetning     |   VertiPaq SE-fråga Cache matchning      |
|Frågebearbetning     |   Direct Query börjar      |
|Frågebearbetning     |  Direct Query-slutpunkt       |

### <a name="service"></a>Tjänst

|Åtgärdens namn  |Inträffar när  |
|---------|---------|
|ResumeServer     |    Återuppta en server     |
|SuspendServer    |   Pausa en server      |
|DeleteServer     |    Ta bort en server     |
|RestartServer    |     Användaren startar om en server via SSMS eller PowerShell    |
|GetServerLogFiles    |    Användaren exporterar serverloggen via PowerShell     |
|ExportModel     |   Användaren exporterar en modell i portalen med hjälp av öppna i Visual Studio     |

### <a name="all-metrics"></a>Alla mått

Mått-kategorin loggar samma [serverstatistik](analysis-services-monitor.md#server-metrics) visas i mått.

## <a name="setup-diagnostics-logging"></a>Konfigurera diagnostikloggning

### <a name="azure-portal"></a>Azure Portal

1. I [Azure-portalen](https://portal.azure.com) > server, klickar du på **diagnostikloggar** i vänstra navigeringsfönstret och klicka sedan på **slå på diagnostik**.

    ![Aktivera Diagnostisk loggning för Azure Cosmos DB i Azure portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. I **diagnostikinställningar**, anger du följande alternativ: 

    * **Namn**. Ange ett namn att skapa loggarna.

    * **Arkivera till ett lagringskonto**. Om du vill använda det här alternativet om behöver du ett befintligt lagringskonto för att ansluta till. Se [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md). Följ anvisningarna för att skapa en Resource Manager Allmänt konto och välj sedan ditt lagringskonto genom att gå tillbaka till den här sidan i portalen. Det kan ta några minuter innan nyligen skapade lagringskonton ska visas i den nedrullningsbara menyn.
    * **Stream till en händelsehubb**. Om du vill använda det här alternativet behöver du en befintlig Event Hub-namnområde och event hub att ansluta till. Mer information finns i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](../event-hubs/event-hubs-create.md). Gå sedan tillbaka till den här sidan i portalen för att välja Event Hub-namnområde och principen.
    * **Skicka till Azure Monitor (Log Analytics-arbetsytan)**. Om du vill använda det här alternativet måste du använda en befintlig arbetsyta eller [skapa en ny arbetsyta](../azure-monitor/learn/quick-create-workspace.md) resurs i portalen. Mer information om hur du visar dina loggar finns i [visa loggar i Log Analytics-arbetsyta](#view-logs-in-log-analytics-workspace) i den här artikeln.

    * **Motorn**. Välj det här alternativet för att logga xEvents. Om du arkivering till ett lagringskonto, kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Tjänsten**. Välj det här alternativet att logga in på tjänsten-händelser. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Mått**. Välj det här alternativet för att lagra utförliga data i [mått](analysis-services-monitor.md#server-metrics). Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.

3. Klicka på **Spara**.

    Om du får ett meddelande om att ”det gick inte att uppdatera diagnostik för \<Arbetsytenamn >. Prenumerationen \<prenumerations-id > har inte registrerats för användning av microsoft.insights ”. Följ den [felsöka Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instruktionerna för att registrera kontot, och försök sedan utföra den här proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden kan du gå tillbaka till den här sidan om du vill ändra inställningarna.

### <a name="powershell"></a>PowerShell

Här är de grundläggande kommandona för att komma igång. Om du vill ha stegvisa hjälp om hur du konfigurerar loggning till ett lagringskonto med hjälp av PowerShell finns i självstudierna senare i den här artikeln.

Om du vill aktivera mått och diagnostik loggning med hjälp av PowerShell, använder du följande kommandon:

- Använd följande kommando om du vill aktivera lagring av diagnostikloggar i ett lagringskonto:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggarna.

- Om du vill aktivera strömning av diagnostikloggar till en händelsehubb, Använd följande kommando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera skicka diagnostikloggar till en Log Analytics-arbetsyta:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera Utdataalternativ för.

### <a name="rest-api"></a>REST-API

Lär dig hur du [ändra inställningarna för startdiagnostik med hjälp av REST-API i Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Lär dig hur du [aktivera diagnostikinställningar när resursen skapas med hjälp av Resource Manager-mall](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Hantera dina loggar

Loggarna är vanligtvis tillgängligt inom ett par timmar för att ställa in loggning. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Glöm inte att ställa in en kvarhållningsperiod för så gamla loggarna tas bort från ditt lagringskonto.

## <a name="view-logs-in-log-analytics-workspace"></a>Visa loggar i Log Analytics-arbetsyta

Mått- och serverhändelser är integrerade med xEvents i din Log Analytics-arbetsytan för sida-vid-sida-analys. Log Analytics-arbetsytan kan också konfigureras för att ta emot händelser från andra Azure-tjänster att tillhandahålla en praktisk vy över diagnostikdata till loggning i din arkitektur.

Om du vill visa dina diagnostiska data i Log Analytics-arbetsytan, öppna **loggar** menyn till vänster.

![Loggalternativ för sökning i Azure portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Expandera i Frågeverktyget **LogManagement** > **AzureDiagnostics**. AzureDiagnostics innehåller motor och tjänsthändelser. Observera att en fråga har skapats på direkt. EventClass\_s fältet innehåller xEvent-namn, som kan se bekant ut om du har använt xEvents för lokal loggning. Klicka på **EventClass\_s** eller en av de händelsenamn och Log Analytics-arbetsytan fortsätter att konstruera en fråga. Glöm inte att spara dina frågor att återanvända senare.

### <a name="example-query"></a>Exempelfråga
Den här frågan beräknar och returnerar CPU för varje fråga slutet/uppdatera Sluthändelse för en modelldatabasen och server:

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and ServerName_s =~"MyServerName" and DatabaseName_s == "Adventure Works Localhost" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| extend Engine_CPUTime=extract(@"([^,]*)", 1,CPUTime_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g ,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs,Engine_CPUTime
| join kind=leftouter (
window
    | where OperationName == "ProgressReportEnd" or (OperationName == "VertiPaqSEQueryEnd" and EventSubclass_s  != 10) or OperationName == "DiscoverEnd" or (OperationName has "CommandEnd" and EventSubclass_s != 38)
    | summarize sum_Engine_CPUTime = sum(extract(@"([^,]*)", 1,CPUTime_s, typeof(long))) by RootActivityId_g
    ) on RootActivityId_g
| extend totalCPU = sum_Engine_CPUTime + Engine_CPUTime

```


Det finns hundratals frågor som du kan använda. Mer information om frågor finns [Kom igång med Azure Monitor log-frågor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

I den här snabba självstudien skapar du ett lagringskonto i samma prenumeration och resursgrupp som Analysis Services-servern. Du kan sedan använda Set-AzDiagnosticSetting för att slå på diagnostik loggning, skicka utdata till det nya lagringskontot.

### <a name="prerequisites"></a>Förutsättningar
Den här kursen måste du ha följande resurser:

* En befintlig Azure Analysis Services-server. Anvisningar om hur du skapar en serverresurs finns i [skapar en server i Azure-portalen](analysis-services-create-server.md), eller [skapa en Azure Analysis Services-server med hjälp av PowerShell](analysis-services-create-powershell.md).

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

Om du vill ange den prenumeration som är associerat med Azure Analysis Services-konto som du loggar, skriver du sedan:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är associerat med ditt konto är det viktigt att ange prenumerationen.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagringskonto för dina loggar, förutsatt att det är i samma prenumeration som din server. För den här självstudien får skapa du ett nytt lagringskonto dedikerad till Analysis Services-loggar. Om du vill göra det enklare, du lagrar uppgifterna för lagringskontot i variabeln **sa**.

Du kan också använda samma resursgrupp som det som innehåller Analysis Services-servern. Ersätt värden för `awsales_resgroup`, `awsaleslogs`, och `West Central US` med dina egna värden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifiera på server-tjänstkontot för dina loggar

Ange namnet på kontot till en variabel med namnet **konto**, där ResourceName är namnet på kontot.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivera loggning

Använd cmdlet Set-AzDiagnosticSetting tillsammans med för att aktivera loggning för det nya lagringskontot, serverkonto och kategorin. Kör följande kommando och ange den **-aktiverad** flaggan till **$true**:

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

Det här resultatet bekräftar att loggning är aktiverat för servern, information sparas i lagringskontot.

Du kan också ange bevarandeprincip för dina loggar så att äldre loggar tas bort automatiskt. Till exempel bevarandeprincip genom **- RetentionEnabled** flaggan till **$true**, och Ställ in **- RetentionInDays** parameter **90**. Loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Diagnostisk loggning för Azure-resurs](../azure-monitor/platform/diagnostic-logs-overview.md).

Se [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) i PowerShell-hjälpen.
