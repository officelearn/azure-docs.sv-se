---
title: "Azure händelse rutnätet resurs grupp Händelseschema"
description: "Beskriver de egenskaper som har angetts för resursen gruppera händelser med Azure händelse rutnätet"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: c7435d87f9aaa906c3f6758186b64f3458cb9716
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure händelse rutnätet Händelseschema för resursgrupper

Den här artikeln innehåller egenskaperna och schemat för resursen gruppera händelser. En introduktion till händelse scheman, se [Azure händelse rutnätet Händelseschema](event-schema.md).

Generera samma händelsetyper Azure-prenumerationer och resursgrupper. Händelsetyperna som är relaterade till ändringar i resurser. Den viktigaste skillnaden är att resursgrupper genererar händelser för resurser i resursgruppen och Azure-prenumerationer genererar händelser för resurser i prenumerationen. 

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Resursgrupper genererar management händelser från Azure Resource Manager, t.ex. när en virtuell dator skapas eller en lagringskontot tas bort.

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Utlöses lyckas när en resurs skapa eller uppdatera åtgärden. |
| Microsoft.Resources.ResourceWriteFailure | Utlöses när en resurs skapa eller uppdateringsåtgärden misslyckas. |
| Microsoft.Resources.ResourceWriteCancel | Utlöses har när en resurs skapa eller uppdatera åtgärden avbrutits. |
| Microsoft.Resources.ResourceDeleteSuccess | Utlöses när en resurs borttagningsåtgärd lyckas. |
| Microsoft.Resources.ResourceDeleteFailure | Utlöses när en resurs borttagningsåtgärd misslyckas. |
| Microsoft.Resources.ResourceDeleteCancel | Utlöses när en resurs delete-åtgärden har avbrutits. Den här händelsen inträffar när en för malldistribution har avbrutits. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visas schemat för en resurs som skapas händelse: 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

Schemat för en resurs tas bort händelse är ungefär:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Avsnittet | Sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. |
| Ämne | Sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | Sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | Sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | Sträng | Unik identifierare för händelsen. |
| Data | Objektet | Händelsedata för resurs-grupp. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Auktorisering | Sträng | Det begärda tillståndet för åtgärden. |
| Anspråk | Sträng | Egenskaperna för anspråk. |
| correlationId | Sträng | En Operations-ID för felsökning. |
| httpRequest | Sträng | Information om åtgärden. |
| resourceProvider | Sträng | Resursprovidern utför åtgärden. |
| resourceUri | Sträng | URI för resursen i åtgärden. |
| operationName | Sträng | Åtgärden som utfördes. |
| status | Sträng | Status för åtgärden. |
| subscriptionId | Sträng | Prenumerations-ID för resursen. |
| Klient-ID | Sträng | Klient-ID för resursen. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
