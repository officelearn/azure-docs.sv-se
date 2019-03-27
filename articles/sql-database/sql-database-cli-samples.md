---
title: Azure CLI-exempelskript för SQL Database | Microsoft Docs
description: Azure CLI-exempelskript för att skapa och hantera Azure SQL Database-servrar, elastiska pooler, databaser och brandväggar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: e3a75acb6d591a9fdf1e632369cc2e9ae6b6099d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447842"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-exempel för Azure SQL Database

Azure SQL Database kan konfigureras med <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-pools"></a>Enkel databas och elastiska pooler

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure SQL Database.

| |  |
|---|---|
|**Skapa en databas och en elastisk pool**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Det här CLI-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. |
| [Skapa elastiska pooler och flytta databaser i pooler](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Det här CLI-exempelskriptet skapar elastiska SQL-pooler, flyttar Azure SQL-databaser i poolerna och ändrar beräkningsstorlekar.|
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Det här CLI-exempelskriptet skalar en enda Azure SQL-databas till en annan beräkningsstorlek efter att ha kört en fråga om databasens storlek. |
| [Skala en elastisk pool](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Det här CLI-exempelskriptet skalar en elastisk SQL-pool till en annan beräkningsstorlek.  |
|||

Läs mer om [Azure CLI API för enkel databas](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Managed Instance

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure SQL Database – Managed Instance.

| |  |
|---|---|
| [Skapa en hanterad instans](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Det här CLI-skriptet visar hur du skapar en hanterad instans. |
| [Uppdatera en hanterad instans](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Det här CLI-skriptet visar hur du uppdaterar en hanterad instans. |
| [Flytta en databas till en annan hanterad instans](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Det här CLI-skriptet visar hur du återställer en säkerhetskopia av en databas från en instans till en annan. |
|||

Läs mer om [Azure CLI API för hanterad instans](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) och hitta [ytterligare exempel här](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
