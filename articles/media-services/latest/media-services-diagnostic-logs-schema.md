---
title: Diagnostikloggar för Azure Media Services - Azure
description: Den här artikeln visar diagnostikloggscheman för Diagnostikloggar för Azure Media Services.
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
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750884"
---
# <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

[Med Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program fungerar. Du kan övervaka Diagnostikloggar för Media Services och skapa aviseringar och meddelanden för insamlade mått och loggar. Du kan skicka loggar till [Azure Storage,](https://azure.microsoft.com/services/storage/)strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/)eller använda tjänster från tredje part.

Detaljerad information finns i [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) och [Azure Monitor Diagnostic logs](../../azure-monitor/platform/platform-logs-overview.md).

I den här artikeln beskrivs diagnostikloggscheman för diagnostikloggar för Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schema för diagnostikloggar på högsta nivå

Detaljerad beskrivning av schemat för diagnostikloggar på den högsta nivån finns i [Tjänster, scheman och kategorier som stöds för Azure Diagnostic Logs](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schema för logg för nyckelleverans

### <a name="properties"></a>Egenskaper

Dessa egenskaper är specifika för nyckelleveransloggschemat.

|Namn|Beskrivning|
|---|---|
|keyId (keyId)|ID:an för den begärda nyckeln.|
|Keytype|Kan vara ett av följande värden: "Clear" (ingen kryptering), "FairPlay", "PlayReady" eller "Widevine".|
|policyName|Azure Resource Manager-namnet på principen.|
|tokenType (tokenType)|Tokentypen.|
|statusMessage|Statusmeddelandet.|

### <a name="examples"></a>Exempel

Egenskaper för schemat för nyckelleveransbegäranden.

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

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

[Övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)
