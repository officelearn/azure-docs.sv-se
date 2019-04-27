---
title: 'Azure Backup: Hantera säkerhetskopieringsjobb via REST-API'
description: Hantera säkerhetskopiering och Återställ jobb av Azure Backup med hjälp av REST API
services: backup
author: pvrk
manager: shivamg
keywords: 'REST API: ET Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;'
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: eb8b7dc77d180eb56c2585e93e60a36742f6c84c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646630"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Spåra säkerhetskopiering och återställning jobb med hjälp av REST API

Azure Backup-tjänsten utlöser jobb som körs i bakgrunden i olika scenarier, till exempel aktiverar säkerhetskopiering, återställning, inaktivera backup. Dessa jobb kan spåras med hjälp av deras ID: N.

## <a name="fetch-job-information-from-operations"></a>Hämta jobbinformation från åtgärder

En åtgärd som att utlösa säkerhetskopieringen returnerar alltid en jobID. För t.ex.: Det slutliga svaret på en [utlösa säkerhetskopieringen till REST API](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) är följande:

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

Säkerhetskopieringsjobbet för virtuella Azure-datorer identifieras med ”jobId”-fältet och kan spåras som vi redan nämnt [här](https://docs.microsoft.com/rest/api/backup/jobdetails/) med hjälp av en enkel *hämta* begäran.

## <a name="tracking-the-job"></a>Spåra jobbet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

Den `{jobName}` är ”jobId” som nämns ovan. Svaret är alltid 200 OK med fältet ”status” som indikerar den aktuella statusen för jobbet. När det gäller ”slutfört” eller ”CompletedWithWarnings”, visar ”extendedInfo”-avsnittet Mer information om jobbet.

### <a name="response"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | Ok        |

#### <a name="example-response"></a>Exempelsvar

När den *hämta* URI skickas, returneras ett svar med 200 (OK).

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