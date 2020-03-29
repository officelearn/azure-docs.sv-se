---
title: Anpassade spårningsscheman för B2B-meddelanden
description: Skapa anpassade spårningsscheman för att övervaka B2B-meddelanden i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903064"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Skapa anpassade spårningsscheman som övervakar end-to-end-arbetsflöden i Azure Logic A

Azure Logic Apps har inbyggd spårning som du kan aktivera för delar av arbetsflödet. Du kan dock ställa in anpassad spårning som loggar händelser från början till slutet av arbetsflöden, till exempel arbetsflöden som innehåller en logikapp, BizTalk Server, SQL Server eller något annat lager. Den här artikeln innehåller anpassad kod som du kan använda i lagren utanför logikappen.

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
         "repeatItemIndex": ,
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
         "record": {}
      }
   ]
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| sourceType (sourceType) | Ja | String | Typ av körningskälla med `Microsoft.Logic/workflows`dessa tillåtna värden: ,`custom` |
| källa | Ja | Sträng eller JToken | Om källtypen `Microsoft.Logic/workflows`är måste källinformationen följa det här schemat. Om källtypen `custom`är är schemat en JToken. |
| systemId | Ja | String | System-ID för logikapp |
| runId (på) | Ja | String | Kör-ID för logikapp |
| operationName | Ja | String | Åtgärdens namn, till exempel åtgärd eller utlösare |
| repeatItemScopeName | Ja | String | Upprepa objektnamn om åtgärden `foreach`finns `until` i en eller slinga |
| repeatItemIndex | Ja | Integer | Anger att åtgärden finns `foreach` i `until` en eller slinga och är det upprepade artikelindexnumret. |
| trackingId (trackingId) | Inga | String | Spårnings-ID för att korrelera meddelandena |
| correlationId | Inga | String | Korrelations-ID för att korrelera meddelandena |
| klientRequestId | Inga | String | Klienten kan fylla i den här egenskapen för att korrelera meddelanden |
| händelseNivå | Ja | String | Nivå av händelsen |
| Händelsetid | Ja | DateTime | Tid för händelsen i *UTC-format: YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType (posttyp) | Ja | String | Typ av meritlista med endast detta tillåtna värde:`custom` |
| spela in | Ja | JToken (svenska) | Anpassad posttyp med endast JToken-format |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollspårningsscheman

Information om B2B-protokollspårningsscheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)