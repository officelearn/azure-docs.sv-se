---
title: Så här konfigurerar du Azure SQL Database - enda | Microsoft Docs
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database - databas
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 195afabc0f512f1202904ab7ebf1471b34e2377f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989278"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Hur du använder en enkel databas i Azure SQL Database

I det här avsnittet kan du hitta olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din databas i Azure SQL Database

## <a name="migrate"></a>Migrera

- [Migrera till SQL Database](sql-database-cloud-migrate.md) – Lär dig mer om rekommenderade migreringsprocessen och verktyg för migrering till en hanterad instans.
- Lär dig hur du [hanterar SQL-databas efter migreringen](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Transaktionsreplikering](replication-to-sql-database.md) att replikera dina datum mellan databaser.
- [Konfigurera hotidentifiering](sql-database-threat-detection.md) så att identifiera misstänkta aktiviteter, till exempel SQL-inmatning eller åtkomst från misstänkta platser för Azure SQL Database.
- [Konfigurera dynamisk datamaskning](sql-database-dynamic-data-masking-get-started-portal.md) att skydda känsliga data.
- [Konfigurera kvarhållning av säkerhetskopior](sql-database-long-term-backup-retention-configure.md) för en databas att hålla dina säkerhetskopior på Azure Blob Storage. Som ett alternativ som finns [konfigurera kvarhållning av säkerhetskopior med hjälp av Azure vault (inaktuell)](sql-database-long-term-backup-retention-configure-vault.md) metod.
- [Konfigurera geo-replikering](sql-database-geo-replication-portal.md) håller en replik av databasen i en annan region.
- [Konfigurera säkerhet för geo-repliker](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Övervaka och finjustera din databas

- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) så att Azure SQL Database optimera prestandan för din arbetsbelastning.
- [Aktivera e-postaviseringar för automatisk justering](sql-database-automatic-tuning-email-notifications.md) att få information om justeringsrekommendationer.
- [Tillämpa prestandarekommendationer](sql-database-advisor-portal.md) och optimera din databas.
- [Skapa aviseringar](sql-database-insights-alerts-portal.md) få meddelanden från Azure SQL Database.
- [Felsökning av anslutningar](sql-database-troubleshoot-common-connection-issues.md) om du upptäcker några problem med nätverksanslutningen mellan program och databasen. Du kan också använda [Resource Health för problem med nätverksanslutningen](sql-database-resource-health.md).
- [Hantera filutrymme](sql-database-file-space-management.md) att övervaka användning av lagringsutrymme i databasen.

## <a name="query-distributed-data"></a>Fråga distribuerade data

- [Fråga lodrätt partitionerade data](sql-database-elastic-query-getting-started-vertical.md) över flera databaser.
- [Rapport över utskalad datanivå](sql-database-elastic-query-horizontal-partitioning.md).
- [Fråga mellan tabeller med olika scheman](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Elastic Database-jobb

- [Skapa och hantera](elastic-jobs-powershell.md) Elastic Database-jobb med hjälp av PowerShell.
- [Skapa och hantera](elastic-jobs-tsql.md) Elastic Database-jobb med hjälp av Transact-SQL.
- [Migrera från gamla Elastiskt jobb](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Horisontell partitionering av databas

- [Uppgradera elastic database-klientbibliotek](sql-database-elastic-scale-upgrade-client-library.md).
- [Skapa shardad app](sql-database-elastic-scale-get-started.md).
- [Fråga vågrätt shardade data](sql-database-elastic-query-getting-started.md).
- Kör [Multi-shard-frågor](sql-database-elastic-scale-multishard-querying.md).
- [Flytta shardade data](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurera säkerhet](sql-database-elastic-scale-split-merge-security-configuration.md) i databasshards.
- [Lägg till en shard](sql-database-elastic-scale-add-a-shard.md) till den aktuella uppsättningen databasshards.
- [Lösa problem i shardkarta](sql-database-elastic-database-recovery-manager.md).
- [Migrera shardad databas](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Skapa räknare](sql-database-elastic-database-perf-counters.md).
- [Använda entity framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) att fråga efter shardade data.
- [Använd Dapper framework](sql-database-elastic-scale-working-with-dapper.md) att fråga efter shardade data.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anvisningar hjälper för den hanterade instansen](sql-database-howto-managed-instance.md)
