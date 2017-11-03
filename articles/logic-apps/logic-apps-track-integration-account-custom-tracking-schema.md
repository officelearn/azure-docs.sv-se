---
title: "Spårning av anpassade scheman för övervakning av B2B - Azure Logic Apps | Microsoft Docs"
description: "Skapa anpassade spårning scheman för att övervaka B2B-meddelanden från transaktioner i ditt Azure-konto för integrering."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b71a4938dde2a71f1ce29403af7aa9101358d64c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Aktivera spårning att övervaka hela arbetsflödet, slutpunkt-till-slutpunkt
Det finns inbyggda spåra att du kan aktivera för olika delar av ditt företag att arbetsflödet, till exempel uppföljning AS2 eller X12 meddelanden. När omfattar du skapar arbetsflöden som en logikapp, BizTalk Server, SQL Server eller något annat lager kan du aktivera anpassade spårning loggar händelser från början till slutet av arbetsflödet. 

Det här avsnittet innehåller anpassad kod som du kan använda i lagren utanför din logikapp. 

## <a name="custom-tracking-schema"></a>Spårning av anpassade schemat
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| SourceType |   | Typ av kör källan. Tillåtna värden är **Microsoft.Logic/workflows** och **anpassade**. (Obligatoriskt) |
| Källa |   | Om källtypen är **Microsoft.Logic/workflows**, källinformation måste följa det här schemat. Om källtypen är **anpassade**, schemat är en JToken. (Obligatoriskt) |
| system-ID | Sträng | Logik app system-ID. (Obligatoriskt) |
| runId | Sträng | Logikapp kör ID. (Obligatoriskt) |
| operationName | Sträng | Namnet på åtgärden (till exempel åtgärd eller utlösare). (Obligatoriskt) |
| repeatItemScopeName | Sträng | Upprepa objektnamnet om åtgärden i en `foreach` / `until` loop. (Obligatoriskt) |
| repeatItemIndex | Integer | Om instruktionen är inuti en `foreach` / `until` loop. Anger det upprepade objekt indexet. (Obligatoriskt) |
| trackingId | Sträng | Spårnings-ID för att korrelera meddelanden. (Valfritt) |
| correlationId | Sträng | Korrelations-ID, att korrelera meddelanden. (Valfritt) |
| ClientRequestId | Sträng | Klienten kan fylla i den för att korrelera meddelanden. (Valfritt) |
| EventLevel |   | Nivå av händelsen. (Obligatoriskt) |
| EventTime |   | Tid för händelse, i UTC-format åååå-MM-DDTHH:MM:SS.00000Z. (Obligatoriskt) |
| RecordType |   | Typ av post spåra. Tillåtna värde är **anpassade**. (Obligatoriskt) |
| Post |   | Anpassad posttyp. Tillåtna format är JToken. (Obligatoriskt) |

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollet spårning scheman
Information om spårning av scheman B2B-protokollet finns i:
* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md).   
* Lär dig mer om [spåra B2B-meddelanden i Operations Management Suite-portalen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Lär dig mer om den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).
