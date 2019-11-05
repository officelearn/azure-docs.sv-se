---
title: Azure SQL Database-Azure Hybrid-förmån | Microsoft Docs
description: Använd befintliga SQL Server licenser för SQL Database rabatter.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: cb5caf65a3083cfb5700432573cfddcfb7c6385f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515894"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den etablerade beräknings nivån för den vCore-baserade inköps modellen kan du byta ut dina befintliga licenser för rabatterade priser på SQL Database genom att använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du Spara upp till 30 procent på Azure SQL Database genom att använda dina lokala SQL Server licenser med Software Assurance.

![Priset](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>Välj en licens modell

Med Azure Hybrid-förmån kan du välja att bara betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL Database-motorn (bas beräknings priser) eller så kan du betala för både den underliggande infrastrukturen och SQL Server licens (licens pris ingår).

Du kan välja eller ändra licensierings modellen med hjälp av Azure Portal eller genom att använda någon av följande API: er:

- Ange eller uppdatera licens typen med hjälp av PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Ange eller uppdatera licens typen med hjälp av Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi-uppdatering](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Ange eller uppdatera licens typen genom att använda REST API:

  - [Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databaser – uppdatera](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Hanterade instanser – uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>Nästa steg

- För att välja mellan SQL Database distributions alternativ, se [välja rätt distributions alternativ i Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- En jämförelse av SQL Database funktioner finns i [Azure SQL Database funktioner](sql-database-features.md).
