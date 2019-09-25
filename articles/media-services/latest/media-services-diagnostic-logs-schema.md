---
title: Scheman för Azure Media Services diagnostiska loggar – Azure
description: Den här artikeln visar scheman för Azure Media Services diagnostiska loggar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: f95258368664aabeb89426afb83854378c0e4429
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261084"
---
# <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

Med [Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program presterar. Du kan övervaka Media Services diagnostikloggar och skapa aviseringar och aviseringar för insamlade mått och loggar. Du kan skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/)eller använda tjänster från tredje part.

Detaljerad information finns i [Azure Monitor mått](../../azure-monitor/platform/data-platform.md) och [Azure Monitor diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md).

I den här artikeln beskrivs scheman för Media Services diagnostiska loggar.

## <a name="top-level-diagnostic-logs-schema"></a>Schema för diagnostiska loggar på högsta nivån

Detaljerad beskrivning av schemat på den högsta nivån av diagnostiska loggar finns i [tjänster, scheman och kategorier som stöds för Azure Diagnostic-loggar](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Logg schema för nyckel leverans

### <a name="properties"></a>properties

De här egenskaperna är speciella för logg schema för nyckel leverans.

|Name|Beskrivning|
|---|---|
|keyId|ID för den begärda nyckeln.|
|keyType|Kan vara något av följande värden: "Rensa" (ingen kryptering), "FairPlay", "PlayReady" eller "Widevine".|
|policyName|Principens Azure Resource Manager namn.|
|tokenType|Tokentyp.|
|statusMessage|Status meddelandet.|

### <a name="examples"></a>Exempel

Egenskaper för nyckel leverans begär ande schema.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Nästa steg

[Övervaka Media Servicess statistik och diagnostiska loggar](media-services-metrics-diagnostic-logs.md)
