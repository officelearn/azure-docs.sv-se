---
title: Hantera säkerhetskopiering av Azure-fildelning med Rest API
description: Lär dig hur du använder REST API för att hantera och övervaka Azure-filresurser som backas upp av Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444737"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Hantera säkerhetskopiering av Azure-fildelning med REST API

I den här artikeln beskrivs hur du utför uppgifter för att hantera och övervaka Azure-filresurser som backas upp av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

## <a name="monitor-jobs"></a>Övervaka jobb

Azure Backup-tjänsten utlöser jobb som körs i bakgrunden. Detta inkluderar scenarier som att utlösa säkerhetskopiering, återställa åtgärder och inaktivera säkerhetskopiering. Dessa jobb kan spåras med hjälp av deras ID.

### <a name="fetch-job-information-from-operations"></a>Hämta jobbinformation från operationer

En åtgärd som utlöser säkerhetskopiering returnerar alltid ett jobID i svaret.

Det slutliga svaret för en [REST API-åtgärd](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) för säkerhetskopiering av utlösare är följande:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Säkerhetskopieringsjobbet för Azure-filresurs identifieras av **jobId-fältet** och kan spåras som nämns [här](https://docs.microsoft.com/rest/api/backup/jobdetails/) med hjälp av en GET-begäran.

### <a name="tracking-the-job"></a>Spåra jobbet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{jobName} är "jobId" som nämns ovan. Svaret är alltid "200 OK" med **statusfältet** som anger status för jobbet. När det är "Completed" eller "CompletedWithWarnings" visar **extendedInfo-avsnittet** mer information om jobbet.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Svar

Namn  | Typ  |  Beskrivning
--- | --- | ----
200 OK |  JobbKälla  | OK

#### <a name="response-example"></a>Exempel på svar

När *GET* URI har skickats returneras ett 200-svar.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Ändra princip

Om du vill ändra principen som filresursen är skyddad med kan du använda samma format som att aktivera skydd. Ange bara det nya princip-ID:t i begäraneprincipen och skicka begäran.

Om du till exempel vill ändra skyddsprincipen *för testdelning* från *schema1* till *schema2*anger du *id-programmet schema2* i begärandetexten.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Stoppa skyddet men behålla befintliga data

Du kan ta bort skyddet för en skyddad filresurs men behålla de data som redan säkerhetskopierats. Om du vill göra det tar du bort principen i det begärandeorgan som du använde för att[aktivera säkerhetskopiering](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) och skicka begäran. När kopplingen till principen har tagits bort utlöses inte längre säkerhetskopior och inga nya återställningspunkter skapas.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Exempelsvar

Att stoppa skyddet för en filresurs är en asynkron åtgärd. Åtgärden skapar en annan åtgärd som måste spåras. Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och 200 när åtgärden är klar.

Svarshuvudet när åtgärden har accepterats:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med kommandot GET:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Själva svaret

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Stoppa skydd och ta bort data

Om du vill ta bort skyddet för en skyddad filresurs och även ta bort säkerhetskopierade data utför du en borttagningsåtgärd som beskrivs [här](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Parametrarna {containerName} och {protectedItemName} anges [här](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname).

I följande exempel utlöses en åtgärd *testshare* för att stoppa skyddet för testdelningsfilresursen som skyddas med *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Svar

Ta bort skydd är en asynkron åtgärd. Åtgärden skapar en annan åtgärd som måste spåras separat.
Två svar returneras: 202 (Accepterad) när en annan åtgärd skapas och 204 (NoContent) när åtgärden är klar.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [felsöker problem när du konfigurerar säkerhetskopiering för Azure File-resurser](troubleshoot-azure-files.md).