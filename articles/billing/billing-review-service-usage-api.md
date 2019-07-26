---
title: Granska användningen av Azure Service-resurser med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska användningen av Azure-tjänst resurser.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443067"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Granska användningen av Azure-resurser med hjälp av REST API

Azure Cost Management-API: er hjälper dig att granska och hantera användningen av dina Azure-resurser.

I den här artikeln får du lära dig hur du skapar en daglig rapport som genererar ett kommaavgränsat värde dokument med din användnings information per timme och sedan hur du använder filter för att anpassa rapporten så att du kan fråga användningen av virtuella datorer, databaser och taggade resurser i en Azure-resurs grupp.

>[!NOTE]
> Cost Management API finns för närvarande i privat för hands version.

## <a name="create-a-basic-cost-management-report"></a>Skapa en grundläggande kostnads hanterings rapport

`reports` Använd åtgärden i Cost Management API för att definiera hur kostnads rapportering skapas och var rapporterna ska publiceras.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` Parametern är obligatorisk och måste innehålla ett prenumerations-ID som kan läsas med de autentiseringsuppgifter som anges i API-token. Det`{reportName}`

Följande rubriker krävs: 

|Begär ande huvud|Beskrivning|  
|--------------------|-----------------|  
|*Innehålls typ:*| Obligatoriskt. Ange till `application/json`. |  
|*Authorization:*| Obligatoriskt. Ange en giltig `Bearer` token. |

Konfigurera rapportens parametrar i HTTP-begärans brödtext. I exemplet nedan är rapporten inställd på att genereras varje dag när aktiv, är en CSV-fil som skrivs till en Azure Storage BLOB-behållare och innehåller Tim kostnads information för alla resurser i resurs gruppen `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Filtypen

## <a name="filtering-reports"></a>Filtrera rapporter

I `filter` avsnittet `dimensions` och i förfrågnings texten när du skapar en rapport kan du fokusera på kostnaderna för vissa resurs typer. Föregående begär ande text visar hur du filtrerar efter alla resurser i en region. 

### <a name="get-all-compute-usage"></a>Hämta all beräknings användning

`ResourceType` Använd dimensionen för att rapportera kostnader för virtuella Azure-datorer i din prenumeration i alla regioner.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Hämta all databas användning

`ResourceType` Använd dimensionen för att rapportera Azure SQL Database kostnader i din prenumeration i alla regioner.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Rapportera om vissa instanser

Med `Resource` dimensionen kan du rapportera kostnader för vissa resurser.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Ändra tidsram

Ange att `timeframe` `Custom` definitionen ska ställas in för att ange en tidsram utanför veckan till datum och månad till datum inbyggda alternativ.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Nästa steg
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
