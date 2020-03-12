---
title: Exportera Azure aktivitets loggen
description: Exportera Azure aktivitets logg till lagring för arkivering eller Azure-Event Hubs för export utanför Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096292"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportera Azure aktivitets logg till lagring eller Azure Event Hubs

> [!IMPORTANT]
> Metoden för att skicka Azure-aktivitets loggen till Azure Storage och Azure-Event Hubs har ändrats till [diagnostikinställningar](diagnostic-settings.md). Den här artikeln beskriver den äldre metoden som håller på att föråldras. Se uppdatering av [Azure aktivitets logg samling och export](diagnostic-settings-legacy.md) för jämförelse.


[Azure aktivitets loggen](platform-logs-overview.md) ger inblick i händelser på prenumerations nivå som har inträffat i din Azure-prenumeration. Förutom att Visa aktivitets loggen i Azure Portal eller kopiera den till en Log Analytics arbets yta där den kan analyseras med andra data som samlas in av Azure Monitor, kan du skapa en logg profil för att arkivera aktivitets loggen till ett Azure Storage-konto eller strömma den till en  Event Hub.

## <a name="archive-activity-log"></a>Arkivera aktivitets logg
Att arkivera aktivitets loggen på ett lagrings konto är användbart om du vill behålla dina loggdata längre än 90 dagar (med fullständig kontroll över bevarande principen) för granskning, statisk analys eller säkerhets kopiering. Om du bara behöver behålla dina händelser i 90 dagar eller mindre behöver du inte konfigurera arkivering till ett lagrings konto eftersom aktivitets logg händelser behålls i Azure-plattformen för 90 dagar.

## <a name="stream-activity-log-to-event-hub"></a>Strömma aktivitets logg till Händelsehubben
[Azure Event Hubs](/azure/event-hubs/) är en data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Data som skickas till en händelsehubb kan omvandlas och lagras med hjälp av valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Du kan använda strömnings funktionerna för aktivitets loggen på två sätt:
* **Strömma till system för loggning och telemetri från tredje part**: över tid blir Azure Event Hubs streaming mekanismen till att skicka in din aktivitets logg till Siem-och Log Analytics-lösningar från tredje part.
* **Skapa en anpassad telemetri-och loggnings plattform**: om du redan har en anpassad telemetri plattform eller tänker på att skapa en, kan du med hjälp av Event Hubs på ett flexibelt sätt mata in aktivitets loggen.

## <a name="prerequisites"></a>Förutsättningar

### <a name="storage-account"></a>Lagringskonto
Om du ska arkivera aktivitets loggen måste du [skapa ett lagrings konto](../../storage/common/storage-account-create.md) om du inte redan har ett. Du bör inte använda ett befintligt lagrings konto som har andra data som inte övervakas, så att du kan kontrol lera åtkomsten till övervaknings data bättre. Om du också arkiverar loggar och mått till ett lagrings konto kan du välja att använda samma lagrings konto för att behålla alla övervaknings data på en central plats.

Lagrings kontot behöver inte finnas i samma prenumeration som den prenumeration som avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna. 

> [!TIP]
> Se [konfigurera Azure Storage brand väggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) för att ge åtkomst till ett lagrings konto bakom ett skyddat virtuellt nätverk.

### <a name="event-hubs"></a>Event Hubs
Om du skickar aktivitets loggen till en Event Hub måste du [skapa en Event Hub](../../event-hubs/event-hubs-create.md) om du inte redan har en. Om du tidigare har strömmat aktivitets logg händelser till den här Event Hubs namn rymden, kommer den händelsehubben att återanvändas.

Principen för delad åtkomst definierar de behörigheter som används av den strömmande mekanismen. Strömning till Event Hubs kräver behörigheterna hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst för Event Hubs namn området i Azure Portal under fliken Konfigurera för ditt Event Hubs namn område.

Om du vill uppdatera logg profilen för aktivitets loggen så att den inkluderar strömning måste du ha ListKey-behörighet för den Event Hubs auktoriseringsregeln. Event Hubs namn området behöver inte finnas i samma prenumeration som den prenumeration som avger loggar, så länge den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna och båda prenumerationerna finns i samma AAD-klient.

