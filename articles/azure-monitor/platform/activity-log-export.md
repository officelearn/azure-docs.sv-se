---
title: Exportera Azure-aktivitetsloggen
description: Exportera Azure Activity-loggen till lagring för arkivering eller Azure Event Hubs för export utanför Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396720"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportera Azure Activity-logg till lagrings- eller Azure-händelsehubbar

> [!IMPORTANT]
> Metoden för att skicka Azure Activity-loggen till Azure Storage och Azure Event Hubs har ändrats till [diagnostikinställningar](diagnostic-settings.md). I den här artikeln beskrivs den äldre metoden som håller på att vara inaktuell. Se Uppdatera för att [samla in och analysera Azure Activity-loggen i Azure Monitor](activity-log-collect.md) för en jämförelse.


[Azure-aktivitetsloggen](platform-logs-overview.md) ger insikt i händelser på prenumerationsnivå som har inträffat i din Azure-prenumeration. Förutom att visa aktivitetsloggen i Azure-portalen eller kopiera den till en Log Analytics-arbetsyta där den kan analyseras med andra data som samlas in av Azure Monitor, kan du skapa en loggprofil för att arkivera aktivitetsloggen till ett Azure-lagringskonto eller strömma den till en eventhub.

## <a name="archive-activity-log"></a>Arkiv aktivitetslogg
Arkivering av aktivitetsloggen till ett lagringskonto är användbart om du vill behålla loggdata längre än 90 dagar (med full kontroll över bevarandeprincipen) för granskning, statisk analys eller säkerhetskopiering. Om du bara behöver behålla dina händelser i 90 dagar eller mindre behöver du inte konfigurera arkivering till ett lagringskonto, eftersom aktivitetslogghändelser lagras på Azure-plattformen i 90 dagar.

## <a name="stream-activity-log-to-event-hub"></a>Strömma aktivitetslogg till händelsehubben
[Azure Event Hubs](/azure/event-hubs/) är en dataströmningsplattform och händelseinmatningstjänst som kan ta emot och bearbeta miljontals händelser per sekund. Data som skickas till en händelsehubb kan omvandlas och lagras med hjälp av valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Två sätt att använda streamingfunktionen för aktivitetsloggen är:
* **Streama till system för loggning och telemetri från tredje part:** Med tiden blir Azure Event Hubs streaming mekanismen för att leda din aktivitetslogg till SIEMs från tredje part och logganalyslösningar.
* **Skapa en anpassad telemetri- och loggningsplattform:** Om du redan har en specialbyggd telemetriplattform eller funderar på att bygga en, gör den mycket skalbara publicerings-prenumerationskaraktären hos Event Hubs dig att flexibelt inta aktivitetsloggen.

## <a name="prerequisites"></a>Krav

### <a name="storage-account"></a>Lagringskonto
Om du arkiverar aktivitetsloggen måste du [skapa ett lagringskonto](../../storage/common/storage-account-create.md) om du inte redan har ett. Du bör inte använda ett befintligt lagringskonto som har andra data som inte övervakar data lagrade i det så att du bättre kan kontrollera åtkomsten till övervakningsdata. Om du också arkiverar loggar och mått till ett lagringskonto kan du dock välja att använda samma lagringskonto för att behålla alla övervakningsdata på en central plats.

Lagringskontot behöver inte vara i samma prenumeration som prenumerationen som avger loggar så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna. 

> [!TIP]
> Se [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) för att ge åtkomst till ett lagringskonto bakom ett säkert virtuellt nätverk.

### <a name="event-hubs"></a>Event Hubs
Om du skickar aktivitetsloggen till en händelsehubb måste du [skapa en händelsehubb](../../event-hubs/event-hubs-create.md) om du inte redan har en. Om du tidigare strömmade aktivitetslogghändelser till det här namnområdet för eventhubbar kommer händelsehubben att återanvändas.

Principen för delad åtkomst definierar de behörigheter som direktuppspelningsmekanismen har. För att strömma till händelsehubbar krävs behörigheter för hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst för namnområdet Event Hubs i Azure-portalen under fliken Konfigurera för namnområdet Event Hubs.

Om du vill uppdatera aktivitetsloggloggen så att den omfattar direktuppspelning måste du ha listnyckelbehörigheten för den behörighetsregeln För eventhubbar. Händelsehubbarnamnområdet behöver inte vara i samma prenumeration som prenumerationen som avger loggar, så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till både prenumerationer och båda prenumerationerna finns i samma AAD-klient.

