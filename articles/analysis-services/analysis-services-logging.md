---
title: Diagnostisk loggning för Azure Analysis Services | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Diagnostisk loggning för Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 480d453cc906fa1b1d93e00bd4a6d2b080768a47
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105854"
---
# <a name="setup-diagnostic-logging"></a>Konfigurera Diagnostisk loggning

En viktig del av alla Analysis Services-lösningar övervakar hur dina servrar utför. Med [diagnostikloggar för Azure-resurs](../azure-monitor/platform/diagnostic-logs-overview.md), du kan övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/), en tjänst av [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Diagnostisk loggning till lagring, Händelsehubbar och Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


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
    * **Skicka till Log Analytics**. Om du vill använda det här alternativet måste använda en befintlig arbetsyta eller skapa en ny Log Analytics-arbetsyta genom att följa stegen för att [skapa en ny arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) i portalen. Mer information om hur du visar dina loggar i Log Analytics finns i [visa loggar i Log Analytics](#view-logs-in-log-analytics) i den här artikeln.

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
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggarna.

- Om du vill aktivera strömning av diagnostikloggar till en händelsehubb, Använd följande kommando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera skicka diagnostikloggar till en Log Analytics-arbetsyta:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
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

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Mått- och serverhändelser är integrerade med xEvents i Log Analytics för analys i sida-vid-sida. Log Analytics kan också konfigureras för att ta emot händelser från andra Azure-tjänster att tillhandahålla en praktisk vy över diagnostikdata till loggning i din arkitektur.

Om du vill visa dina diagnostiska data i Log Analytics, öppna sidan Log Search från den vänstra menyn eller hanteringsområdet, enligt nedan.

![Loggalternativ för sökning i Azure portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Nu när du har aktiverat insamling av data i **Loggsökning**, klickar du på **alla insamlade data**.

I **typ**, klickar du på **AzureDiagnostics**, och klicka sedan på **tillämpa**. AzureDiagnostics innehåller motor och tjänsthändelser. Lägg märke till en Log Analytics-fråga skapas i farten. EventClass\_s fältet innehåller xEvent-namn, som kan se bekant ut om du har använt xEvents för lokal loggning.

Klicka på **EventClass\_s** eller en av händelsenamn och Log Analytics fortsätter att konstruera en fråga. Glöm inte att spara dina frågor att återanvända senare.

Hittar du i Log Analytics, som tillhandahåller en webbplats med förbättrade funktioner för frågor, dashboarding, och varningar på insamlade data.

### <a name="queries"></a>Frågor

Det finns hundratals frågor som du kan använda. Här följer några att komma igång.
Mer information om hur du använder det nya frågespråket Log Search finns [Understanding log söker i Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Frågan returnerar frågor skickas till Azure Analysis Services tog över fem minuter (300 000 millisekunder) för att slutföra.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifiera skala ut repliker.

    ```
    search * | summarize count() by ServerName_s
    ```
    När du använder skalbar, kan du identifiera skrivskyddade repliker eftersom ServerName\_s fältvärdena har repliken instansnummer läggas till i namnet. Resursen innehåller Azure resursnamnet som matchar servernamnet som användarna kan se. Fältet IsQueryScaleoutReadonlyInstance_s är lika med sant för repliker.



> [!TIP]
> Har du en bra Log Analytics-fråga som du vill dela? Om du har ett GitHub-konto, kan du lägga till det i den här artikeln. Klicka bara på **redigera** längst upp till höger på den här sidan.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Självstudiekurs – aktivera loggning med hjälp av PowerShell
I den här snabba självstudien skapar du ett lagringskonto i samma prenumeration och resursgrupp som Analysis Services-servern. Du kan sedan använda Set-AzureRmDiagnosticSetting för att slå på diagnostik loggning, skicka utdata till det nya lagringskontot.

### <a name="prerequisites"></a>Förutsättningar
Den här kursen måste du ha följande resurser:

* En befintlig Azure Analysis Services-server. Anvisningar om hur du skapar en serverresurs finns i [skapar en server i Azure-portalen](analysis-services-create-server.md), eller [skapa en Azure Analysis Services-server med hjälp av PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Ansluta till dina prenumerationer

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzureRmAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer kan du behöva ange en som användes för att skapa Azure Key Vault. Skriv följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzureRmSubscription
```

Om du vill ange den prenumeration som är associerat med Azure Analysis Services-konto som du loggar, skriver du sedan:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är associerat med ditt konto är det viktigt att ange prenumerationen.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagringskonto för dina loggar, förutsatt att det är i samma prenumeration som din server. Den här självstudien skapa du ett nytt lagringskonto dedikerad till Analysis Services-loggar. Om du vill göra det enklare, du lagrar uppgifterna för lagringskontot i variabeln **sa**.

Du kan också använda samma resursgrupp som det som innehåller Analysis Services-servern. Ersätt värden för `awsales_resgroup`, `awsaleslogs`, och `West Central US` med dina egna värden:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifiera på server-tjänstkontot för dina loggar

Ange namnet på kontot till en variabel med namnet **konto**, där ResourceName är namnet på kontot.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivera loggning

Använd cmdlet Set-AzureRmDiagnosticSetting tillsammans med för att aktivera loggning för det nya lagringskontot, serverkonto och kategorin. Kör följande kommando och ange den **-aktiverad** flaggan till **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Utdata bör se ut ungefär så här:

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

Detta bekräftar att loggning är aktiverat för servern, information sparas i lagringskontot.

Du kan också ange bevarandeprincip för dina loggar så att äldre loggar tas bort automatiskt. Till exempel bevarandeprincip genom **- RetentionEnabled** flaggan till **$true**, och Ställ in **- RetentionInDays** parameter **90**. Loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Diagnostisk loggning för Azure-resurs](../azure-monitor/platform/diagnostic-logs-overview.md).

Se [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) i PowerShell-hjälpen.
