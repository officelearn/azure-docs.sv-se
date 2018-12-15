---
title: Så här konfigurerar du Azure SQL Database | Microsoft Docs
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440508"
---
# <a name="how-to-use-azure-sql-database"></a>Hur du använder Azure SQL Database

I det här avsnittet kan du hitta olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera Azure SQL Database. Du kan också hitta specifika instruktionsguider för [enkel databas](sql-database-howto-single-database.md) och [Managed Instance](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Läsa in data

- [Kopiera en enkel databas i Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Importera en databas från en BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Exportera en databas till BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Läs in data med BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Läs in data med ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Datasynkronisering

- [SQL Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Data Sync-agenten](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Replikera schemaändringar](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Övervaka med OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Metodtips för datasynkronisering](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Felsöka datasynkronisering](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Övervakning och justering

-  [Manuell inställning](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Använda DMV: er för att övervaka prestanda](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Använda Query store för att övervaka prestanda](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Felsöka prestanda med smarta insikter](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Använda diagnostiklogg med smarta insikter](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Övervaka minnesintern OLTP-utrymme](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Store utökade händelser till event-fil](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Store utökade händelser i ringbufferten](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Konfigurera villkorlig åtkomst](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Multifaktorautentisering för AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurera multifaktorautentisering](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Konfigurera temporala bevarandeprincip](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Konfigurera transparent Datakryptering med BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Rotera TDE BYOK nycklar](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Ta bort TDE-skydd](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Konfigurera minnesintern OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Konfigurera Azure Automation](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Utveckla program

- [Anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Använda Spark-Anslutningsappen](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Autentisera app](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Felmeddelanden](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Använda batchbearbetning för bättre prestanda](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Vägledning för anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Konfigurera DNS-alias PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Portar – ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C och C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Utforma program

- [Utformning för katastrofåterställning](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Design för elastiska pooler](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Design för uppgraderingar av appar](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Utforma flera innehavare SaaS-program

- [Designmönster för SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS-videoindexerare](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Säkerhet för SaaS-app](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anvisningar hjälper i Managed Instance](sql-database-howto-managed-instance.md).
- Läs mer om [anvisningar hjälper i enkel databas](sql-database-howto-single-database.md).
