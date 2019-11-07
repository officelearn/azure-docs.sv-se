---
title: Så här konfigurerar du Azure SQL Database-Single
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database-enkel databas
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 45e25641092fae36cd58a59400cea025f74cbc1f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689897"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Använda en enda databas i Azure SQL Database

I det här avsnittet hittar du olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera en enskild databas i Azure SQL Database

## <a name="migrate"></a>Migrera

- [Migrera till SQL Database](sql-database-single-database-migrate.md) – Läs mer om den rekommenderade migreringsprocessen och verktyg för migrering till en hanterad instans.
- Lär dig hur du [hanterar SQL Database efter migreringen](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera](replication-to-sql-database.md) Transaktionsreplikering för att replikera ditt datum mellan databaser.
- [Konfigurera hot identifiering](sql-database-threat-detection.md) för att låta Azure SQL Database identifiera misstänkta aktiviteter som SQL-inmatning eller åtkomst från misstänkta platser.
- [Konfigurera dynamisk data maskning](sql-database-dynamic-data-masking-get-started-portal.md) för att skydda känsliga data.
- [Konfigurera kvarhållning av säkerhets kopior](sql-database-long-term-backup-retention-configure.md) för en databas om du vill behålla dina säkerhets kopior på Azure Blob Storage. Ett alternativ är att [Konfigurera kvarhållning av säkerhets kopior med Azure Vault (inaktuell)](sql-database-long-term-backup-retention-configure-vault.md) metod.
- [Konfigurera geo-replikering](sql-database-geo-replication-portal.md) för att behålla en replik av databasen i en annan region.
- [Konfigurera säkerhet för geo-Replicas](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Övervaka och finjustera databasen

- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) för att låta Azure SQL Database optimera arbets Belastningens prestanda.
- [Aktivera e-postaviseringar för automatisk justering](sql-database-automatic-tuning-email-notifications.md) för att få information om justerings rekommendationer.
- [Använd prestanda rekommendationer](sql-database-advisor-portal.md) och optimera databasen.
- [Skapa aviseringar](sql-database-insights-alerts-portal.md) för att få meddelanden från Azure SQL Database.
- [Felsök anslutningen](sql-database-troubleshoot-common-connection-issues.md) om du märker några anslutnings problem mellan programmen och-databasen. Du kan också använda [Resource Health för anslutnings problem](sql-database-resource-health.md).
- [Hantera fil utrymme](sql-database-file-space-management.md) för att övervaka lagrings användningen i databasen.

## <a name="query-distributed-data"></a>Fråga distribuerade data

- [Fråga lodrätt partitionerade data](sql-database-elastic-query-getting-started-vertical.md) över flera databaser.
- [Rapport över skalade data nivåer](sql-database-elastic-query-horizontal-partitioning.md).
- [Fråga över tabeller med olika scheman](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Elastic Database-jobb

- [Skapa och hantera](elastic-jobs-powershell.md) Elastic Database jobb med PowerShell.
- [Skapa och hantera](elastic-jobs-tsql.md) Elastic Database jobb med hjälp av Transact-SQL.
- [Migrera från gammalt elastiskt jobb](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Horisontell partitionering av databas

- [Uppgradera klient biblioteket för Elastic Database](sql-database-elastic-scale-upgrade-client-library.md).
- [Skapa shardade-app](sql-database-elastic-scale-get-started.md).
- [Fråga vågrätt shardade data](sql-database-elastic-query-getting-started.md).
- Köra [multi-Shard-frågor](sql-database-elastic-scale-multishard-querying.md).
- [Flytta shardade-data](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurera säkerhet](sql-database-elastic-scale-split-merge-security-configuration.md) i Database Shards.
- [Lägg till en Shard](sql-database-elastic-scale-add-a-shard.md) i den aktuella uppsättningen databas Shards.
- [Åtgärda problem med Shard Map](sql-database-elastic-database-recovery-manager.md).
- [Migrera SHARDADE DB](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Skapa räknare](sql-database-elastic-database-perf-counters.md).
- [Använd Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) för att fråga shardade-data.
- [Använd dapper Framework](sql-database-elastic-scale-working-with-dapper.md) för att fråga shardade-data.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [instruktions guider för hanterad instans](sql-database-howto-managed-instance.md)
