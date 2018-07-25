---
title: Azure PowerShell-exempelskript för SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript för att skapa och hantera Azure SQL Database-servrar, elastiska pooler, databaser och brandväggar.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 81f39c2ecc014ee53dcff2fab88ff0f890e1a610
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091906"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-exempel för Azure SQL Database

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure SQL Database.

| |  |
|---|---|
|**Skapa en databas och en elastisk pool**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. |
| [Skapa elastiska pooler och flytta databaser i pooler](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-exempelskriptet skapar elastiska Azure SQL Database-pooler och flyttar databaser i pooler och ändrar prestandanivåer.|
|**Konfigurera geo-replikering och redundans**||
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar en aktiv geo-replikering för en enskild Azure SQL-databas och redundansväxlar den till en sekundär replik. |
| [Konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar en aktiv geo-replikering för en enskild Azure SQL-databas i en elastisk SQL-pool och redundansväxlar den till en sekundär replik. |
| [Konfigurera och redundansväxla en redundansgrupp för en enskild databas](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar en redundansgrupp för en Azure SQL Database-serverinstans, lägger till en databas i redundansgruppen och redundansväxlar den till den sekundära servern |
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestandavärden för en Azure SQL-databas, skalar den till en högre prestandanivå och skapar en varningsregel för ett av prestandavärdena. |
| [Skala en elastisk pool](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestandavärden för en elastisk Azure SQL Database-pool, skalar den till en högre prestandanivå och skapar en varningsregel för ett av prestandavärdena.  |
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
