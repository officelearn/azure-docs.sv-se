---
title: Konfigurera en enskild databas
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database – en enda databas
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027718"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Så här använder du en enskild databas i Azure SQL Database

I det här avsnittet hittar du olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din enda databas i Azure SQL Database

## <a name="migrate"></a>Migrera

- [Migrera till SQL Database](sql-database-single-database-migrate.md) – Lär dig mer om den rekommenderade migreringsprocessen och verktygen för migrering till en hanterad instans.
- Lär dig hur du [hanterar SQL-databas efter migreringen](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera transaktionsreplikering](replication-to-sql-database.md) så att datumet replikeras mellan databaser.
- [Konfigurera hotidentifiering](sql-database-threat-detection.md) så att Azure SQL Database kan identifiera misstänkta aktiviteter som SQL Injection eller åtkomst från misstänkta platser.
- [Konfigurera dynamisk datamaskering](sql-database-dynamic-data-masking-get-started-portal.md) för att skydda känsliga data.
- [Konfigurera lagring av säkerhetskopiering](sql-database-long-term-backup-retention-configure.md) för en databas för att behålla dina säkerhetskopior på Azure Blob Storage. Som ett alternativ finns [konfigurera lagring av säkerhetskopiering med Azure vault (föråldrad)](sql-database-long-term-backup-retention-configure-vault.md) metod.
- [Konfigurera georeplikering](sql-database-geo-replication-portal.md) för att hålla en replik av databasen i en annan region.
- [Konfigurera säkerhet för georepliker](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Övervaka och finjustera databasen

- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) så att Azure SQL Database kan optimera prestanda för din arbetsbelastning.
- [Aktivera e-postmeddelanden för automatisk justering för](sql-database-automatic-tuning-email-notifications.md) att få information om justeringsrekommendationer.
- [Tillämpa prestandarekommendationer](sql-database-advisor-portal.md) och optimera databasen.
- [Skapa aviseringar](sql-database-insights-alerts-portal.md) för att få meddelanden från Azure SQL Database.
- [Felsöka anslutningen](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) om du upptäcker vissa anslutningsproblem mellan programmen och databasen. Du kan också använda [Resurshälsa för anslutningsproblem](sql-database-resource-health.md).
- [Hantera filutrymme](sql-database-file-space-management.md) för att övervaka lagringsanvändningen i databasen.

## <a name="query-distributed-data"></a>Fråga distribuerade data

- [Fråga lodrätt partitionerade data](sql-database-elastic-query-getting-started-vertical.md) över flera databaser.
- [Rapportera över utskalad datanivå](sql-database-elastic-query-horizontal-partitioning.md).
- [Fråga över tabeller med olika scheman](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Elastic Database-jobb

- [Skapa och hantera](elastic-jobs-powershell.md) Elastiska databasjobb med PowerShell.
- [Skapa och hantera](elastic-jobs-tsql.md) Elastiska databasjobb med Transact-SQL.
- [Migrera från det gamla elastiska jobbet](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Horisontell partitionering av databas

- [Uppgradera klientbiblioteket för elastisk databas](sql-database-elastic-scale-upgrade-client-library.md).
- [Skapa fragmenterad app](sql-database-elastic-scale-get-started.md).
- [Fråga vågrätt fragmenterade data](sql-database-elastic-query-getting-started.md).
- Kör [multi-shardfrågor](sql-database-elastic-scale-multishard-querying.md).
- [Flytta fragmenterade data](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurera säkerhet](sql-database-elastic-scale-split-merge-security-configuration.md) i databasshards.
- [Lägg till en shard](sql-database-elastic-scale-add-a-shard.md) i den aktuella uppsättningen databasshards.
- [Åtgärda problem med fragmentkartan](sql-database-elastic-database-recovery-manager.md).
- [Migrera fragmenterad DB](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Skapa räknare](sql-database-elastic-database-perf-counters.md).
- [Använd entitetsramverk](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) för att fråga fragmenterade data.
- [Använd Dapper-ramverket](sql-database-elastic-scale-working-with-dapper.md) för att fråga fragmenterade data.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [How-to-guider för hanterad instans](sql-database-howto-managed-instance.md)
