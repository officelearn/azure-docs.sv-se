---
title: Azure PowerShell-exempelskript för SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript för att skapa och hantera Azure SQL Database-servrar, elastiska pooler, databaser och brandväggar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 77e7ed6b39f18f05323562865097dd0760240abf
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446372"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-exempel för Azure SQL Database

Med Azure SQL Database kan du konfigurera dina databaser, instanser och pooler med hjälp av Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="single-database-and-elastic-pools"></a>Enkel databas och elastiska pooler

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure SQL Database.

| |  |
|---|---|
|**Skapa och konfigurera enkla databaser och elastiska pooler**||
| [Skapa en databas och konfigurera en brandväggsregel för databasserver](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. |
| [Skapa elastiska pooler och flytta databaser i pooler](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-exempelskriptet skapar elastiska Azure SQL Database-pooler, flyttar databaserna i poolerna och ändrar beräkningsstorlekar.|
|**Konfigurera geo-replikering och redundans**||
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar en aktiv geo-replikering för en enskild Azure SQL-databas och redundansväxlar den till en sekundär replik. |
| [Konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar en aktiv geo-replikering för en enskild Azure SQL-databas i en elastisk SQL-pool och redundansväxlar den till en sekundär replik. |
| [Konfigurera och redundansväxla en redundansgrupp för en enskild databas](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar en redundansgrupp för en Azure SQL Database-serverinstans, lägger till en databas i redundansgruppen och redundansväxlar den till den sekundära servern |
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestandavärden för en Azure SQL-databas, skalar ut den till en större beräkningsstorlek och skapar en varningsregel för ett av prestandamåtten. |
| [Skala en elastisk pool](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestandavärden för en elastisk Azure SQL Database-pool, skalar ut den till en större beräkningsstorlek och skapar en varningsregel för ett av prestandamåtten.  |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar principer för granskning och hotidentifiering för en Azure SQL-databas. |
| **Återställa, kopiera och importera en databas**||
| [Återställa en databas](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet återställer en Azure SQL-databas från en geo-redundant säkerhetskopia och återställer en borttagen Azure SQL-databas till den senaste säkerhetskopian. |
| [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet skapar en kopia av en befintlig Azure SQL-databas på en ny Azure SQL-server. |
| [Importera en databas från en bacpac-fil](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet importerar en databas till en Azure SQL-server från en bacpac-fil. |
| **Synkronisera data mellan databaser**||
| [Synkronisera data mellan SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar Data Sync för att synkronisera mellan flera Azure SQL-databaser. |
| [Synkronisera data mellan SQL Database och SQL Server lokalt](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar Data Sync för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas. |
| [Uppdatera synkroniseringsschemat för SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet lägger till eller tar bort objekt från synkroniseringsschemat för Data Sync. |
|||

Läs mer om [Azure PowerShell API för enkel databas](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Managed Instance

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure SQL Database – hanterad instans.

| |  |
|---|---|
|**Skapa och konfigurera hanterade instanser**||
| [Skapa och hantera en hanterad instans](scripts/sql-database-create-configure-managed-instance-powershell.md) | Detta PowerShell-skript visar hur du skapar och hanterar en hanterad instans med hjälp av Azure PowerShell |
| [Skapa och hantera en hanterad instans med hjälp av en Azure Resource Manager-mall](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Detta PowerShell-skript visar hur du skapar och hanterar en hanterad instans med hjälp av Azure PowerShell och Azure Resource Manager-mallen.|
| **Konfigurera transparent datakryptering (TDE)**||
| [Hantera transparent datakryptering i en hanterad instans med din egen nyckel från Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar transparent datakryptering (TDE) i Bring Your Own Key-scenariot för Azure SQL Managed Instance med hjälp av en nyckel från Azure Key Vault|
|||

Läs mer om [Azure PowerShell API för hanterad instans](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).
