---
title: Arkivera Azures resurs loggar till lagrings kontot | Microsoft Docs
description: Lär dig att arkivera Azures resurs loggar för långsiktig kvarhållning i ett lagrings konto.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262562"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Arkivera Azures resurs loggar till lagrings kontot
[Resurs loggar](resource-logs-overview.md) i Azure ger omfattande, frekventa data om den interna driften av en Azure-resurs. I den här artikeln beskrivs insamling av resurs loggar till ett Azure Storage-konto som lagrar data för arkivering.

## <a name="prerequisites"></a>Förutsättningar
Du måste [skapa ett Azure Storage-konto](../../storage/common/storage-quickstart-create-account.md) om du inte redan har ett. Lagrings kontot behöver inte finnas i samma prenumeration som resursen som skickar loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

Du bör inte använda ett befintligt lagrings konto som har andra data som inte övervakas, så att du kan kontrol lera åtkomsten till övervaknings data bättre. Om du också arkiverar [aktivitets loggen](activity-logs-overview.md) till ett lagrings konto kan du välja att använda samma lagrings konto för att behålla alla övervaknings data på en central plats.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning
Resurs loggar samlas inte in som standard. Samla in dem i ett Azure Storage-konto och andra mål genom att skapa en diagnostisk inställning för en Azure-resurs. Mer information finns i [skapa diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md) .


## <a name="data-retention"></a>Datakvarhållning
Bevarande principen definierar antalet dagar som data ska bevaras från varje logg kategori och mått data som lagras i ett lagrings konto. En bevarande princip kan vara ett valfritt antal dagar mellan 0 och 365. En bevarande princip på noll anger att händelser för den logg kategorin lagras på obestämd tid.

Bevarande principer tillämpas per dag, så i slutet av en dag (UTC) kommer loggar från den dag som nu är större än bevarande principen att tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Schema för resurs loggar i lagrings kontot

När du har skapat den diagnostiska inställningen skapas en lagrings behållare i lagrings kontot så snart en händelse inträffar i en av de aktiverade logg kategorierna. Blobarna i behållaren använder följande namngivnings konvention:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan blobben för en nätverks säkerhets grupp ha ett namn som liknar följande:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minut värdet (m = 00) är alltid 00, eftersom resurs logg händelser delas upp i enskilda blobbar per timme.

I filen PT1H. JSON lagras varje händelse med följande format. Detta kommer att använda ett vanligt schema på högsta nivå, men det är unikt för varje Azure-tjänst enligt beskrivningen i [resurs loggs schema](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Plattforms loggar skrivs till blob-lagring med hjälp av [JSON-linjer](http://jsonlines.org/), där varje händelse är en rad och rad matnings tecknet indikerar en ny händelse. Det här formatet implementerades i november 2018. Innan detta datum skrevs loggarna till Blob Storage som en JSON-matris med poster enligt beskrivningen i [förbereda för format ändring till Azure Monitor plattforms loggar som arkiverats på ett lagrings konto](resource-logs-blob-format.md).

## <a name="next-steps"></a>Nästa steg

* [Ladda ned blobbar för analys](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Arkivera Azure Active Directory loggar med Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Läs mer om resurs loggar](../../azure-monitor/platform/resource-logs-overview.md).

