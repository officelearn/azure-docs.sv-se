---
title: Arkivera Azure-aktivitetsloggen
description: Arkivera dina Azure-aktivitetsloggen för långsiktig kvarhållning i ett lagringskonto.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 33681c7c9e1a625757e3f9403820ed3f469bec64
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705794"
---
# <a name="archive-the-azure-activity-log"></a>Arkivera Azure-aktivitetsloggen
I den här artikeln visar vi hur du kan använda Azure-portalen, PowerShell-Cmdlets och plattformsoberoende CLI för att arkivera dina [ **Azure-aktivitetsloggen** ](monitoring-overview-activity-logs.md) i ett lagringskonto. Det här alternativet är användbart om du vill behålla din aktivitetslogg som är längre än 90 dagar (med fullständig kontroll över bevarandeprincipen) för granskning, statiska analys eller säkerhetskopiering. Om du behöver bara att behålla dina händelser i 90 dagar eller mindre du behöver inte konfigurera arkivering till ett lagringskonto eftersom aktivitetslogghändelser finns kvar i Azure-plattformen i 90 dagar utan att aktivera arkivering.

> [!WARNING]
> Formatet för loggdata i lagringskontot ändras till JSON Lines den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md) som du kan arkivera din aktivitetslogg. Vi rekommenderar starkt att du inte använder ett befintligt lagringskonto som har andra, icke-övervakning av data som lagras i den så att du kan få bättre kontroll över åtkomsten till övervakningsdata. Om du även arkiverar diagnostikloggar och mått till ett lagringskonto, men kan det vara klokt du använder det lagringskontot för din aktivitetslogg så att alla övervakningsdata på en central plats. Storage-kontot behöver inte finnas i samma prenumeration som prenumerationen för loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
>  Du kan för närvarande inte arkivera data till ett lagringskonto som skapats bakom ett skyddat virtuellt nätverk.

## <a name="log-profile"></a>Loggprofil
Om du vill arkivera aktivitetsloggen med någon av metoderna nedan, anger du den **Loggprofil** för en prenumeration. Log-profil definierar vilken typ av händelser som lagras eller strömmas och utdata – storage-konto och/eller event hub. Den definierar även bevarandeprincipen (antal dagar) för händelser som lagras i ett lagringskonto. Om policyn för datalagring i anges till noll, lagras händelser på obestämd tid. I annat fall kan detta anges till ett värde mellan 1 och 2147483647. Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto. [Du kan läsa mer om log profiler här](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Arkivera aktivitetsloggen med hjälp av portalen
1. I portalen klickar du på den **aktivitetsloggen** länk navigeringen till vänster. Om du inte ser en länk för aktivitetsloggen, klickar du på den **alla tjänster** länka först.
   
    ![Gå till bladet aktivitetslogg](media/monitoring-archive-activity-log/activity-logs-portal-navigate-v2.png)
2. Överst på bladet klickar du på **exportera till Event Hub**.
   
    ![Klicka på knappen Export](media/monitoring-archive-activity-log/activity-logs-portal-export-v2.png)
3. På bladet som visas, markerar du kryssrutan för **exportera till ett lagringskonto** och välj ett lagringskonto.
   
    ![Ange ett lagringskonto](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Med hjälp av skjutreglaget eller textruta, definiera ett antal dagar (0 till 365) som aktivitetslogghändelser ska behållas i ditt storage-konto. Om du vill ha dina data sparas i lagringskontot på obestämd tid, anges numret till noll. Om du vill ange hur många dagar som är mer än 365 kan du använda PowerShell eller CLI-metoder som beskrivs nedan.
5. Klicka på **Spara**.

## <a name="archive-the-activity-log-via-powershell"></a>Arkivera aktivitetsloggen via PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| StorageAccountId |Ja |Resurs-ID för det Lagringskonto där aktivitetsloggar ska sparas. |
| Platser |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser i aktivitetsloggen. Du kan visa en lista över alla regioner för din prenumeration med hjälp av `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Nej |Antal dagar för vilka händelser ska behållas, mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid (alltid). |
| Kategorier |Nej |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva, ta bort och åtgärder.  Om det inte anges, antas alla möjliga värden |

## <a name="archive-the-activity-log-via-cli"></a>Arkivera aktivitetsloggen via CLI

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| namn |Ja |Namnet på din loggprofil. |
| Storage-konto-id |Ja |Resurs-ID för det Lagringskonto där aktivitetsloggar ska sparas. |
| platser |Ja |Blankstegsavgränsad lista över regioner som du vill samla in händelser i aktivitetsloggen. Du kan visa en lista över alla regioner för din prenumeration med hjälp av `az account list-locations --query [].name`. |
| dagar |Ja |Antal dagar för vilka händelser ska behållas, mellan 1 och 2147483647. Värdet noll kommer att lagra loggarna på obestämd tid (alltid).  Om noll, sedan parametern aktiverade ska anges till true. |
|aktiverad | Ja |SANT eller FALSKT.  Används för att aktivera eller inaktivera bevarandeprincipen.  Om värdet är True måste dagsparametern vara ett värde som är större än 0.
| kategorier |Ja |Blankstegsavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva, ta bort och åtgärder. |

## <a name="storage-schema-of-the-activity-log"></a>Storage-schemat för aktivitetsloggen
När du har konfigurerat arkivering, skapas en lagringsbehållare i lagringskontot när en aktivitetslogghändelsen inträffar. Blobbar i behållaren följer samma namngivningskonvention för aktivitetsloggar och diagnostiska loggar som visas här:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan ett blobbnamn vara:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (t.ex. h = 12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minutvärdet (m = 00) är alltid 00, eftersom aktivitetslogghändelser delas upp i enskilda blobar per timme.

Varje händelse som lagras i filen pt1h.JSON i matrisen ”poster” efter det här formatet:

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
| Auktorisering |BLOB för RBAC egenskaper för händelsen. Vanligtvis inkluderar egenskaper för ”action”, ”roll” och ”omfång”. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”fel”, ”varning”, ”information” och ”utförlig” |
| location |Region i platsen uppstod (eller global). |
| properties |Uppsättning `<Key, Value>` par (d.v.s. ordlista) som beskriver informationen om händelsen. |

> [!NOTE]
> Egenskaper och användningen av dessa egenskaper kan variera beroende på resurs.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Ladda ned blobar för analys](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream aktivitetsloggen till Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
* [Läs mer om aktivitetsloggen](monitoring-overview-activity-logs.md)

