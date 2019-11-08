---
title: 'Azure Backup: hantera säkerhets kopierings jobb med REST API'
description: I den här artikeln lär du dig att spåra och hantera säkerhets kopierings-och återställnings jobb för Azure Backup med hjälp av REST API.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure VM-säkerhetskopiering; Återställning av Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: dacurwin
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: c0ce79c68b9d9cf11ea20c2d6469f4240fb38a95
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747539"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Spåra säkerhets kopierings-och återställnings jobb med REST API

Azure Backup tjänst utlöser jobb som körs i bakgrunden i olika scenarier, till exempel utlöser säkerhets kopiering, återställnings åtgärder, inaktiverar säkerhets kopiering. Dessa jobb kan spåras med hjälp av deras ID.

## <a name="fetch-job-information-from-operations"></a>Hämta jobb information från åtgärder

En åtgärd som utlöser säkerhets kopiering returnerar alltid en jobID. Exempel: det slutliga svaret från en [Utlös ande säkerhets kopiering REST API åtgärd](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) är följande:

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

Jobbet för säkerhets kopiering av virtuella Azure-datorer identifieras av "jobId"-fältet och kan spåras på det sätt som anges [här](https://docs.microsoft.com/rest/api/backup/jobdetails/) med en enkel *Get* -begäran.

## <a name="tracking-the-job"></a>Spåra jobbet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

`{jobName}` är "jobId" som nämnts ovan. Svaret är alltid 200 OK med fältet status och indikerar jobbets aktuella status. När det är "slutfört" eller "CompletedWithWarnings" visar avsnittet "extendedInfo" Mer information om jobbet.

### <a name="response"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Exempelsvar

När *hämtnings* -URI: n har skickats returneras ett 200-svar (OK).

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
