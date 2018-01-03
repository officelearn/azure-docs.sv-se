---
title: "Diganostic loggning för Azure Analysis Services | Microsoft Docs"
description: "Lär dig mer om hur du konfigurerar diagnostikloggning för Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/29/2017
ms.author: owend
ms.openlocfilehash: 982626b3bafbb3857d2d85e9442982e8f46f0501
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="setup-diagnostic-logging"></a>Konfigurera diagnostikloggning

En viktig del av en Analysis Services-lösning övervakar hur servrarna utför. Med [Azure-resurs diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), kan du övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [logg Analytics](https://azure.microsoft.com/services/log-analytics/), en del av [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Diagnostikloggning till lagring, Händelsehubbar eller Operations Management Suite via logganalys](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Vad är inloggad?

Du kan välja **motorn**, **Service**, och **mått** kategorier.

### <a name="engine"></a>Motorn

Att välja motorn loggar alla [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Du kan inte välja enskilda händelser. 

|XEvent-kategorier |Händelsenamn  |
|---------|---------|
|Säkerhetsgranskning    |   Granska inloggning      |
|Säkerhetsgranskning    |   Granska logga ut      |
|Säkerhetsgranskning    |   Gransknings-och servern startar och stoppar      |
|Rapporter     |   Förlopp rapporten Begin      |
|Rapporter     |   Förlopp rapporten slut      |
|Rapporter     |   Förlopp rapporten aktuella      |
|Frågor     |  Frågan Begin       |
|Frågor     |   Frågan slut      |
|Kommandon     |  Kommandot Begin       |
|Kommandon     |  Kommando End       |
|Fel och varningar     |   Fel      |
|Utforska     |   Identifiera slut      |
|Avisering     |    Avisering     |
|Session     |  Sessionen initiera       |
|Lås    |  deadlock       |
|Frågebearbetning     |   VertiPaq Frågecache Begin      |
|Frågebearbetning     |   VertiPaq Frågecache slut      |
|Frågebearbetning     |   VertiPaq Frågecache Cache-matchning      |
|Frågebearbetning     |   Direktfrågan Begin      |
|Frågebearbetning     |  Direktfrågan slut       |

### <a name="service"></a>Tjänst

|Åtgärdsnamn  |Inträffar när  |
|---------|---------|
|CreateGateway     |   Användaren kan konfigurera en gateway på servern      |
|ResumeServer     |    Återuppta en server     |
|SuspendServer    |   Pausa en server      |
|DeleteServer     |    Ta bort en server     |
|RestartServer    |     Användaren startar om en server via SSMS eller PowerShell    |
|GetServerLogFiles    |    Användaren exporterar serverloggen via PowerShell     |
|ExportModel     |   Användaren exporterar en modell i portalen med hjälp av öppna i Visual Studio     |

### <a name="all-metrics"></a>Alla mått

Kategorin AllMetrics loggar samma [serverstatistik](analysis-services-monitor.md#server-metrics) visas i mått.

## <a name="setup-diagnostics-logging"></a>Konfigurera diagnostikloggning

### <a name="by-using-the-azure-portal"></a>Med hjälp av Azure portal

1. I [Azure-portalen](https://portal.azure.com), i din Azure Analysis Services-server klickar du på **diagnostikloggar** i det vänstra navigeringsfönstret och klicka sedan på **aktivera diagnostiken**.

    ![Aktivera diagnostikloggning för Azure Cosmos DB i Azure-portalen](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. I den **diagnostikinställningar** gör följande: 

    * **Namn**. Ange ett namn för loggfilerna för att skapa.

    * **Arkivet till ett lagringskonto**. Om du vill använda det här alternativet behöver du ett befintligt lagringskonto för att ansluta till. Se [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md). Följ instruktionerna för att skapa en Resource Manager allmänna konto. Återgå sedan till den här sidan i portalen för att markera ditt lagringskonto. Det kan ta några minuter för nyskapade lagringskonton ska visas i den nedrullningsbara menyn.
    * **Dataströmmen till en händelsehubb**. Om du vill använda det här alternativet behöver du en befintlig Event Hub-namnområde och händelsen hubb att ansluta till. Om du vill skapa ett namnområde för Händelsehubbar finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md). Återgå sedan till den här sidan i portalen för att välja Event Hub namnområde och principen.
    * **Skicka till logganalys**. Om du vill använda det här alternativet Använd en befintlig arbetsyta eller skapa en ny logganalys-arbetsyta genom att följa stegen för att [skapa en ny arbetsyta](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) i portalen. Mer information om hur du visar loggarna i logganalys finns [visa loggar i logganalys](#view-in-loganalytics).

    * **Motorn**. Välj det här alternativet för att logga xEvents. Om du arkivera till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Tjänsten**. Välj det här alternativet för att logga händelser för tjänsten. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.
    * **Mått**. Välj det här alternativet för att lagra utförliga data i [mått](analysis-services-monitor.md#server-metrics). Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för diagnostiska loggar. Loggarna är autodeleted när kvarhållningsperioden upphör att gälla.

3. Klicka på **Spara**.

    Om du får ett felmeddelande som säger ”det gick inte att uppdatera diagnostik för \<arbetsytans namn >. Prenumerationen \<prenumerations-id > är inte registrerad för att använda microsoft.insights ”. Följ den [felsöka Azure-diagnostik](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instruktioner för att registrera kontot, och försök sedan den här proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden kan du återgå till den här sidan om du vill ändra inställningarna.

### <a name="by-using-powershell"></a>Med hjälp av PowerShell
Här är de grundläggande kommandona som du kan gå. Om du vill hjälp om hur du konfigurerar loggning till ett lagringskonto med hjälp av PowerShell, finns det [kursen](#tutorial) senare i den här artikeln.

Om du vill aktivera mätvärden och diagnostikfunktionerna loggning med hjälp av PowerShell använder du följande kommandon:

- Använd följande kommando för att aktivera lagring av diagnostik loggar i ett lagringskonto:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggar.

- Om du vill aktivera strömning av diagnostik loggar till en händelsehubb, Använd följande kommando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera skickas diagnostik loggar till en logganalys-arbetsyta:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för logganalys-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="rest-api"></a>REST-API

Lär dig hur du [ändra diagnostikinställningarna med hjälp av REST API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Lär dig hur du [aktivera diagnostikinställningar när resursen skapas med hjälp av en Resource Manager-mall](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Hantera dina loggar

Loggarna är normalt tillgängliga inom några timmar för att konfigurera loggning. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Glöm inte att ange en kvarhållningsperiod för så gamla loggarna tas bort från ditt lagringskonto.


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Visa loggfiler i logganalys

Mått och serverhändelser är integrerade med xEvents i Log Analytics för sida vid sida analys. Log Analytics kan också konfigureras för att ta emot händelser från andra Azure-tjänster att ge en helhetsvy över diagnostiska loggningsdata över arkitekturen.

Om du vill visa dina diagnostiska data i logganalys öppna loggen söksidan från den vänstra menyn eller området på sidan som visas i följande bild:

![Logga Sökalternativ i Azure-portalen](./media/analysis-services-logging/aas-logging-open-log-search.png)

Nu när du har aktiverat insamling av data i **loggen Sök**, klickar du på **alla insamlade data**.

I **typen**, klickar du på **AzureDiagnostics**, och klicka sedan på **tillämpa**. AzureDiagnostics innefattar motor- och händelser. Lägg märke till en Log Analytics-fråga skapas på direkt. EventClass\_s fältet innehåller xEvent-namn, som kan se bekant ut om du har använt xEvents för lokal loggning.

Klicka på **EventClass\_s** eller en händelsenamn och logganalys fortsätter att konstruera en fråga. Glöm inte att spara dina frågor att återanvända senare.

Se till att checka ut Operations Management Suite som ger en webbplats med aviseringar funktioner på logganalys data, dashboarding och förbättrad frågan.

<a id="#queries"></a>
### <a name="queries"></a>Frågor

Det finns hundratals frågor som du kan använda. Här följer några att komma igång.
Mer information om hur du använder nya loggen Sök frågespråket finns [förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md). 

* Frågan returnerar frågor som skickats till Azure Analysis Services som tog över fem minuter (300 000 millisekunder) för att slutföra.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifiera skala ut repliker.

    ```
    search * | summarize count() by ServerName_s
    ```
    När du använder skalbar, kan du identifiera skrivskyddade repliker eftersom ServerName\_s fältvärden har replik-instansnummer som läggs till i namnet. Resursen innehåller Azure-resurs-namnet, som matchar namnet på servern som användarna kan se. Fältet IsQueryScaleoutReadonlyInstance_s är lika med sant för repliker.



> [!TIP]
> Har en bra Log Analytics-fråga som du vill dela? Om du har en GitHub-konto kan du lägga till den i den här artikeln. Klicka bara på **redigera** på upp till höger på sidan.


<a id="#tutorial"></a>
## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Självstudiekurs – aktivera loggning med hjälp av PowerShell
I självstudierna snabbt skapa ett lagringskonto i samma prenumeration och resursgrupp som Analysis Services-servern. Du kan sedan använda Set-AzureRmDiagnosticSetting för att aktivera diagnostiken loggar, skicka utdata till det nya lagringskontot.

### <a name="prerequisites"></a>Förutsättningar
Den här kursen behöver du följande resurser:

* En befintlig Azure Analysis Services-server. Anvisningar om hur du skapar en server-resurs finns [skapa en server i Azure-portalen](analysis-services-create-server.md), eller [skapa en Azure Analysis Services-server med hjälp av PowerShell](analysis-services-create-powershell.md).


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

Om du vill ange den prenumeration som är associerad med Azure Analysis Services-konto som du loggar in, skriver du sedan:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är kopplade till ditt konto, är det viktigt att ange prenumerationen.
>
>

### <a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagringskonto för dina loggar under förutsättning att den är i samma prenumeration som din server. Den här självstudiekursen skapa du ett nytt lagringskonto dedikerade till Analysis Services-loggar. Om du vill göra det enklare, du lagrar information om lagringskonto i en variabel med namnet **sa**.

Du kan också använda samma resursgrupp som det innehåller Analysis Services-servern. Ersätt värdena för `awsales_resgroup`, `awsaleslogs`, och `West Central US` med egna värden:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a id="identify"></a>Identifiera server-konto för loggarna

Ange namnet på kontot till en variabel med namnet **konto**, där ResourceName är namnet på kontot.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a id="enable"></a>Aktivera loggning

Använd cmdlet Set-AzureRmDiagnosticSetting tillsammans med variabler för att aktivera loggning för det nya lagringskontot, server-konto och kategorin. Kör följande kommando, ange den **-aktiverad** flaggan till **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Resultatet bör se ut ungefär så här:

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

Det här bekräftar att loggning har nu aktiverats för servern, spara information till lagringskontot.

Du kan också ange bevarandeprincip för loggarna så att äldre loggar tas bort automatiskt. Till exempel kvarhållning princip genom att använda **- RetentionEnabled** flaggan till **$true**, och ange **- RetentionInDays** parameter till **90**. Loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-resurs diagnostikloggning](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Se [Set AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) i PowerShell-hjälpen.