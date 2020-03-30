---
title: Pausa, återuppta, skala med REST-API:er
description: Hantera beräkningskraft i Azure Synapse Analytics informationslager via REST API:er.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 322f1dfcb709727ddd3a97ea22dbe8243aedca20
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350355"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API:er för Azure SQL Data Warehouse
REST API:er för hantering av beräkning i Azure Synapse Analytics informationslager.

## <a name="scale-compute"></a>Skala beräkning
Om du vill ändra informationslagerenheterna använder du REST-API:et [Skapa eller uppdatera databas.](/rest/api/sql/databases/createorupdate) I följande exempel anges informationslagerenheterna till DW1000 för databasen MySQLDW, som finns på servern MyServer. Servern finns i en Azure-resursgrupp med namnet ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pausa beräkning

Om du vill pausa en databas använder du [REST-API:et för pausdatabasen.](/rest/api/sql/databases/pause) I följande exempel pausas en databas med namnet Database02 som finns på en server med namnet Server01. Servern finns i en Azure-resursgrupp med namnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Återuppta beräkning

Starta en databas genom att använda [REST-API:et för återställningsdatabasen.](/rest/api/sql/databases/resume) I följande exempel startas en databas med namnet Database02 som finns på en server med namnet Server01. Servern finns i en Azure-resursgrupp med namnet ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrollera databastillstånd

> [!NOTE]
> Kontrollera databastillstånd kan returnera ONLINE medan databasen slutför onlinearbetsflödet, vilket resulterar i anslutningsfel. Du kan behöva lägga till en fördröjning på 2 till 3 minuter i programkoden om du använder det här API-anropet för att utlösa anslutningsförsök.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Hämta underhållsschema
Kontrollera underhållsschemat som har angetts för ett informationslager. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ange underhållsschema
Så här anger och uppdaterar du ett underhållsschema på ett befintligt informationslager.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Nästa steg
Mer information finns i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md).

