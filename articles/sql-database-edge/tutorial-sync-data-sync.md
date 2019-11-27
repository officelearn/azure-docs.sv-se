---
title: Synkronisera data från Azure SQL Database Edge genom att använda SQL Data Sync | Microsoft Docs
description: Lär dig mer om att synkronisera data från Azure SQL Database Edge med hjälp av Azure SQL Data Sync
keywords: SQL Database Edge, synkronisera data från SQL Database Edge, SQL Database Edge Data Sync
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384184"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Självstudie: synkronisera data från SQL Database Edge till Azure SQL Database med SQL Data Sync

I den här självstudien får du lära dig hur du använder en Azure SQL Data Sync *Sync-grupp* för att stegvis synkronisera Data från Azure SQL Database Edge till Azure SQL Database. SQL Data Sync är en tjänst som bygger på Azure SQL Database som gör att du synkroniserar data som du väljer riktningarna över flera SQL-databaser och SQL Server-instanser. Mer information om SQL Data Sync finns i [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Eftersom SQL Database Edge bygger på de senaste versionerna av SQL Server- [databasmotorn](/sql/sql-server/sql-server-technical-documentation/), kan all mekanism för datasynkronisering som är tillämplig på en lokal SQL Server-instans också användas för att synkronisera data till eller från en SQL Database Edge-instans som körs på en Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver att en Windows-dator har kon figurer ATS med [Data Sync-agenten för Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

## <a name="before-you-begin"></a>Innan du börjar

* Skapa en Azure SQL-databas. Information om hur du skapar en Azure SQL-databas med hjälp av Azure Portal finns [i skapa en enskild databas i Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Skapa tabeller och andra nödvändiga objekt i din Azure SQL Database-distribution.

* Skapa nödvändiga tabeller och objekt i din Azure SQL Database Edge-distribution. Mer information finns i [använda SQL Database DAC-paket med SQL Database Edge](stream-analytics.md).

* Registrera Azure SQL Database Edge-instansen med Data Sync-agenten för Azure SQL Data Sync. Mer information finns i [lägga till en lokal SQL Server databas](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synkronisera data mellan en Azure SQL-databas och SQL Database Edge

Att ställa in synkronisering mellan en Azure SQL-databas och en SQL Database Edge-instans med hjälp av SQL Data Sync innefattar tre viktiga steg:  

1. Använd Azure Portal för att skapa en Sync-grupp. Mer information finns i [skapa en Sync-grupp](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Du kan använda en enda *hubb* -databas för att skapa flera Sync-grupper för att synkronisera data från olika SQL Database Edge-instanser till en eller flera SQL-databaser i Azure.

2. Lägg till Sync-medlemmar i Sync-gruppen. Mer information finns i [lägga till Sync-medlemmar](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Konfigurera Sync-gruppen för att välja de tabeller som ska ingå i synkroniseringen. Mer information finns i [Konfigurera en Sync-grupp](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

När du har slutfört föregående steg har du en Sync-grupp som innehåller en Azure SQL-databas och en SQL Database Edge-instans.

Mer information om SQL Data Sync finns i följande artiklar:

* [Data Sync-agent för Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Metod tips](../sql-database/sql-database-best-practices-data-sync.md) och [fel sökning av problem med Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Övervaka SQL Data Sync med Azure Monitor loggar](../sql-database/sql-database-sync-monitor-oms.md)

* [Uppdatera synkroniseringsschemat med Transact-SQL](../sql-database/sql-database-update-sync-schema.md) eller [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Använd PowerShell för att synkronisera mellan Azure SQL Database och Azure SQL Database kant](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). I den här självstudien ersätter du `OnPremiseServer` Database-information med Azure SQL Database Edge-information.
