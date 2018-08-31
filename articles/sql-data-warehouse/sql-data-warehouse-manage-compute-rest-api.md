---
title: Pausa, återuppta och skala med vila i Azure SQL Data Warehouse | Microsoft Docs
description: 'Hantera beräkningskraft i SQL Data Warehouse via REST API: er.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8db4d5cb69b65e60cd77d85d743798168bc6d813
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300841"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API: er för Azure SQL Data Warehouse
REST API: er för att hantera beräkning i Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Skala beräkning
Du kan ändra informationslagerenheter med den [skapa eller uppdatera databasen](/rest/api/sql/databases/createorupdate) REST API. I följande exempel anger informationslagerenheter till DW1000 för MySQLDW som finns på servern minserver-databasen. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pausa databearbetning

För att pausa en databas, använda den [pausa databasen](/rest/api/sql/databases/pause) REST API. I följande exempel pausar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Återuppta beräkning

Starta en databas med den [databasen återupptas](/rest/api/sql/databases/resume) REST API. I följande exempel startar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrollera databasens status

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Nästa steg
Mer information finns i [hantera beräkning](sql-data-warehouse-manage-compute-overview.md).

