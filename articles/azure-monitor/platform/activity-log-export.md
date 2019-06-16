---
title: Exportera Azure-aktivitetsloggen
description: Exportera Azure-aktivitetsloggen till lagring för arkivering eller Azure Event Hubs för att exportera utanför Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248151"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportera aktivitetslogg för Azure storage eller Azure Event Hubs
Den [Azure-aktivitetsloggen](activity-logs-overview.md) ger information om händelser på prenumerationsnivå som har inträffat i Azure-prenumerationen. Förutom att visa aktivitetsloggen i Azure-portalen eller kopiera den till en Log Analytics-arbetsyta där de kan analyseras med annan information som samlas in av Azure Monitor kan du skapa en loggprofil om du vill arkivera aktivitetslogg till ett Azure storage-konto eller strömma det till en  Event Hub.

## <a name="archive-activity-log"></a>Arkivera aktivitetsloggen
Arkivera aktivitetsloggen till ett lagringskonto är användbart om du vill behålla dina loggdata som är längre än 90 dagar (med fullständig kontroll över bevarandeprincipen) för granskning, statiska analys eller säkerhetskopiering. Om du behöver bara att behålla dina händelser i 90 dagar eller mindre du behöver inte konfigurera arkivering till ett lagringskonto eftersom aktivitetslogghändelser finns kvar i Azure-plattformen i 90 dagar.

## <a name="stream-activity-log-to-event-hub"></a>Stream aktivitetsloggen till Event Hub
[Azure Event Hubs](/azure/event-hubs/) är en dataströmningsplattform och händelseinmatningstjänst som kan ta emot och bearbeta miljontals händelser per sekund. Data som skickas till en händelsehubb kan omvandlas och lagras med hjälp av valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Det finns två sätt som du kan använda den strömmande kapaciteten för aktivitetsloggen:
* **Stream att loggning och telemetri tredjepartssystem**: Framöver kommer blir Azure Event Hubs streaming mekanism för att skicka vidare din aktivitetslogg till tredje parts SIEMs och logga Analyslösningar.
* **Skapa en anpassad telemetri och loggning**: Om du redan har en specialbyggda telemetri plattform eller funderar på att skapa en mycket skalbar publicerings-/ natur Händelsehubbar kan du flexibelt kan mata in aktivitetsloggen. 

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="storage-account"></a>Lagringskonto
Om du arkivera din aktivitetslogg, måste du [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md) om du inte redan har ett. Du bör inte använda ett befintligt lagringskonto som har andra, icke-övervakning av data som lagras i den så att du kan få bättre kontroll över åtkomsten till övervakningsdata. Om du även arkivera diagnostikloggar och mått till ett lagringskonto om du använder det samma lagringskontot för att hålla alla övervakningsdata på en central plats.

Storage-kontot behöver inte finnas i samma prenumeration som prenumerationen för loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.
> [!NOTE]
>  Du kan för närvarande inte arkivera data till ett lagringskonto som ligger bakom ett skyddat virtuellt nätverk.

### <a name="event-hubs"></a>Event Hubs
Om du skickar din aktivitetslogg till en händelsehubb, måste du [skapar en event hub](../../event-hubs/event-hubs-create.md) om du inte redan har ett. Om du strömmat tidigare händelser i aktivitetsloggen till Event Hubs-namnområdet, återanvändas den händelsehubben.

Princip för delad åtkomst definierar de behörigheter som har mekanismen för direktuppspelning. Strömma till Event Hubs kräver behörighet att hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst för Event Hubs-namnområde i Azure-portalen under fliken Konfigurera för Event Hubs-namnområdet.

Om du vill uppdatera aktivitetsloggen log profilen om du vill inkludera streaming, måste du ha behörigheten ListKey på den Event Hubs-auktoriseringsregeln. Event Hubs-namnområdet behöver inte vara i samma prenumeration som den prenumeration som genererar loggar, så länge som den användare som konfigurerar inställningen har rätt RBAC åtkomst till båda prenumerationerna och båda prenumerationerna finns i samma AAD-klient.

