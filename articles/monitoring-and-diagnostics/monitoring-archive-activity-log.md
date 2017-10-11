---
title: Arkivera Azure aktivitetsloggen | Microsoft Docs
description: "Lär dig mer om att arkivera dina Azure-aktivitetsloggen för långsiktig kvarhållning i ett lagringskonto."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 0e3a5b84f57eac96249430fa1c2c4cc076c2926a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="archive-the-azure-activity-log"></a>Arkivera Azure-aktivitetsloggen
I den här artikeln visar vi hur du kan använda Azure-portalen, PowerShell-Cmdlets och plattformsoberoende CLI för att arkivera dina [ **Azure-aktivitetsloggen** ](monitoring-overview-activity-logs.md) i ett lagringskonto. Det här alternativet är användbart om du vill behålla din aktivitetsloggen som är längre än 90 dagar (med fullständig kontroll över bevarandeprincipen) för granskning, statiska analys eller säkerhetskopiering. Om du bara behöver lagra dina händelser i 90 dagar eller mindre och du behöver inte konfigurera arkivering till ett lagringskonto eftersom aktivitetsloggen händelser lagras i Azure-plattformen i 90 dagar utan att aktivera arkivering.

## <a name="prerequisites"></a>Krav
Innan du börjar måste du [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) som du kan arkivera dina aktivitetsloggen. Vi rekommenderar starkt att du inte använder ett befintligt lagringskonto som har andra, icke-övervakning data som lagras i den så att du bättre kan styra åtkomsten till övervakningsdata. Men om du även arkiverar diagnostikloggar och mått till ett lagringskonto, kan det vara bra att använda detta lagringskonto för aktivitetsloggen samt för att hålla alla övervakningsdata på en central plats. Storage-konto som du använder måste vara ett allmänt lagringskonto inte ett blob storage-konto. Storage-konto behöver inte finnas i samma prenumeration som prenumerationen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

## <a name="log-profile"></a>Log-profil
Om du vill arkivera aktivitetsloggen med någon av metoderna nedan kan du ange den **loggen profil** för en prenumeration. Log-profil definierar typ av händelser som lagras eller strömmas och utdata – lagring konto och/eller event hub. Den definierar även bevarandeprincip (antal dagar att behålla) för händelser som lagras i ett lagringskonto. Om bevarandeprincipen anges till noll, som händelser lagras på obestämd tid. I annat fall kan detta anges till ett värde mellan 1 och 2147483647. Bevarandeprinciper är tillämpade per dag, så i slutet av dagen (UTC) loggar från den dagen är nu utöver kvarhållning princip kommer att tas bort. Till exempel om du har en bevarandeprincip på en dag skulle i början av dagen idag loggar från dag före igår tas bort. [Du kan läsa mer om loggen profiler här](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Arkivera aktivitetsloggen med hjälp av portalen
1. I portalen klickar du på den **aktivitetsloggen** länk i navigeringen till vänster. Om du inte ser en länk för aktivitetsloggen, klickar du på den **fler tjänster** länka först.
   
    ![Navigera till bladet för aktivitetsloggen](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Överst på bladet klickar du på **exportera**.
   
    ![Klicka på knappen Export](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. I bladet som visas, markera kryssrutan för **exportera till ett lagringskonto** och välj ett lagringskonto.
   
    ![Ange ett lagringskonto](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Med hjälp av skjutreglaget eller textruta, definiera ett antal dagar som aktiviteten logghändelser ska behållas i ditt lagringskonto. Om du vill ha dina data kvar i lagringskontot på obestämd tid genom att ange siffran noll.
5. Klicka på **Spara**.

## <a name="archive-the-activity-log-via-powershell"></a>Arkivera aktivitetsloggen via PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| StorageAccountId |Nej |Resurs-ID för Storage-konto som aktivitetsloggar ska sparas. |
| Platser |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser för aktivitetsloggen. Du kan visa en lista över alla regioner [genom att besöka sidan](https://azure.microsoft.com/en-us/regions) eller genom att använda [Azure Management REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Ja |Antal dagar för vilka händelser som ska behållas, mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid (alltid). |
| Kategorier |Ja |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva och ta bort åtgärd. |

## <a name="archive-the-activity-log-via-cli"></a>Arkivera aktivitetsloggen via CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| namn |Ja |Namnet på loggen profilen. |
| storageId |Nej |Resurs-ID för Storage-konto som aktivitetsloggar ska sparas. |
| Platser |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser för aktivitetsloggen. Du kan visa en lista över alla regioner [genom att besöka sidan](https://azure.microsoft.com/en-us/regions) eller genom att använda [Azure Management REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Ja |Antal dagar för vilka händelser som ska behållas, mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid (alltid). |
| Kategorier |Ja |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva och ta bort åtgärd. |

## <a name="storage-schema-of-the-activity-log"></a>Storage-schemat för aktivitetsloggen
När du har konfigurerat arkivering, skapas en lagringsbehållare i storage-konto så snart aktivitetsloggen händelse inträffar. Blobbar i behållaren följer samma format i aktivitetsloggen och diagnostikloggar. Strukturen för de här blobbar är:

> insikter-operativa-loggar/name = standard/resourceId = / PRENUMERATIONER / {prenumerations-ID} / y = {numeriskt årtal} / m = {tvåsiffrig numeriska month} / d = {tvåsiffrig kalenderdag} / tim = {tvåsiffrig 24-timmarsklocka hour}/m=00/PT1H.json
> 
> 

Till exempel kan en blobbnamnet vara:

> Insights-Operational-Logs/Name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON
> 
> 

Varje PT1H.json blobb innehåller en JSON-blob av händelser som inträffade inom en timme som anges i blob-URL (t.ex. h = 12). Under den aktuella timman läggs händelser till filen PT1H.json när de inträffar. Minuten (m = 00) är alltid 00, eftersom aktiviteten logghändelser delas upp i enskilda blobbar per timme.

Varje händelse lagras i filen PT1H.json i matrisen ”innehåller” följa det här formatet:

```
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
| time |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| resourceId |Resurs-ID för resursen påverkas. |
| operationName |Namnet på åtgärden. |
| category |Kategori för åtgärden, t.ex. Skrivning, Läs, åtgärden. |
| resultType |Typ av resultat, t.ex. Lyckades, fel, Start |
| resultSignature |Beror på resurstypen. |
| durationMs |Varaktighet för åtgärden i millisekunder |
| callerIpAddress |IP-adressen för den användare som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. |
| correlationId |Vanligtvis ett GUID i strängformatet. Händelser som delar en correlationId tillhöra samma uber åtgärd. |
| identity |JSON-blob som beskriver auktoriserings- och anspråk. |
| Auktorisering |BLOB RBAC egenskaper för händelsen. Inkluderar vanligtvis egenskaperna ””, ”roll” och ”omfattning”. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| location |Region i platsen uppstod (eller global). |
| properties |En uppsättning `<Key, Value>` par (d.v.s. ordlista) som beskriver information om händelsen. |

> [!NOTE]
> Egenskaper och användning av dessa egenskaper kan variera beroende på resursen.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Ladda ned blobbar för analys](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Dataströmmen aktivitetsloggen i Händelsehubbar](monitoring-stream-activity-logs-event-hubs.md)
* [Läs mer om aktivitetsloggen](monitoring-overview-activity-logs.md)

