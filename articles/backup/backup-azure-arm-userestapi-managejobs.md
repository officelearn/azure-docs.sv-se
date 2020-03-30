---
title: Hantera säkerhetskopieringsjobb med REST API
description: I den här artikeln kan du läsa om hur du spårar och hanterar säkerhetskopiering och återställning av jobb för Azure Backup med REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: 628569c547aa776ec2fbb7ec7e32edad7c1fe7dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273532"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Spåra säkerhetskopierings- och återställningsjobb med REST API

Azure Backup-tjänsten utlöser jobb som körs i bakgrunden i olika scenarier, till exempel utlösa säkerhetskopiering, återställningsåtgärder, inaktivera säkerhetskopiering. Dessa jobb kan spåras med hjälp av deras ID.

## <a name="fetch-job-information-from-operations"></a>Hämta jobbinformation från åtgärder

En åtgärd som utlöser säkerhetskopiering returnerar alltid ett jobID. Till exempel: Det slutliga svaret för en [återställningsåtgärd för säkerhetskopiering](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) av säkerhetskopiering är följande:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM-säkerhetskopieringsjobbet identifieras av fältet "jobId" och kan spåras som nämnts [här](https://docs.microsoft.com/rest/api/backup/jobdetails/) med en enkel *GET-begäran.*

## <a name="tracking-the-job"></a>Spåra jobbet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

Den `{jobName}` är "jobId" som nämns ovan. Svaret är alltid 200 OK med fältet "status" som anger jobbets aktuella status. När det är "Completed" eller "CompletedWithWarnings" visar avsnittet extendedInfo mer information om jobbet.

### <a name="response"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [JobbKälla](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Exempelsvar

När *GET* URI har skickats returneras ett 200 (OK) svar.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
