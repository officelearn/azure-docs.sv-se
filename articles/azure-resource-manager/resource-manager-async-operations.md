---
title: "Asynkrona åtgärder i Azure | Microsoft Docs"
description: "Beskriver hur du spårar asynkrona åtgärder i Azure."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: 9fe3d98cd345aae45722295b6c1b7fc3e9036e95
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="track-asynchronous-azure-operations"></a>Spåra asynkrona åtgärder i Azure
Vissa Azure REST-åtgärder körs asynkront eftersom åtgärden inte går att slutföra snabbt. Det här avsnittet beskriver hur du spåra statusen för asynkrona åtgärder via värden som returneras i svaret.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuskoder för asynkrona åtgärder
En asynkron åtgärd returnerar först en HTTP-statuskod antingen:

* 201 (skapad)
* 202 (accepterad) 

När åtgärden har slutförts, returnerar antingen:

* 200 (OK)
* 204 (inget innehåll) 

Referera till den [REST API-dokumentation](/rest/api/) att se svar för åtgärden som du kör. 

## <a name="monitor-status-of-operation"></a>Övervaka status för åtgärden
De asynkrona REST-åtgärderna returvärden huvud som du använder för att avgöra status för åtgärden. Det finns potentiellt tre värden i huvudet för att granska:

* `Azure-AsyncOperation`-URL för att kontrollera pågående status för åtgärden. Om åtgärden returnerar det här värdet måste du alltid använda den (i stället för platsen) att spåra status för åtgärden.
* `Location`-URL för att avgöra när en åtgärd har slutförts. Använd det här värdet bara när Azure-asynkrona åtgärder inte returneras.
* `Retry-After`-Antalet sekunder som ska förflyta innan kontrollerar statusen för den asynkrona åtgärden.

Men returnerar inte varje asynkron åtgärd dessa värden. Du kan behöva utvärdera huvudvärde Azure-asynkrona åtgärder för en åtgärd och huvudvärde platsen för en annan åtgärd. 

Du kan hämta huvudvärden som du vill hämta alla huvudvärde för en begäran. Till exempel i C# kan du hämta huvudvärde från en `HttpWebResponse` objekt med namnet `response` med följande kod:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-asynkrona åtgärder förfrågan och svar

Om du vill hämta status för den asynkrona åtgärden att skicka en GET-begäran till URL: en i Azure-asynkrona åtgärder huvudets värde.

Brödtexten i svaret från den här åtgärden innehåller information om åtgärden. I följande exempel visas de möjliga värdena som returnerades från igen:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Endast `status` returneras för alla svar. Felobjekt returneras när det har status misslyckad eller annullerad. Alla andra värden är valfritt. därför se det svar du får annorlunda ut än exemplet.

## <a name="provisioningstate-values"></a>provisioningState värden

Åtgärder som att skapa, uppdatera eller ta bort (PUT, korrigering, ta bort) en resurs vanligtvis returnera en `provisioningState` värde. När en åtgärd har slutförts, returneras ett av följande tre värden: 

* Lyckades
* Det gick inte
* Avbrutna

Alla andra värden anger åtgärden körs fortfarande. Resursprovidern kan returnera ett anpassat värde som anger dess tillstånd. Du kan till exempel få **godkända** när begäran har tagits emot och körs.

## <a name="example-requests-and-responses"></a>Exempel och -svar

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starta den virtuella datorn (202 med Azure-asynkrona åtgärder)
Det här exemplet visar hur du bestämma tillståndet hos **starta** åtgärden för virtuella datorer. Den första begäranden är i följande format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Den returnerar statuskod 202. Mellan huvudvärden visas:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

För att kontrollera status för den asynkrona åtgärden som en annan begäran skickades till URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Svarstexten innehåller status för åtgärden:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Distribuera resurser (201 med Azure-asynkrona åtgärder)

Det här exemplet visar hur du bestämma tillståndet hos **distributioner** åtgärden för att distribuera resurser till Azure. Den första begäranden är i följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Den returnerar statuskod 201. Brödtexten i svaret innehåller:

```json
"provisioningState":"Accepted",
```

Mellan huvudvärden visas:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

För att kontrollera status för den asynkrona åtgärden som en annan begäran skickades till URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Svarstexten innehåller status för åtgärden:

```json
{"status":"Running"}
```

När distributionen är klar visas innehåller svaret:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Skapa lagringskonto (202 med plats och försök igen efter)

Det här exemplet visar hur du bestämma tillståndet hos den **skapa** åtgärden för storage-konton. Den första begäranden är i följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Och begärandetexten innehåller egenskaper för lagringskontot:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Den returnerar statuskod 202. Bland huvudvärden Se följande två värden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

När väntar antalet sekunder anges i försök igen efter, kontrollera status för den asynkrona åtgärden genom att skicka en annan begäran till URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Om begäran körs får du en statuskod 202. Om begäran har slutförts, din får en statuskod 200 och brödtexten i svaret innehåller egenskaper för det lagringskonto som har skapats.

## <a name="next-steps"></a>Nästa steg

* Dokumentation om varje REST-åtgärd finns [REST API-dokumentation](/rest/api/).
* Information om hur du hanterar resurser via Hanteraren för filserverresurser REST-API finns [med hjälp av hanteraren för filserverresurser REST API](resource-manager-rest-api.md).
* information om hur du distribuerar mallar via Hanteraren för filserverresurser REST-API finns [distribuera resurser med Resource Manager-mallar och Resource Manager REST API](resource-group-template-deploy-rest.md).