---
title: Arkivera Azure-resursloggar till lagringskonto | Microsoft-dokument
description: Lär dig hur du arkiverar dina Azure-resursloggar för långsiktig kvarhållning i ett lagringskonto.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274221"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Arkivera Azure-resursloggar till lagringskonto
[Plattformsloggar](platform-logs-overview.md) i Azure, inklusive Azure Activity-logg och resursloggar, tillhandahåller detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av.  I den här artikeln beskrivs hur du samlar in plattformsloggar till ett Azure-lagringskonto för att lagra data för arkivering.

## <a name="prerequisites"></a>Krav
Du måste [skapa ett Azure-lagringskonto](../../storage/common/storage-account-create.md) om du inte redan har ett. Lagringskontot behöver inte vara i samma prenumeration som resursen som skickar loggar så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.


> [!IMPORTANT]
> Azure Data Lake Storage Gen2-konton stöds för närvarande inte som en destination för diagnostikinställningar även om de kan visas som ett giltigt alternativ i Azure-portalen.


Du bör inte använda ett befintligt lagringskonto som har andra data som inte övervakar data lagrade i det så att du bättre kan kontrollera åtkomsten till data. Om du arkiverar aktivitetsloggen och resursloggarna tillsammans kan du dock välja att använda samma lagringskonto för att lagra alla övervakningsdata på en central plats.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning
Skicka plattformsloggar till lagring och andra destinationer genom att skapa en diagnostikinställning för en Azure-resurs. Mer information finns [i Skapa diagnostikinställning för att samla in loggar och mått i Azure.](diagnostic-settings.md)


## <a name="collect-data-from-compute-resources"></a>Samla in data från beräkningsresurser
Diagnostikinställningar samlar in resursloggar för Azure-beräkningsresurser som alla andra resurser, men inte deras gästoperativsystem eller arbetsbelastningar. Om du vill samla in dessa data installerar du [Windows Azure Diagnostics-agenten](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schema för plattformsloggar i lagringskonto

När du har skapat diagnostikinställningen skapas en lagringsbehållare i lagringskontot så snart en händelse inträffar i en av de aktiverade loggkategorierna. Blobbar i behållaren använder följande namngivningskonvention:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Blobben för en nätverkssäkerhetsgrupp kan till exempel ha ett namn som liknar följande:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minutvärdet (m=00) är alltid 00, eftersom resurslogghändelser delas upp i enskilda blobbar per timme.

I filen PT1H.json lagras varje händelse med följande format. Detta kommer att använda ett gemensamt schema på den högsta nivån men vara unikt för varje Azure-tjänster enligt beskrivningen i [resursloggschema och](diagnostic-logs-schema.md) [aktivitetsloggschema](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Plattformsloggar skrivs till blob storage med [JSON-rader](http://jsonlines.org/), där varje händelse är en rad och det nya radtecknet anger en ny händelse. Detta format implementerades i november 2018. Före detta datum skrevs loggar till blob-lagring som en json-matris med poster som beskrivs i [Förbered för formatändring till Azure Monitor-plattformsloggar som arkiverats till ett lagringskonto](resource-logs-blob-format.md).

## <a name="next-steps"></a>Nästa steg

* [Läs mer om resursloggar](platform-logs-overview.md).
* [Skapa diagnostikinställning för att samla in loggar och mått i Azure](diagnostic-settings.md).
* [Ladda ner blobbar för analys](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Arkivera Azure Active Directory-loggar med Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
