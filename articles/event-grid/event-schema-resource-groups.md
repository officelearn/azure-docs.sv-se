---
title: Azure Event Grid resource group Händelseschema
description: Beskriver de egenskaper som har angetts för resursen gruppera händelser med Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 10/12/2018
ms.author: tomfitz
ms.openlocfilehash: ffc9eba251cbf4d9e2542791d90943ecdd1a972a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310580"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid-Händelseschema för resursgrupper

Den här artikeln innehåller egenskaperna och schemat för resursen gruppera händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

Generera samma händelsetyper Azure-prenumerationer och resursgrupper. Händelsetyperna som är relaterade till resursändringar eller åtgärder. Den viktigaste skillnaden är att resursgrupper skickar händelser för resurserna i resursgruppen och Azure-prenumerationer Generera händelser för resurser i prenumerationen.

Resurs-händelser skapas för PUT, PATCH, POST och ta bort som skickas till `management.azure.com`. Hämta operations skapa inte händelser. Åtgärder som skickas till dataplanet (t.ex. `myaccount.blob.core.windows.net`) skapa inte händelser. Åtgärd-händelser ger händelsedata för åtgärder som att visa en lista över nycklar för en resurs.

När du prenumererar på händelser för en resursgrupp får din slutpunkt alla händelser för resursgruppen. Händelser kan omfatta händelse som du vill se, till exempel att uppdatera en virtuell dator, men även händelser som kanske inte är viktiga för dig, till exempel skriver en ny post i distributionshistoriken. Du kan ta emot alla händelser på din slutpunkt och skriva kod som bearbetar händelserna som du vill hantera. Eller så kan du definiera ett filter när du skapar händelseprenumerationen.

För att programmässigt hantera händelser, kan du sortera händelser genom att titta på den `operationName` värde. Till exempel händelse slutpunkten endast bearbeta händelser för åtgärder som är lika med `Microsoft.Compute/virtualMachines/write` eller `Microsoft.Storage/storageAccounts/write`.

Ämne för händelsen är resurs-ID för den resurs som är mål för åtgärden. Om du vill filtrera händelser för en resurs, anger du den resursen-ID: T när du skapar händelseprenumerationen.  Om du vill filtrera efter en resurstyp, använder du ett värde i följande format: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

En lista över exempel på skript och självstudier finns i [Resource group händelsekälla](event-sources.md#resource-groups).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Resursgrupper generera management händelser från Azure Resource Manager, t.ex. när en virtuell dator skapas eller en lagringskontot har tagits bort.

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Utlöses när åtgärden på resursen har avbrutits. |
| Microsoft.Resources.ResourceActionFailure | Utlöses när åtgärden på resursen misslyckas. |
| Microsoft.Resources.ResourceActionSuccess | Utlöses när åtgärden på resursen fungerar. |
| Microsoft.Resources.ResourceDeleteCancel | Utlöses när ta bort åtgärden har avbrutits. Den här händelsen inträffar när en för malldistribution har avbrutits. |
| Microsoft.Resources.ResourceDeleteFailure | Utlöses när ta bort åtgärden misslyckas. |
| Microsoft.Resources.ResourceDeleteSuccess | Utlöses när för att ta bort lyckas. |
| Microsoft.Resources.ResourceWriteCancel | Utlöses när Skapa eller uppdateringsåtgärden har avbrutits. |
| Microsoft.Resources.ResourceWriteFailure | Utlöses när Skapa eller uppdatera åtgärden misslyckas. |
| Microsoft.Resources.ResourceWriteSuccess | Utlöses när Skapa eller uppdateringsåtgärden lyckas. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visas schemat för en **ResourceWriteSuccess** händelse. Samma schema används för **ResourceWriteFailure** och **ResourceWriteCancel** händelser med olika värden för `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

I följande exempel visas schemat för en **ResourceDeleteSuccess** händelse. Samma schema används för **ResourceDeleteFailure** och **ResourceDeleteCancel** händelser med olika värden för `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

I följande exempel visas schemat för en **ResourceActionSuccess** händelse. Samma schema används för **ResourceActionFailure** och **ResourceActionCancel** händelser med olika värden för `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
}]
```

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är inte skrivbar. Event Grid ger det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för resurs-grupp. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Auktorisering | objekt | Den begärda auktoriseringen för åtgärden. |
| anspråk | objekt | Egenskaper för anspråken. Mer information finns i [JWT-specifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | sträng | En Operations-ID för felsökning. |
| httpRequest | objekt | Information om åtgärden. Det här objektet är endast ingår när du uppdaterar en befintlig resurs eller ta bort en resurs. |
| ResourceProvider | sträng | Resource provider för åtgärden. |
| resourceUri | sträng | URI för resursen i åtgärden. |
| operationName | sträng | Åtgärden som utfördes. |
| status | sträng | Status för åtgärden. |
| subscriptionId | sträng | Prenumerations-ID för resursen. |
| TenantId | sträng | Klient-ID för resursen. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
