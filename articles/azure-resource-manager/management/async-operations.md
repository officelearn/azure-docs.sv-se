---
title: Status för asynkrona åtgärder
description: Beskriver hur du spårar asynkrona åtgärder i Azure. Den visar de värden du använder för att få status för en tidskrävande åtgärd.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485447"
---
# <a name="track-asynchronous-azure-operations"></a>Spåra asynkrona Azure-åtgärder
Vissa Azure REST-åtgärder körs asynkront eftersom åtgärden inte kan slutföras snabbt. I den här artikeln beskrivs hur du spårar status för asynkrona åtgärder genom värden som returneras i svaret.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuskoder för asynkrona åtgärder
En asynkron åtgärd returnerar inledningsvis en HTTP-statuskod för antingen:

* 201 (Skapad)
* 202 (Accepterad) 

När åtgärden har slutförts returneras antingen följande:

* 200 (OK)
* 204 (Inget innehåll) 

Se [REST API-dokumentationen](/rest/api/) för att se svaren för åtgärden du kör.

## <a name="monitor-status-of-operation"></a>Övervaka åtgärdens status
De asynkrona REST-operationerna returnerar huvudets värden, som du använder för att fastställa åtgärdens status. Det finns potentiellt tre rubrikvärden att undersöka:

* `Azure-AsyncOperation`- URL för att kontrollera åtgärdens pågående status. Om åtgärden returnerar det här värdet använder du det alltid (i stället för Plats) för att spåra åtgärdens status.
* `Location`- URL för att avgöra när en åtgärd har slutförts. Använd det här värdet endast när Azure-AsyncOperation inte returneras.
* `Retry-After`- Antalet sekunder att vänta innan du kontrollerar status för den asynkrona åtgärden.

Alla asynkrona åtgärder returnerar dock inte alla dessa värden. Du kan till exempel behöva utvärdera huvudvärdet för Azure-AsyncOperation för en åtgärd och värdet för platshuvudet för en annan åtgärd. 

Du hämtar rubrikvärdena på samma sätt som du hämtar alla huvudvärden för en begäran. I C#hämtar du till exempel rubrikvärdet `HttpWebResponse` från `response` ett objekt med namnet med följande kod:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Begäran och svar på Azure-AsyncOperation-begäran och svar

Om du vill hämta status för den asynkrona åtgärden skickar du en GET-begäran till URL:en i Azure-AsyncOperation-huvudvärdet.

Brödtexten för svaret från den här åtgärden innehåller information om åtgärden. I följande exempel visas möjliga värden som returnerats från åtgärden:

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

Endast `status` returneras för alla svar. Felobjektet returneras när statusen har misslyckats eller Avbryts. Alla andra värden är valfria. Därför kan svaret du får se annorlunda ut än exemplet.

## <a name="provisioningstate-values"></a>etableringStatsvärden

Åtgärder som skapar, uppdaterar eller tar bort (PUT, PATCH, DELETE) returnerar en resurs vanligtvis ett `provisioningState` värde. När en operation har slutförts returneras ett av följande tre värden: 

* Lyckades
* Misslyckades
* Avbrutna

Alla andra värden anger att åtgärden fortfarande körs. Resursleverantören kan returnera ett anpassat värde som anger dess tillstånd. Du kan till exempel få **accepterad** när begäran tas emot och körs.

## <a name="example-requests-and-responses"></a>Exempel på förfrågningar och svar

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starta virtuell dator (202 med Azure-AsyncOperation)
Det här exemplet visar hur du avgör status för **startåtgärd** för virtuella datorer. Den första begäran är i följande format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Statuskoden 202 returneras. Bland rubrikvärdena ser du:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Om du vill kontrollera status för den asynkrona åtgärden skickar du en annan begäran till den URL:en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Svarstexten innehåller åtgärdens status:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Distribuera resurser (201 med Azure-AsyncOperation)

Det här exemplet visar hur du avgör status för **distributioner** för distribution av resurser till Azure. Den första begäran är i följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Den returnerar statuskoden 201. I svarets brödtext ingår:

```json
"provisioningState":"Accepted",
```

Bland rubrikvärdena ser du:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Om du vill kontrollera status för den asynkrona åtgärden skickar du en annan begäran till den URL:en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Svarstexten innehåller åtgärdens status:

```json
{"status":"Running"}
```

När distributionen är klar innehåller svaret:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Skapa lagringskonto (202 med plats och försök igen)

I det här exemplet visas hur du bestämmer status för **åtgärden skapa** för lagringskonton. Den första begäran är i följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Och begärandetexten innehåller egenskaper för lagringskontot:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Statuskoden 202 returneras. Bland rubrikvärdena visas följande två värden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

När du har väntat på ett antal sekunder som anges i Försök igen kontrollerar du statusen för den asynkrona åtgärden genom att skicka en annan begäran till den URL:en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Om begäran fortfarande körs får du en statuskod 202. Om begäran har slutförts får du en statuskod 200 och brödtexten i svaret innehåller egenskaperna för lagringskontot som har skapats.

## <a name="next-steps"></a>Nästa steg

* Dokumentation om varje REST-åtgärd finns i [REST API-dokumentation](/rest/api/).
* Information om hur du distribuerar mallar via RESURSHANTERARENS REST API finns i [Distribuera resurser med Resource Manager-mallar och RESURSHANTERARENS REST API](../templates/deploy-rest.md).