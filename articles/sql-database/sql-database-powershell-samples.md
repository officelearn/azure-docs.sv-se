---
title: "Azure PowerShell exempel på skript för SQL-databas | Microsoft Docs"
description: "Azure PowerShell-skript exempel som hjälper dig att skapa och hantera Azure SQL Database-servrar, elastiska pooler, databaser och brandväggar."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: On Demand
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: fd60a8987e6c57cf72eb7766f6a1b784e46a894d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-exempel för Azure SQL Database

Följande tabell innehåller länkar till exempel Azure PowerShell-skript för Azure SQL Database.

| |  |
|---|---|
|**Skapa en databas och en elastisk pool**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Detta PowerShell-skript skapar en enda Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. |
| [Skapa elastiska pooler och flytta grupperade databaser](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Detta PowerShell-skript skapar Azure SQL Database elastiska pooler, flyttar grupperade databaser och ändrar prestandanivåer.|
|**Konfigurera geo-replikering och redundans**||
| [Konfigurera och redundans en enskild databas som använder aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Detta PowerShell-skript som konfigurerar aktiv geo-replikering för en enda Azure SQL-databas och växlar till den sekundära repliken. |
| [Konfigurera och redundans en delad databas som använder aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Detta PowerShell-skript konfigurerar aktiv geo-replikering för en Azure SQL-databas i en elastisk SQL-pool och växlar till den sekundära repliken. |
| [Konfigurera och redundans en växling vid fel grupp för en enskild databas (förhandsgranskning)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Detta PowerShell-skript konfigurerar en grupp för växling vid fel för en Azure SQL Database server-instans, lägger till en databas i gruppen redundans och växlar till den sekundära servern |
|**Skala en enskilda databaser och en elastisk pool**||
| [Skala en enskild databas](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skript Övervakare prestandamått för en Azure SQL database skalas till en högre prestandanivå och skapar en aviseringsregel på något av prestandamåtten. |
| [Skala en elastisk pool](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skript Övervakare prestandamått för en elastisk pool i Azure SQL Database skalas till en högre prestandanivå och skapar en aviseringsregel på något av prestandamåtten.  |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och hotidentifiering](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar principer för granskning och hotidentifiering identifiering för en Azure SQL database. |
| **Återställa, kopiera och importera en databas**||
| [Återställa en databas](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Detta PowerShell-skript återställer en Azure SQL-databas från en geo-redundant säkerhetskopia och återställer en borttagen Azure SQL-databas till den senaste säkerhetskopian. |
| [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Detta PowerShell-skript skapar en kopia av en befintlig Azure SQL-databas i en ny Azure SQL-server. |
| [Importera en databas från en bacpac-fil](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Detta PowerShell-skript importerar en databas till en Azure SQL-server från en bacpac-fil. |
| **Synkronisera data mellan databaser**||
| [Synkronisera data mellan SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar datasynkronisering ska synkroniseras mellan flera Azure SQL-databaser. |
| [Synkronisera data mellan SQL-databas och SQL Server lokalt](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar datasynkronisering ska synkroniseras mellan en Azure SQL-databas och en lokal SQL Server-databas. |
| [Uppdatera schema för datasynkronisering för SQL-synkronisering](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Detta PowerShell-skript som lägger till eller tar bort objekt från synkroniseringsschema för datasynkronisering. |
|||
