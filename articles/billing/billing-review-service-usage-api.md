---
title: Granska Azure-tjänsten Resursanvändning med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska Resursanvändning i Azure-tjänsten.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 2af87c87916dd272026a3bd7e1438507c655053b
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617001"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Granska Azure-Resursanvändning med hjälp av REST-API

Azure Cost Management API: er hjälper dig att granska och hantera användningen av dina Azure-resurser.

I den här artikeln får du lära dig hur du skapar en daglig rapport som genererar ett CSV-dokument med din per timme användningsinformation och hur du använder filter för att anpassa rapporten så att du kan fråga användningen av virtuella datorer, databaser, och märks resurser i en Azure-resursgrupp.

>[!NOTE]
> Cost Management-API är för tillfället i privat förhandsversion.

## <a name="create-a-basic-cost-management-report"></a>Skapa en grundläggande cost management-rapport

Använd den `reports` åtgärden i Cost Management-API att definiera hur kostnaden reporting genereras och där rapporterna kommer att publiceras till.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Den `{subscriptionGuid}` parametern krävs och bör innehålla ett prenumerations-ID som kan läsas med hjälp av autentiseringsuppgifter provieed i API-token. den `{reportName}`

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*| Krävs. Ange `application/json`. |  
|*Auktorisering:*| Krävs. Ange att ett giltigt `Bearer` token. |

Konfigurera parametrarna för rapporten i HTTP-begärandetexten. I exemplet nedan anges rapporten att generera varje dag när aktiv, är en CSV-fil som skrivs till en Azure Storage blob-behållare och per timme innehåller kostnadsinformation för alla resurser i resursgruppen `westus`.

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

den

## <a name="filtering-reports"></a>Filtrera rapporter

Den `filter` och `dimensions` avsnittet i begärandetexten när du skapar en rapport kan du koncentrera dig på kostnaderna för specifika resurstyper. Föregående begärandetexten visar hur du filtrera efter alla resurser i en region. 

### <a name="get-all-compute-usage"></a>Få all användning för beräkning

Använd den `ResourceType` dimension att rapportera kostnader för Azure-dator i din prenumeration i alla regioner.

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

### <a name="get-all-database-usage"></a>Hämta alla databasanvändningen

Använd den `ResourceType` dimension till rapporten Azure SQL Database kostnader i din prenumeration i alla regioner.

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

### <a name="report-on-specific-instances"></a>Rapport om specifika instanser

Den `Resource` dimensionen kan du rapportera kostnaderna för specifika resurser.

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

### <a name="changing-timeframes"></a>Ändra tidsramar

Ange den `timeframe` definitionen för att `Custom` att ställa in ett tidsintervall utanför veckan på hittills inbyggda i alternativ för datum och månad.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Nästa steg
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
