---
title: Konfigurera och hantera
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209430"
---
# <a name="how-to-use-azure-sql-database"></a>Så här använder du Azure SQL Database

I det här avsnittet hittar du olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din Azure SQL-databas. Du kan också hitta specifika programguider för [en enda databas](sql-database-howto-single-database.md) och [hanterad instans](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Läsa in data

- [Kopiera en enskild databas eller poolad databas i Azure](sql-database-copy.md)
- [Importera en databas från en BACPAC-fil](sql-database-import.md)
- [Exportera en databas till en BACPAC-fil](sql-database-export.md)
- [Läsa in data med BCP](sql-database-load-from-csv-with-bcp.md)
- [Läs in data med ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Datasynkronisering

- [SQL Data Sync](sql-database-sync-data.md)
- [Datasynkroniseringsagent](sql-database-data-sync-agent.md)
- [Replikera schemaändringar](sql-database-update-sync-schema.md)
- [Övervaka med OMS](sql-database-sync-monitor-oms.md)
- [Metodtips för Data Sync](sql-database-best-practices-data-sync.md)
- [Felsöka Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Övervakning och justering

- [Manuell inställning](sql-database-performance-guidance.md)
- [Använda DMV:er för att övervaka prestanda](sql-database-monitoring-with-dmvs.md)
- [Använda Query Store för att övervaka prestanda](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Felsöka prestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Använda Intelligent Insights-diagnostikloggar](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Övervaka minnesintern OLTP-plats](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](sql-database-xevent-db-diff-from-svr.md)
- [Lagra utökade händelser i händelsefilen](sql-database-xevent-code-event-file.md)
- [Lagra utökade händelser i ringbuffert](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Azure AD-autentisering](sql-database-aad-authentication-configure.md)
- [Konfigurera villkorlig åtkomst](sql-database-conditional-access.md)
- [Flerfaktorautentisering för AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurera multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurera temporal bevarandeprincip](sql-database-temporal-tables-retention-policy.md)
- [Konfigurera TDE med BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Rotera TDE BYOK-nycklar](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Ta bort TDE-skydd](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurera minnesintern OLTP](sql-database-in-memory-oltp-migration.md)
- [Konfigurera Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Utveckla program

- [Anslutning](sql-database-libraries.md)
- [Använda Spark-anslutningsappen](sql-database-spark-connector.md)
- [Autentisera app](sql-database-client-id-keys.md)
- [Använd batching för bättre prestanda](sql-database-use-batching-to-improve-performance.md)
- [Vägledning för anslutning](sql-database-connectivity-issues.md)
- [DNS-alias](dns-alias-overview.md)
- [Konfigurera DNS-alias PowerShell](dns-alias-powershell.md)
- [Portar – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C och C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Utforma program

- [Utforma för haveriberedskap](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Utforma för elastiska pooler](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Utforma för appuppgraderingar](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Utforma SaaS-program med flera innehavare

- [SaaS-designmönster](saas-tenancy-app-design-patterns.md)
- [SaaS-videoindexerare](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Säkerhet för SaaS-app](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [instruktioner för hanterade instanser](sql-database-howto-managed-instance.md).
- Läs mer om [instruktioner för enskilda databaser](sql-database-howto-single-database.md).
