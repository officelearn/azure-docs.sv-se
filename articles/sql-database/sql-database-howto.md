---
title: Så här konfigurerar du Azure SQL Database
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
ms.date: 01/25/2019
ms.openlocfilehash: ccdeb883dc9cf2cba499e45e25ff4706bb120463
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689870"
---
# <a name="how-to-use-azure-sql-database"></a>Använda Azure SQL Database

I det här avsnittet hittar du olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din Azure SQL Database. Du kan också hitta detaljerade instruktions guider för [en enkel databas och en](sql-database-howto-single-database.md) [hanterad instans](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Läsa in data

- [Kopiera en enskild databas eller en databas i pooler i Azure](sql-database-copy.md)
- [Importera en databas från en BACPAC](sql-database-import.md)
- [Exportera en databas till BACPAC](sql-database-export.md)
- [Läs in data med BCP](sql-database-load-from-csv-with-bcp.md)
- [Läs in data med ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Datasynkronisering

- [SQL Data Sync](sql-database-sync-data.md)
- [Data Sync-agent](sql-database-data-sync-agent.md)
- [Replikera schema ändringar](sql-database-update-sync-schema.md)
- [Övervaka med OMS](sql-database-sync-monitor-oms.md)
- [Metod tips för datasynkronisering](sql-database-best-practices-data-sync.md)
- [Felsöka datasynkronisering](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Övervakning och justering

- [Manuell justering](sql-database-performance-guidance.md)
- [Använd DMV: er för att övervaka prestanda](sql-database-monitoring-with-dmvs.md)
- [Använd Query Store för att övervaka prestanda](sql-database-operate-query-store.md)
- [Felsöka prestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Använd Intelligent Insights diagnostikloggar](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Övervaka minnes intern OLTP-rymd](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](sql-database-xevent-db-diff-from-svr.md)
- [Lagra utökade händelser i händelse filen](sql-database-xevent-code-event-file.md)
- [Lagra utökade händelser i ringbufferten](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Azure AD-autentisering](sql-database-aad-authentication-configure.md)
- [Konfigurera villkorlig åtkomst](sql-database-conditional-access.md)
- [AAD-autentisering med flera faktorer](sql-database-ssms-mfa-authentication.md)
- [Konfigurera Multi-factor auth](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurera princip för temporal bevarande](sql-database-temporal-tables-retention-policy.md)
- [Konfigurera TDE med BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Rotera TDE BYOK-nycklar](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Ta bort TDE-skydd](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurera minnesintern OLTP](sql-database-in-memory-oltp-migration.md)
- [Konfigurera Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Utveckla program

- [Anslutning](sql-database-libraries.md)
- [Använd Spark-anslutning](sql-database-spark-connector.md)
- [Autentisera app](sql-database-client-id-keys.md)
- [Felmeddelanden](sql-database-develop-error-messages.md)
- [Använd batching för bättre prestanda](sql-database-use-batching-to-improve-performance.md)
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

### <a name="design-multi-tenant-saas-applications"></a>Designa SaaS-program för flera innehavare

- [Design mönster för SaaS](saas-tenancy-app-design-patterns.md)
- [SaaS video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS App Security](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [instruktions guider för hanterade instanser](sql-database-howto-managed-instance.md).
- Lär dig mer om [instruktions guider för enskilda databaser](sql-database-howto-single-database.md).
