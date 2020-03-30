---
title: Azure Hybrid-förmån
description: Använd befintliga SQL Server-licenser för SQL Database-rabatter.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945627"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den etablerade beräkningsnivån för den vCore-baserade inköpsmodellen kan du byta ut dina befintliga licenser mot rabatterade priser i SQL Database med hjälp av [Azure Hybrid Benefit för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du spara upp till 30 procent eller ännu högre i Azure SQL Database genom att använda dina lokala SQL Server-licenser med Software Assurance. Använd Azure Hybrid Benefit Calculator med hjälp av länken som nämnts tidigare för korrekta värden. 

> [!NOTE]
> Om du går till Azure Hybrid Benefit krävs inga driftstopp.

![Prissättning](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Välj en licensmodell

Med Azure Hybrid Benefit kan du välja att bara betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL-databasmotorn (base compute-prissättning), eller så kan du betala för både den underliggande infrastrukturen och SQL Server licens (priser som ingår i licensen).

Du kan välja eller ändra din licensieringsmodell med hjälp av Azure-portalen eller genom att använda något av följande API:er:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Så här anger eller uppdaterar du licenstypen med PowerShell:

- [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [Ny-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här anger eller uppdaterar du licenstypen med hjälp av Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi skapa](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi uppdatering](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Så här anger eller uppdaterar du licenstypen med REST API:

- [Databaser - Skapa eller uppdatera](/rest/api/sql/databases/createorupdate)
- [Databaser - Uppdatering](/rest/api/sql/databases/update)
- [Hanterade instanser – skapa eller uppdatera](/rest/api/sql/managedinstances/createorupdate)
- [Hanterade instanser – uppdatering](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Nästa steg

- Information om hur du väljer mellan distributionsalternativen för SQL-databasen finns [i Välja rätt distributionsalternativ i Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- En jämförelse av SQL Database-funktioner finns i [Azure SQL Database Features](sql-database-features.md).
