---
title: Så här konfigurerar du Azure SQL Database | Microsoft Docs
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: f7e31c9e153f25faae9224f04eabf5ca54bb06b4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759227"
---
# <a name="how-to-use-azure-sql-database"></a>Hur du använder Azure SQL Database

I det här avsnittet kan du hitta olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera Azure SQL Database. Du kan också hitta specifika instruktionsguider för [enkel databas](sql-database-howto-single-database.md) och [Managed Instance](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Läsa in data

- [Kopiera en enkel databas eller en databas i Azure](sql-database-copy.md)
- [Importera en databas från en BACPAC](sql-database-import.md)
- [Exportera en databas till BACPAC](sql-database-export.md)
- [Läs in data med BCP](sql-database-load-from-csv-with-bcp.md)
- [Läs in data med ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Datasynkronisering

- [SQL Data Sync](sql-database-sync-data.md)
- [Data Sync-agenten](sql-database-data-sync-agent.md)
- [Replikera schemaändringar](sql-database-update-sync-schema.md)
- [Övervaka med OMS](sql-database-sync-monitor-oms.md)
- [Metodtips för datasynkronisering](sql-database-best-practices-data-sync.md)
- [Felsöka datasynkronisering](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Övervakning och justering

- [Manuell inställning](sql-database-performance-guidance.md)
- [Använda DMV: er för att övervaka prestanda](sql-database-monitoring-with-dmvs.md)
- [Använda Query store för att övervaka prestanda](sql-database-operate-query-store.md)
- [Felsöka prestanda med smarta insikter](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Använda diagnostiklogg med smarta insikter](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Övervaka minnesintern OLTP-utrymme](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](sql-database-xevent-db-diff-from-svr.md)
- [Store utökade händelser till event-fil](sql-database-xevent-code-event-file.md)
- [Store utökade händelser i ringbufferten](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Azure AD-autentisering](sql-database-aad-authentication-configure.md)
- [Konfigurera villkorlig åtkomst](sql-database-conditional-access.md)
- [Multifaktorautentisering för AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurera multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurera temporala bevarandeprincip](sql-database-temporal-tables-retention-policy.md)
- [Konfigurera transparent Datakryptering med BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Rotera TDE BYOK nycklar](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Ta bort TDE-skydd](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurera minnesintern OLTP](sql-database-in-memory-oltp-migration.md)
- [Konfigurera Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Utveckla program

- [Anslutning](sql-database-libraries.md)
- [Använda Spark-Anslutningsappen](sql-database-spark-connector.md)
- [Autentisera app](sql-database-client-id-keys.md)
- [Felmeddelanden](sql-database-develop-error-messages.md)
- [Använda batchbearbetning för bättre prestanda](sql-database-use-batching-to-improve-performance.md)
- [Vägledning för anslutning](sql-database-connectivity-issues.md)
- [DNS-alias](dns-alias-overview.md)
- [Konfigurera DNS-alias PowerShell](dns-alias-powershell.md)
- [Portar – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C och C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Utforma program

- [Utforma för haveriberedskap](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Design för elastiska pooler](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design för uppgraderingar av appar](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Utforma flera innehavare SaaS-program

- [Designmönster för SaaS](saas-tenancy-app-design-patterns.md)
- [SaaS-videoindexerare](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Säkerhet för SaaS-app](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [anvisningar hjälper för hanterade instanser](sql-database-howto-managed-instance.md).
- Läs mer om [anvisningar hjälper för enskilda databaser](sql-database-howto-single-database.md).