Stream aktivitetsloggen till en Händelsehubb genom [skapar en profil för Log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Skapa en loggprofil
Du definierar hur din Azure-aktivitetsloggen är exporteras med hjälp av en **loggprofilen**. Varje Azure-prenumeration kan bara ha en loggprofil. De här inställningarna kan konfigureras via den **exportera** alternativet i bladet aktivitetsloggen i portalen. De kan också konfigureras programmässigt [med hjälp av REST-API i Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdletar eller CLI.

Loggprofil definierar följande.

**Där aktivitetsloggen ska skickas.** De tillgängliga alternativen är för närvarande Lagringskonto eller Event Hubs.

**Vilka kategorier ska skickas.** Enligt *kategori* i Loggprofiler och aktivitetsloggen händelser är olika. I profilen för den Log *kategori* representerar åtgärdstypen (skriva, ta bort, åtgärd). I en aktivitetslogghändelse den *kategori*”* egenskap representerar källan eller typen av händelse (till exempel Administration, ServiceHealth och avisering).

**Vilka regioner (platser) ska exporteras.** Eftersom många händelser i aktivitetsloggen är globala händelser bör du inkludera alla platser.

**Hur länge aktivitetsloggen ska behållas i ett Lagringskonto.** En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.

Om principerna för kvarhållning anges, men lagra loggar i ett lagringskonto är inaktiverad, har ingen effekt med principer för kvarhållning. Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto.



> [!WARNING]
> Formatet på loggdata i storage-kontot ändras till JSON-rader från den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Skapa loggprofil med hjälp av Azure portal

Skapa eller redigera en loggprofil med den **exportera till Event Hub** alternativ i Azure-portalen.

1. Från den **övervakaren** menyn i Azure portal, Välj **exportera till Event Hub**.

    ![Exportera-knappen i portalen](media/activity-log-export/portal-export.png)

3. På bladet som visas anger du följande:
   * Regioner med händelser som ska exporteras. Du bör välja alla regioner för att se till att du inte missa viktiga händelser eftersom aktivitetsloggen är en global (icke-regionalt) logg och så inte behöver en region som är kopplade till dem i de flesta händelser. 
   * Om du vill skriva till lagringskontot:
       * Det Lagringskonto som du vill spara händelser.
       * antal dagar som du vill behålla dessa händelser i lagring. En inställning på 0 dagar behåller loggarna alltid.
   * Om du vill skriva till event hub:
       * Service Bus Namespace där du vill ha en Händelsehubb skapas för direktuppspelning av dessa händelser.

     ![Exportera aktivitetslogg bladet](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klicka på **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart på din prenumeration.


### <a name="configure-log-profile-using-powershell"></a>Konfigurera loggprofil med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det finns redan en loggprofil, måste du först ta bort den befintliga log-profilen och sedan skapa en ny.

1. Använd `Get-AzLogProfile` att identifiera om det finns en loggprofil.  Observera om en logg-profilen finns i *namn* egenskapen.

1. Använd `Remove-AzLogProfile` att ta bort log-profilen med värdet från den *namn* egenskapen.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Använd `Add-AzLogProfile` att skapa en ny loggprofil:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | Name |Ja |Namnet på din loggprofil. |
    | StorageAccountId |Nej |Resurs-ID för det Lagringskonto där aktivitetsloggen ska sparas. |
    | serviceBusRuleId |Nej |Service Bus regel-ID för Service Bus-namnområde som du vill ha händelsehubbar som skapats i. Detta är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser i aktivitetsloggen. |
    | RetentionInDays |Ja |Antal dagar som händelser ska behållas i storage-konto, mellan 1 och 2147483647. Värdet noll lagras loggarna på obestämd tid. |
    | Category |Nej |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är _skriva_, _ta bort_, och _åtgärd_. |

### <a name="example-script"></a>Exempelskript
Följande är ett exempel PowerShell-skript för att skapa en loggprofil som skriver aktivitetsloggen till både en storage-konto och event hub.

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


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurera loggprofil med hjälp av Azure CLI

Om det finns redan en loggprofil, måste du först ta bort den befintliga log-profilen och sedan skapa en ny loggprofil.

1. Använd `az monitor log-profiles list` att identifiera om det finns en loggprofil.
2. Använd `az monitor log-profiles delete --name "<log profile name>` att ta bort log-profilen med värdet från den *namn* egenskapen.
3. Använd `az monitor log-profiles create` att skapa en ny loggprofil:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | name |Ja |Namnet på din loggprofil. |
    | storage-account-id |Ja |Resurs-ID för det Lagringskonto där aktivitetsloggar ska sparas. |
    | locations |Ja |Blankstegsavgränsad lista över regioner som du vill samla in händelser i aktivitetsloggen. Du kan visa en lista över alla regioner för din prenumeration med hjälp av `az account list-locations --query [].name`. |
    | days |Ja |Antal dagar för vilka händelser ska behållas, mellan 1 och 365. Värdet noll kommer att lagra loggarna på obestämd tid (alltid).  Om noll, sedan parametern aktiverade ska anges till true. |
    |enabled | Ja |SANT eller FALSKT.  Används för att aktivera eller inaktivera bevarandeprincipen.  Om värdet är True måste dagsparametern vara ett värde som är större än 0.
    | kategorier |Ja |Blankstegsavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva, ta bort och åtgärder. |



## <a name="activity-log-schema"></a>Aktivitetslogg
Om skickas till Azure storage eller Event Hub, skrivs aktivitetsloggdata till JSON med följande format.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
Elementen i den här JSON beskrivs i följande tabell.

| Elementnamn | Beskrivning |
| --- | --- |
| time |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| resourceId |Resurs-ID för resursen som påverkas. |
| operationName |Åtgärdens namn. |
| category |Kategori för åtgärden, t.ex. Skriva, Läs, åtgärd. |
| resultType |Vilken typ av resultatet, t.ex. Klart, fel, Start |
| resultSignature |Beror på resurstypen. |
| durationMs |Varaktighet i millisekunder |
| callerIpAddress |IP-adressen för den användare som har utfört den åtgärden eller UPN-anspråket SPN-anspråk baserat på tillgänglighet. |
| correlationId |Vanligtvis ett GUID i formatet för strängen. Händelser som delar en correlationId tillhöra samma uber åtgärd. |
| identity |JSON-blob som beskriver auktorisering och anspråk. |
| authorization |BLOB för RBAC egenskaper för händelsen. Vanligtvis inkluderar egenskaper för ”action”, ”roll” och ”omfång”. |
| nivå |Nivån på händelsen. En av följande värden: _Kritiska_, _fel_, _varning_, _endast i informationssyfte_, och _utförlig_ |
| location |Region i platsen uppstod (eller global). |
| properties |Uppsättning `<Key, Value>` par (d.v.s. ordlista) som beskriver informationen om händelsen. |

> [!NOTE]
> Egenskaper och användning av de här egenskaperna kan variera beroende på resurs.



## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitetsloggen](../../azure-resource-manager/resource-group-audit.md)
* [Samla in aktivitetsloggen i Azure Monitor-loggar](activity-log-collect.md)
