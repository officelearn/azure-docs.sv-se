---
title: Pausa, återuppta, skala med övriga i Azure SQL Data Warehouse | Microsoft Docs
description: 'Hantera datorkraft i SQL Data Warehouse via REST API: er.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 21423d69bf2cf06bcd208082ce492bf5dd038e29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790669"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API: er för Azure SQL Data Warehouse
REST API: er för att hantera beräkning i Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Skala beräkning
Du kan ändra informationslagerenheter den [skapa eller uppdatera databasen](/rest/api/sql/databases/createorupdate) REST API. I följande exempel anger informationslagerenheter till DW1000 för MySQLDW som finns på servern minserver-databasen. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

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

Pausa en databas genom att använda den [pausa databas](/rest/api/sql/databases/pause) REST API. Följande exempel pausar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Återuppta beräkning

Starta en databas med den [återuppta databasen](/rest/api/sql/databases/resume) REST API. Följande exempel startar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrollera databasens status

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Nästa steg
Mer information finns i [hantera beräkning](sql-data-warehouse-manage-compute-overview.md).