Strömma aktivitets loggen till en Event Hub genom att [skapa en logg profil](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Skapa en logg profil
Du definierar hur din Azure aktivitets logg ska exporteras med en **logg profil**. Varje Azure-prenumeration kan bara ha en logg profil. Dessa inställningar kan konfigureras via alternativet **Exportera** på bladet aktivitets logg i portalen. De kan också konfigureras program mässigt [med hjälp av Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdletar eller cli.

Logg profilen definierar följande.

**Var aktivitets loggen ska skickas.** För närvarande är de tillgängliga alternativen lagrings konto eller Event Hubs.

**Vilka händelse kategorier som ska skickas.** Syftet med *kategorin* i logg profiler och aktivitets logg händelser är annorlunda. I logg profilen representerar *kategorin* åtgärds typen (Skriv, ta bort, åtgärd). I en aktivitets logg händelse representerar *kategorin*"*" källan eller typen av händelse (till exempel administration, ServiceHealth och avisering).

**Vilka regioner (platser) som ska exporteras.** Du bör inkludera alla platser eftersom många händelser i aktivitets loggen är globala händelser.

**Hur länge aktivitets loggen ska behållas i ett lagrings konto.** En kvarhållning av noll dagar innebär loggar hålls alltid. Annars kan värdet vara valfritt antal dagar mellan 1 och 365.

Om bevarande principer har ställts in, men lagrings loggar i ett lagrings konto är inaktiverat, har bevarande principerna ingen påverkan. Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto.


> [!IMPORTANT]
> Du kan få ett fel meddelande när du skapar en logg profil om Microsoft. Insights-resurs leverantören inte är registrerad. Se [Azures resurs leverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md) för att registrera denna provider.


### <a name="create-log-profile-using-the-azure-portal"></a>Skapa logg profil med hjälp av Azure Portal

Skapa eller redigera en logg profil med alternativet **Exportera till Event Hub** i Azure Portal.

1. Välj **aktivitets logg**på **Azure Monitor** -menyn i Azure Portal.
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för den äldre upplevelsen.

    ![Äldre miljö](media/diagnostic-settings-subscription/legacy-experience.png)

3. I bladet som visas anger du följande:
   * Regioner med händelser att exportera. Du bör välja alla regioner för att se till att du inte är missa viktiga händelser eftersom aktivitets loggen är en global (icke-regional) logg och att de flesta händelser inte har någon region kopplad till sig.
   * Om du vill skriva till lagrings kontot:
       * Lagrings kontot som du vill spara händelser i.
       * Antalet dagar som du vill behålla dessa händelser i lagringen. En inställning på 0 dagar behåller alltid loggarna.
   * Om du vill skriva till Event Hub:
       * Service Bus namn området som du vill att en Event Hub ska skapas för att strömma dessa händelser.

     ![Bladet exportera aktivitets logg](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klicka på **Spara** för att spara inställningarna. Inställningarna tillämpas omedelbart på din prenumeration.


### <a name="configure-log-profile-using-powershell"></a>Konfigurera logg profil med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det redan finns en logg profil måste du först ta bort den befintliga logg profilen och sedan skapa en ny.

1. Använd `Get-AzLogProfile` för att identifiera om en logg profil finns.  Om det finns en logg profil noterar du egenskapen *namn* .

1. Använd `Remove-AzLogProfile` för att ta bort logg profilen med värdet från egenskapen *Name* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Använd `Add-AzLogProfile` för att skapa en ny logg profil:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | Namn |Ja |Namn på din logg profil. |
    | StorageAccountId |Nej |Resurs-ID för det lagrings konto där aktivitets loggen ska sparas. |
    | serviceBusRuleId |Nej |Service Bus regel-ID för det Service Bus namn område som du vill ha händelse hubbar skapade i. Det här är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`. |
    | plats. |Ja |Kommaavgränsad lista över regioner för vilka du vill samla in aktivitets logg händelser. |
    | RetentionInDays |Ja |Antal dagar som händelser ska behållas i lagrings kontot, mellan 1 och 365. Värdet noll lagrar loggarna oändligt. |
    | Kategori |Nej |Kommaavgränsad lista över händelse kategorier som ska samlas in. Möjliga värden är _Write_, _Delete_och _Action_. |

### <a name="example-script"></a>Exempelskript
Följande är ett exempel på PowerShell-skript för att skapa en logg profil som skriver aktivitets loggen till både ett lagrings konto och en Event Hub.

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


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurera logg profil med Azure CLI

Om det redan finns en logg profil måste du först ta bort den befintliga logg profilen och sedan skapa en ny logg profil.

1. Använd `az monitor log-profiles list` för att identifiera om en logg profil finns.
2. Använd `az monitor log-profiles delete --name "<log profile name>` för att ta bort logg profilen med värdet från egenskapen *Name* .
3. Använd `az monitor log-profiles create` för att skapa en ny logg profil:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | namn |Ja |Namn på din logg profil. |
    | lagrings konto-ID |Ja |Resurs-ID för det lagrings konto som aktivitets loggar ska sparas i. |
    | platser |Ja |Blankstegsavgränsad lista över regioner för vilka du vill samla in aktivitets logg händelser. Du kan visa en lista över alla regioner för din prenumeration med hjälp av `az account list-locations --query [].name`. |
    | dagar |Ja |Antal dagar som händelser ska behållas, mellan 1 och 365. Om värdet är noll lagras loggarna oändligt (för alltid).  Om värdet är noll ska parametern Enabled vara inställd på falskt. |
    |enabled | Ja |SANT eller FALSKT.  Används för att aktivera eller inaktivera bevarande principen.  Om värdet är true måste parametern Days vara ett värde som är större än 0.
    | kategorier |Ja |Blankstegsavgränsad lista över händelse kategorier som ska samlas in. Möjliga värden är Write, Delete och action. |



## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitets loggen](../../azure-resource-manager/management/view-activity-logs.md)
* [Samla in aktivitets loggar i Azure Monitor loggar](activity-log-collect.md)
