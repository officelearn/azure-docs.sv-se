---
title: Anpassade spårningsscheman för B2B - meddelanden i Azure Logic Apps | Microsoft Docs
description: Skapa anpassade spårningsscheman som övervakar B2B-meddelanden i integrationskonton för Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 68c5d6e68562d4027c102e1bde42c775648e58c4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124851"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Skapa anpassade spårningsscheman som övervakar arbetsflöden för slutpunkt till slutpunkt i Azure Logic Apps

Det finns inbyggda spåra att du kan aktivera för olika delar av din business-to-business-arbetsflödet, till exempel uppföljning AS2 eller X12 meddelanden. När du skapar arbetsflöden som omfattar en logikapp, BizTalk Server, SQL Server eller något annat lager kan du aktivera anpassade spårning som loggar händelser från början till slutet av arbetsflödet. 

Den här artikeln innehåller anpassad kod som du kan använda i lagren utanför din logikapp. 

## <a name="custom-tracking-schema"></a>Anpassat spårningsschema

```json
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
```

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| sourceType |   | Typ av kör källan. Tillåtna värden är **Microsoft.Logic/workflows** och **anpassade**. (Obligatorisk) |
| Källa |   | Om typ av datakälla är **Microsoft.Logic/workflows**, information om källdatorn måste följa det här schemat. Om typ av datakälla är **anpassade**, schemat är en JToken. (Obligatorisk) |
| system-ID | Sträng | Logic app system-ID. (Obligatorisk) |
| runId | Sträng | Logikappskörningen ID. (Obligatorisk) |
| operationName | Sträng | Namnet på åtgärden (till exempel åtgärd eller utlösare). (Obligatorisk) |
| repeatItemScopeName | Sträng | Upprepa objektnamn om åtgärden finns i en `foreach` / `until` loop. (Obligatorisk) |
| repeatItemIndex | Integer | Om åtgärden finns i en `foreach` / `until` loop. Anger det upprepade objekt indexet. (Obligatorisk) |
| spårnings-ID | Sträng | Spårnings-ID för att knyta samman meddelanden. (Valfritt) |
| correlationId | Sträng | Korrelations-ID, att korrelera meddelanden. (Valfritt) |
| clientRequestId | Sträng | Klienten kan fylla i den för att korrelera meddelanden. (Valfritt) |
| eventLevel |   | Nivån på händelsen. (Obligatorisk) |
| eventTime |   | Tid för händelse, i UTC-format åååå-MM-DDTHH:MM:SS.00000Z. (Obligatorisk) |
| RecordType |   | Typ av bevisade framgångar. Tillåtna värde är **anpassade**. (Obligatorisk) |
| post |   | Anpassad posttyp. Formatet är JToken. (Obligatorisk) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollet-spårningsscheman

Information om B2B-protokollet spårningsscheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md)
* Lär dig mer om [spåra B2B-meddelanden i Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)