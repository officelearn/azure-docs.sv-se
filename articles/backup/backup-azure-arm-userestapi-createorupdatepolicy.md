---
title: 'Azure Backup: Skapa principer för säkerhetskopiering med REST API'
description: Hantera principer för säkerhetskopiering (schema och kvarhållning) med hjälp av REST API
services: backup
author: pvrk
manager: shivamg
keywords: 'REST API: ET Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;'
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648813"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Skapa Azure Recovery Services-säkerhetskopieringsprinciper via REST API

Stegen för att skapa en princip för säkerhetskopiering för ett Azure Recovery Services-valv beskrivs i den [REST API principdokument](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Använd berätta det här dokumentet som referens för att skapa en princip för säkerhetskopiering av Azure virtuella datorer.

## <a name="backup-policy-essentials"></a>Princip för säkerhetskopiering essentials

- En princip för säkerhetskopiering skapas per valv.
- En princip för säkerhetskopiering kan skapas för säkerhetskopiering av följande arbetsbelastningar
  - Azure VM
  - SQL i Azure VM
  - Azure-filresurs
- En princip kan tilldelas till många resurser. En princip för säkerhetskopiering av virtuella Azure-datorer kan användas för att skydda många virtuella datorer i Azure.
- En princip som består av två komponenter
  - Schema: När du göra en säkerhetskopia
  - Kvarhållning av säkerhetskopior: Hur länge ska varje säkerhetskopiering behållas.
- Schemat kan du definiera ”varje dag” eller ”varje vecka” med en specifik tidpunkt.
- Kvarhållning kan definieras för ”daglig”, ”vecka”, ”månad”, ”år” säkerhetskopieringspunkter.
- ”varje vecka” refererar till en säkerhetskopia på en viss dag i veckan, ”månatliga” innebär att en säkerhetskopia på en viss dag i månaden och ”år” refererar till en säkerhetskopia för en viss dag på året.
- Kvarhållning för ”månatliga”, ”år” säkerhetskopieringspunkter kallas för ”LongTermRetention”.
- När ett valv skapas, skapas också en princip för Virtuella Azure-säkerhetskopieringar som kallas ”DefaultPolicy” och kan användas för att säkerhetskopiera virtuella Azure-datorer.

Använd följande för att skapa eller uppdatera en Azure Backup-principen, *PLACERA* åtgärden

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

Den `{policyName}` och `{vaultName}` finns i URI: N. Mer information finns i begärandetexten.

## <a name="create-the-request-body"></a>Skapa begärandetexten

Om du vill skapa en princip för säkerhetskopiering av Azure virtuella datorer är till exempel följande komponenter i begärandetexten.

|Namn  |Obligatoriskt  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource egenskaper        |
|tags     |         | Object        |  Resurstaggar       |

Den fullständiga listan med definitioner i begärandetexten finns i den [säkerhetskopieringsprincip REST API-dokumentet](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Exempel-begärandetexten

Följande begäran definierar en princip för säkerhetskopiering för Virtuella Azure-säkerhetskopieringar.

Principen säger:

- Ta en veckovis säkerhetskopiering varje måndag, onsdag, torsdag klockan 10:00 Pacific Standard Time.
- Bevara säkerhetskopior som gjorts varje måndag, onsdag, torsdag för en vecka.
- Bevara säkerhetskopior som har gjorts i varje första onsdag och tredje torsdag för en månad i två månader (åsidosättningar föregående kvarhållning villkor, om sådana).
- Bevara säkerhetskopior som har gjorts på fjärde måndag och fjärde torsdagen i februari och November i fyra år (åsidosättningar föregående kvarhållning villkor, om sådana).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Tidsformat för schema och kvarhållning stöder endast DateTime. De stöder inte fristående tidsformat.

## <a name="responses"></a>Svar

Princip för säkerhetskopiering skapande/uppdatering är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Två svar returneras: 202 (accepterad) när en annan åtgärd har skapats och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [Skydd PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  Ok       |
|202-accepterad     |         |     Accepterad    |

### <a name="example-responses"></a>Exempelsvar

När du skickar in den *PLACERA* begäran för att skapa eller uppdatera, Rapid response är 202 (accepterad) med en platsrubrik eller Azure-async-rubrik.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

Spåra resulterande åtgärden med en enkel platsrubrik eller Azure-AsyncOperation rubrik *hämta* kommando.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

När åtgärden har slutförts, returnerar 200 (OK) med innehåll för principen i svarstexten.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Om en princip är redan används för att skydda ett objekt, någon uppdatering i principen kommer att leda [ändrar skyddet](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) för alla sådana associerade objekt.

## <a name="next-steps"></a>Nästa steg

[Aktivera skydd för en oskyddad Azure VM](backup-azure-arm-userestapi-backupazurevms.md).

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-provider REST API](/rest/api/recoveryservices/)
- [Kom igång med Azure REST API](/rest/api/azure/)