---
title: Status för asynkrona åtgärder – Azure Resource Manager
description: Beskriver hur du spårar asynkrona åtgärder i Azure. Den visar de värden som du använder för att hämta status för en långvarig åtgärd.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1b05ed50f08ddbf2eb5da8e08f5bf623596e1f9b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135465"
---
# <a name="track-asynchronous-azure-operations"></a>Spåra asynkrona åtgärder i Azure
Vissa Azure REST-åtgärder körs asynkront eftersom åtgärden inte kan slutföras snabbt. Den här artikeln beskriver hur du spårar statusen för asynkrona åtgärder via värden som returneras i svaret.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuskoder för asynkrona åtgärder
En asynkron åtgärd returnerar inledningsvis HTTP-statuskoden antingen:

* 201 (skapad)
* 202 (accepterad) 

När åtgärden har slutförts returnerar antingen:

* 200 (OK)
* 204 (inget innehåll) 

Referera till den [REST API-dokumentation](/rest/api/) att se svar för åtgärden som du.

## <a name="monitor-status-of-operation"></a>Övervakarstatus för åtgärd
Asynkrona REST-åtgärder returnerar värden i huvudet, som du använder för att bestämma status för åtgärden. Det finns potentiellt tre värden i huvudet att undersöka:

* `Azure-AsyncOperation` -URL för att kontrollera den pågående statusen för åtgärden. Om åtgärden returnerar det här värdet ska du alltid Använd den (i stället plats) att spåra status för åtgärden.
* `Location` -URL för att fastställa när en åtgärd har slutförts. Använd det här värdet bara när Azure-AsyncOperation inte returneras.
* `Retry-After` -Antalet sekunder som ska förflyta innan kontrollerar statusen för den asynkrona åtgärden.

Inte alla asynkrona åtgärden returnerar dock alla dessa värden. Du kan behöva utvärdera Azure-AsyncOperation huvudets värde för en åtgärd och platsrubriksvärde för en annan åtgärd. 

Du kan hämta värdena i huvudet som du vill hämta alla huvudets värde för en begäran. Till exempel i C# kan du hämta huvudvärde från en `HttpWebResponse` objekt med namnet `response` med följande kod:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation begäranden och svar

Om du vill hämta status för den asynkrona åtgärden Skicka en GET-begäran till URL: en i Azure-AsyncOperation huvudets värde.

Brödtexten i svaret från den här åtgärden innehåller information om åtgärden. I följande exempel visas de möjliga värdena som returneras från åtgärden:

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

Endast `status` returneras för alla svar. Felobjekt returneras när statusen är misslyckad eller avbruten. Alla andra värden är valfritt. Därför svaret felmeddelandet kan se annorlunda ut än i exemplet.

## <a name="provisioningstate-values"></a>provisioningState värden

Åtgärder som att skapa, uppdatera eller ta bort (PUT, PATCH, DELETE) en resurs returnerar vanligtvis en `provisioningState` värde. När en åtgärd har slutförts returneras ett av följande tre värden: 

* Lyckades
* Misslyckad
* Avbrutna

Alla andra värden anger åtgärden körs fortfarande. Resursprovidern kan returnera ett anpassat värde som anger dess tillstånd. Du kan till exempel få **godkända** när begäran är mottagna och körs.

## <a name="example-requests-and-responses"></a>Exempel begäranden och svar

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starta virtuell dator (202 med Azure-AsyncOperation)
Det här exemplet visar hur du bestämma tillståndet hos **starta** åtgärden för virtuella datorer. Den första begäran är i följande format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Returnerar den statuskod 202. Mellan huvudvärden visas:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

För att kontrollera status för den asynkrona åtgärden, skicka en annan begäran till URL: en.

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

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Distribuera resurser (201 med Azure-AsyncOperation)

Det här exemplet visar hur du bestämma tillståndet hos **distributioner** åtgärden för att distribuera resurser till Azure. Den första begäran är i följande format:

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

För att kontrollera status för den asynkrona åtgärden, skicka en annan begäran till URL: en.

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

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Skapa lagringskonto (202 med platsen och försök igen efter)

Det här exemplet visar hur du bestämma tillståndet hos den **skapa** åtgärden för storage-konton. Den första begäran är i följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Och begärandetexten innehåller egenskaper för lagringskontot:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Returnerar den statuskod 202. Mellan huvudvärden kan du se följande två värden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Efter att vänta tills antalet sekunder anges i Retry-After Kontrollera status för den asynkrona åtgärden genom att skicka en annan begäran till URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Om begäran körs, får du en statuskod 202. Om begäran har slutförts, den får en statuskod 200 och texten i svaret innehåller egenskaper för det lagringskonto som har skapats.

## <a name="next-steps"></a>Nästa steg

* Dokumentation om varje REST-åtgärd i [REST API-dokumentation](/rest/api/).
* information om hur du distribuerar mallar via Resource Manager REST API finns i [distribuera resurser med Resource Manager-mallar och Resource Manager REST API](resource-group-template-deploy-rest.md).