---
title: Azure Event Grid-prenumerationshändelseschema
description: Beskriver de egenskaper som tillhandahålls för prenumerationshändelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561684"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid-händelseschema för prenumerationer

Den här artikeln innehåller egenskaper och schema för Azure-prenumerationshändelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

Azure-prenumerationer och resursgrupper avger samma händelsetyper. Händelsetyperna är relaterade till resursändringar eller åtgärder. Den primära skillnaden är att resursgrupper avger händelser för resurser inom resursgruppen och Azure-prenumerationer avger händelser för resurser i hela prenumerationen.

Resurshändelser skapas för åtgärder för PUT, PATCH, `management.azure.com`POST och DELETE som skickas till . GET-åtgärder skapar inte händelser. Åtgärder som skickas till `myaccount.blob.core.windows.net`dataplanet (som) skapar inte händelser. Åtgärdshändelserna tillhandahåller händelsedata för åtgärder som att lista nycklarna för en resurs.

När du prenumererar på händelser för en Azure-prenumeration får slutpunkten alla händelser för den prenumerationen. Händelserna kan innehålla händelser som du vill se, till exempel uppdatera en virtuell dator, men även händelser som kanske inte är viktiga för dig, till exempel att skriva en ny post i distributionshistoriken. Du kan ta emot alla händelser vid slutpunkten och skrivkoden som bearbetar de händelser som du vill hantera. Du kan också ange ett filter när du skapar händelseprenumerationen.

Om du vill hantera händelser programmässigt kan `operationName` du sortera händelser genom att titta på värdet. Händelseslutpunkten kan till exempel bara bearbeta händelser för `Microsoft.Compute/virtualMachines/write` `Microsoft.Storage/storageAccounts/write`åtgärder som är lika med eller .

Händelseämnet är resurs-ID för den resurs som är målet för operationen. Om du vill filtrera händelser för en resurs anger du det resurs-ID:et när du skapar händelseprenumerationen. Om du vill filtrera efter en resurstyp använder du ett värde i följande format:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

En lista över exempelskript och självstudier finns i [Azure-prenumerationshändelsekälla](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure-prenumerationer avger hanteringshändelser från Azure Resource Manager, till exempel när en virtuell dator skapas eller ett lagringskonto tas bort.

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Utlöses när åtgärden för resursen avbryts. |
| Microsoft.Resources.ResourceActionFailure | Utlöses när åtgärden för resursen misslyckas. |
| Microsoft.Resources.ResourceActionSuccess | Utlöses när åtgärden för resurs lyckas. |
| Microsoft.Resources.ResourceDeleteCancel | Utlöses när borttagningen avbryts. Den här händelsen inträffar när en malldistribution avbryts. |
| Microsoft.Resources.ResourceDeleteFailure | Utlöses när borttagningen misslyckas. |
| Microsoft.Resources.ResourceDeleteSuccess | Utlöses när borttagningsåtgärden lyckas. |
| Microsoft.Resources.ResourceWriteCancel | Utlöses när åtgärden skapa eller uppdatera avbryts. |
| Microsoft.Resources.ResourceWriteFailure | Utlöses när åtgärden skapa eller uppdatera misslyckas. |
| Microsoft.Resources.ResourceWriteSuccess | Utlöses när åtgärden skapa eller uppdatera lyckas. |

## <a name="example-event"></a>Exempel händelse

I följande exempel visas schemat för en **ResourceWriteSuccess-händelse.** Samma schema används för **ResourceWriteFailure-** och **ResourceWriteCancel-händelser** med olika värden för `eventType`.

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
  "topic": "/subscriptions/{subscription-id}"
}]
```

I följande exempel visas schemat för en **ResourceDeleteSuccess-händelse.** Samma schema används för **ResourceDeleteFailure-** och **ResourceDeleteCancel-händelser** med olika värden för `eventType`.

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
  "topic": "/subscriptions/{subscription-id}"
}]
```

I följande exempel visas schemat för en **ResourceActionSuccess-händelse.** Samma schema används för **ResourceActionFailure-** och **ResourceActionCancel-händelser** med olika värden för `eventType`.

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
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Uppgifter om prenumerationshändelse. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| auktorisering | objekt | Den begärda behörigheten för operationen. |
| Hävdar | objekt | Egenskaperna för fordringarna. Mer information finns i [JWT-specifikationen](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | sträng | Ett åtgärds-ID för felsökning. |
| httpRequest (på) | objekt | Detaljerna i operationen. Det här objektet inkluderas bara när du uppdaterar en befintlig resurs eller tar bort en resurs. |
| resurserProvider | sträng | Resursprovidern för åtgärden. |
| resourceUri (på ett år) | sträng | URI för resursen i åtgärden. |
| operationName | sträng | Operationen som togs. |
| status | sträng | Status för åtgärden. |
| subscriptionId | sträng | Resursens prenumerations-ID. |
| tenantId (hyresgäst) | sträng | Resursens klient-ID. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är händelserutnät?](overview.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
