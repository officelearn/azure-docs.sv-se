---
title: Skapa principer för säkerhetskopiering med REST API
description: I den här artikeln får du lära dig hur du skapar och hanterar principer för säkerhetskopiering (schema och kvarhållning) med REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963860"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Skapa principer för säkerhetskopiering av Azure Recovery Services med REST API

Stegen för att skapa en princip för säkerhetskopiering för ett Azure Recovery Services-valv beskrivs i [princip-REST API-dokumentet](/rest/api/backup/protectionpolicies/createorupdate). Låt oss använda det här dokumentet som en referens för att skapa en princip för azure vm-säkerhetskopiering.

## <a name="create-or-update-a-policy"></a>Skapa eller uppdatera en princip

Om du vill skapa eller uppdatera en Azure Backup-princip använder du följande *PUT-åtgärd*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

Den `{policyName}` `{vaultName}` och tillhandahålls i URI. Ytterligare information finns i begärandeorganet.

## <a name="create-the-request-body"></a>Skapa förfrådetexten

Om du till exempel vill skapa en princip för azure VM-säkerhetskopiering följer följande komponenter i begärandetexten.

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Egenskaper för ProtectionPolicyResource        |
|tags     |         | Objekt        |  Resurstaggar       |

Den fullständiga listan över definitioner i begärandetexten finns i [REST API-dokumentet för reservprincipen](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Exempel på begäran

Följande begärandetext definierar en säkerhetskopieringsprincip för Azure VM-säkerhetskopior.

Policyn säger:

- Ta en vecka backup varje måndag, onsdag, torsdag kl 10:00 Pacific Standard Time.
- Behåll säkerhetskopiorna som tas på varje måndag, onsdag, torsdag i en vecka.
- Behåll de säkerhetskopior som tas på var första onsdag och tredje torsdag i en månad i två månader (åsidosätter tidigare kvarhållningsvillkor, om sådana finns).
- Behåll de säkerhetskopior som tas på fjärde måndag och fjärde torsdag i februari och november i fyra år (åsidosätter tidigare lagringsvillkor, om någon).

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
> Tidsformaten för schema och kvarhållning stöder endast DateTime. De stöder inte tidsformat ensam.

## <a name="responses"></a>Svar

Skapandet/uppdateringen av säkerhetskopieringsprincipen är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [SkyddspolicyResurser](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Accepterad     |         |     Accepterad    |

### <a name="example-responses"></a>Exempel på svar

När du har skickat *put-begäran* om att skapa eller uppdatera principen är det första svaret 202 (accepterad) med ett platshuvud eller Azure-async-header.

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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med ett enkelt *GET-kommando.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

När åtgärden är klar returneras 200 (OK) med principinnehållet i svarstexten.

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

Om en princip redan används för att skydda ett objekt, kommer alla uppdateringar i principen att resultera i [att ändra skyddet](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) för alla sådana associerade objekt.

## <a name="next-steps"></a>Nästa steg

[Aktivera skydd för en oskyddad Azure VM](backup-azure-arm-userestapi-backupazurevms.md).

Mer information om AZURE Backup REST API:er finns i följande dokument:

- [REST-API för Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)
