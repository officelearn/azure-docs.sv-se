---
title: Azure Media Services diagnostiska loggar scheman, Azure
description: Den här artikeln visar Azure Media Services diagnostikloggar scheman.
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
ms.openlocfilehash: 225bc4368d2a87fa5fef3f74624fd10f42dc7c97
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555191"
---
# <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

[Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program utför. Du kan övervaka diagnostikloggar för Media Services och skapa aviseringar och meddelanden för insamlade mått och loggar. Du kan skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/), eller använda tjänster med 3 part.

Detaljerad information finns i [Azure Monitor Metrics](../../azure-monitor/platform/data-collection.md) och [Azure Monitor diagnostiska loggar](../../azure-monitor/platform/diagnostic-logs-overview.md).

Den här artikeln beskriver medietjänster scheman för diagnostikloggar.

## <a name="top-level-diagnostic-logs-schema"></a>Översta diagnostikloggar schema

Detaljerad beskrivning av översta diagnostikloggar schemat finns i [tjänster, scheman och kategorier som stöds för diagnostikloggar för Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Viktiga leverans log schema

### <a name="properties"></a>Egenskaper

De här egenskaperna är specifika för leverans av nyckel log schemat.

|Namn|Beskrivning|
|---|---|
|keyId|ID för den begärda nyckeln.|
|KeyType|Kan vara något av följande värden: ”Rensa” (Ingen kryptering), ”FairPlay”, ”PlayReady” eller ”Widevine”.|
|policyName|Azure Resource Manager-namnet på principen.|
|TokenType|Tokentypen.|
|statusMessage|Statusmeddelande.|

### <a name="examples"></a>Exempel

Egenskaper för leverans av nyckel begäranden schemat.

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

[Övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)