Strömma aktivitetsloggen till en händelsehubb genom [att skapa en loggprofil](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Skapa en loggprofil
Du definierar hur din Azure-aktivitetslogg exporteras med hjälp av en **loggprofil**. Varje Azure-prenumeration kan bara ha en loggprofil. Dessa inställningar kan konfigureras via alternativet **Exportera** i bladet Aktivitetslogg i portalen. De kan också konfigureras programmässigt [med hjälp av AZURE Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdlets eller CLI.

Loggprofilen definierar följande.

**Var aktivitetsloggen ska skickas.** De tillgängliga alternativen är för närvarande lagringskonto eller eventhubbar.

**Vilka händelsekategorier som ska skickas.** Innebörden av *kategori* i Loggprofiler och aktivitetslogghändelser är annorlunda. I loggprofilen representerar *kategorin* operationstypen (Skriv, Ta bort, Åtgärd). I en aktivitetslogghändelse representerar *egenskapen*"* källan eller typen av händelse (till exempel Administration, ServiceHealth och Alert).

**Vilka regioner (platser) som ska exporteras.** Du bör inkludera alla platser eftersom många händelser i aktivitetsloggen är globala händelser.

**Hur länge aktivitetsloggen ska behållas i ett lagringskonto.** En kvarhållning på noll dagar innebär att loggar bevaras för alltid. Annars kan värdet vara valfritt antal dagar mellan 1 och 365.

Om bevarandeprinciper har angetts, men lagring av loggar i ett lagringskonto är inaktiverat, har bevarandeprinciper ingen effekt. Bevarandeprinciper tillämpas per dag, så i slutet av en dag (UTC) tas loggar från den dag som nu ligger utanför bevarandeprincipen bort. Om du till exempel hade en bevarandeprincip på en dag tas loggarna från i förrgår bort i början av dagen. Borttagningsprocessen börjar vid midnatt UTC, men observera att det kan ta upp till 24 timmar innan loggarna tas bort från ditt lagringskonto.


> [!IMPORTANT]
> Ett felmeddelande kan visas när du skapar en loggprofil om Microsoft.Insights-resursleverantören inte är registrerad. Se [Azure-resursleverantörer och -typer](../../azure-resource-manager/management/resource-providers-and-types.md) för att registrera den här providern.


### <a name="create-log-profile-using-the-azure-portal"></a>Skapa loggprofil med Azure-portalen

Skapa eller redigera en loggprofil med alternativet **Exportera till händelsehubb** i Azure-portalen.

1. Välj **Aktivitetslogg**på **Azure Monitor-menyn** i Azure-portalen .
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för äldreupplevelsen.

    ![Äldre erfarenhet](media/diagnostic-settings-subscription/legacy-experience.png)

3. Ange följande i bladet som visas:
   * Regioner med de händelser som ska exporteras. Du bör välja alla regioner för att säkerställa att du inte missar viktiga händelser eftersom aktivitetsloggen är en global (icke-regional) logg och därför har de flesta händelser inte en region kopplad till sig.
   * Om du vill skriva till lagringskonto:
       * Det lagringskonto som du vill spara händelser till.
       * Antalet dagar som du vill behålla dessa händelser i lagring. En inställning på 0 dagar behåller loggarna för alltid.
   * Om du vill skriva till händelsehubben:
       * Service Bus-namnområdet där du vill att en händelsehubb ska skapas för direktuppspelning av dessa händelser.

     ![Bladet Exportera aktivitetslogg](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Spara inställningarna genom att klicka på **Spara.** Inställningarna tillämpas omedelbart på din prenumeration.


### <a name="configure-log-profile-using-powershell"></a>Konfigurera loggprofil med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det redan finns en loggprofil måste du först ta bort den befintliga loggprofilen och sedan skapa en ny.

1. Används `Get-AzLogProfile` för att identifiera om det finns en loggprofil.  Om det finns en loggprofil noterar du *egenskapen name.*

1. Används `Remove-AzLogProfile` för att ta bort loggprofilen med värdet från namnegenskapen. *name*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Används `Add-AzLogProfile` för att skapa en ny loggprofil:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | Namn |Ja |Namn på din loggprofil. |
    | LagringKontoId |Inga |Resurs-ID för lagringskontot där aktivitetsloggen ska sparas. |
    | serviceBusRuleId |Inga |Service Bus Regel-ID för servicebussens namnområde som du vill ha händelsehubbar skapade i. Detta är en sträng `{service bus resource ID}/authorizationrules/{key name}`med formatet: . |
    | Location |Ja |Kommaavgränsad lista över regioner som du vill samla in aktivitetslogghändelser för. |
    | RetentionInDays |Ja |Antal dagar för vilka händelser ska behållas på lagringskontot, mellan 1 och 365. Värdet noll lagrar loggarna på obestämd tid. |
    | Kategori |Inga |Kommaavgränsad lista över händelsekategorier som ska samlas in. Möjliga värden är _Skriv,_ _Ta bort_och _Åtgärd_. |

### <a name="example-script"></a>Exempelskript
Nedan följer ett exempel på PowerShell-skript för att skapa en loggprofil som skriver aktivitetsloggen till både ett lagringskonto och en händelsehubb.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurera loggprofil med Azure CLI

Om det redan finns en loggprofil måste du först ta bort den befintliga loggprofilen och sedan skapa en ny loggprofil.

1. Används `az monitor log-profiles list` för att identifiera om det finns en loggprofil.
2. Används `az monitor log-profiles delete --name "<log profile name>` för att ta bort loggprofilen med värdet från namnegenskapen. *name*
3. Används `az monitor log-profiles create` för att skapa en ny loggprofil:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | namn |Ja |Namn på din loggprofil. |
    | lagring-konto-id |Ja |Resurs-ID för det lagringskonto som aktivitetsloggar ska sparas till. |
    | platser |Ja |Utrymmesavgränsad lista över regioner som du vill samla in aktivitetslogghändelser för. Du kan visa en lista över `az account list-locations --query [].name`alla regioner för din prenumeration med . |
    | Dagar |Ja |Antal dagar för vilka händelser ska behållas, mellan 1 och 365. Värdet noll lagras loggarna på obestämd tid (för alltid).  Om noll, då den aktiverade parametern bör ställas in på false. |
    |enabled | Ja |Sant eller Falskt.  Används för att aktivera eller inaktivera bevarandeprincipen.  Om värdet Är sant måste parametern dagar vara ett värde som är större än 0.
    | kategorier |Ja |Utrymmesavgränsad lista över händelsekategorier som ska samlas in. Möjliga värden är Skriv, Ta bort och Åtgärd. |



## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitetsloggen](../../azure-resource-manager/management/view-activity-logs.md)
* [Samla in aktivitetsloggar i Azure Monitor-loggar](activity-log-collect.md)
