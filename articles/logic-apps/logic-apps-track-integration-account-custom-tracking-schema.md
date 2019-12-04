---
title: Anpassade spårnings scheman för B2B-meddelanden
description: Skapa anpassade spårnings scheman som övervakar B2B-meddelanden i integrations konton för Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792792"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Skapa anpassade spårnings scheman som övervakar arbets flöden från slut punkt till slut punkt i Azure Logic Apps

Det finns inbyggd spårning som du kan aktivera för olika delar av ditt verksamhets-till-affärs-arbetsflöde, till exempel spårning av AS2-eller X12-meddelanden. När du skapar arbets flöden som innehåller en Logic app, BizTalk Server, SQL Server eller något annat lager, kan du Aktivera anpassad spårning som loggar händelser från början till slutet av arbets flödet. 

Den här artikeln innehåller anpassad kod som du kan använda i lager utanför din Logic app. 

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

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| sourceType | Ja |   | Typ av körnings källa. Tillåtna värden är **Microsoft. Logic/arbetsflöden** och **Custom**. |
| källa | Ja |   | Om käll typen är **Microsoft. Logic/-arbetsflöden**måste käll informationen följa det här schemat. Om käll typen är **anpassad**är schemat ett JToken. |
| systemId | Ja | Sträng | Logic app system-ID. |
| RunId | Ja | Sträng | Körnings-ID för Logic app. |
| operationName | Ja | Sträng | Åtgärdens namn (till exempel åtgärd eller utlösare). |
| repeatItemScopeName | Ja | Sträng | Upprepa objekt namn om åtgärden finns i en `foreach`/`until` slinga. |
| repeatItemIndex | Ja | Integer | Om åtgärden finns i en `foreach`/`until` slinga. Anger det upprepande objekt indexet. |
| trackingId | Nej | Sträng | Spårnings-ID, för att korrelera meddelandena. |
| correlationId | Nej | Sträng | Korrelations-ID för att korrelera meddelandena. |
| clientRequestId | Nej | Sträng | Klienten kan fylla i den för att korrelera meddelanden. |
| eventLevel | Ja |   | Händelsens nivå. |
| eventTime | Ja |   | Tid för händelsen, i UTC-format ÅÅÅÅ-MM-DDTHH: MM: SS. 00000Z. |
| RecordType | Ja |   | Typ av spårnings post. Det tillåtna värdet är **anpassat**. |
| konkurrentpost | Ja |   | Anpassad posttyp. Det tillåtna formatet är JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om att [övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md)
* Lär dig mer om att [spåra B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
