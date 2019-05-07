---
title: Serverloggar i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver hur Azure Database för PostgreSQL – enskild Server genererar fråga och felloggar och hur loggbevarande konfigureras.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4d1cf2c59e324cedd9b747b1ac65d6edcb9deb45
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067399"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Serverloggar i Azure Database för PostgreSQL – enskild Server
Azure Database för PostgreSQL genererar fråge- och loggar. Fråga och felloggar kan användas för att identifiera, felsöka och reparera konfigurationsfel och icke-optimal prestanda. (Åtkomst till transaktionsloggar ingår inte). 

## <a name="configure-logging"></a>Konfigurera loggning 
Du kan konfigurera loggning på servern med hjälp av serverparametrarna för loggning. På varje ny server **log_checkpoints** och **log_connections** är aktiverade som standard. Det finns ytterligare parametrar som du kan justera efter dina behov för loggning: 

![Azure Database för PostgreSQL - parametrar](./media/concepts-server-logs/log-parameters.png)

Mer information om dessa parametrar finns i Postgresql's [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentation. Läs hur du konfigurerar Azure Database för PostgreSQL-parametrar i den [portaldokumentationen](howto-configure-server-parameters-using-portal.md) eller [CLI-dokumentationen](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Åtkomst till serverloggar via portalen eller CLI
Om du har aktiverat loggar, kan du komma åt dem från Azure Database for PostgreSQL log storage med den [Azure-portalen](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md), och Azure REST API: er. Loggfilerna rotera varje 1 timme eller storlek på 100MB, beroende på vilket som inträffar först. Du kan ställa in kvarhållningsperioden för den här log storage med hjälp av den **log\_kvarhållning\_period** parameter som är associerade med servern. Standardvärdet är 3 dagar. Det maximala värdet är 7 dagar. Servern måste ha tillräckligt med allokerat lagringsutrymme för att rymma loggfilerna. (Den här parametern för kvarhållning reglerar inte Azure-diagnostikloggar.)


## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database för PostgreSQL är integrerad med Azure Monitor-diagnostikloggar. När du har aktiverat loggar på din PostgreSQL-server, kan du låta dem anges till [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md), Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar, se avsnittet anvisningar i den [diagnostikloggar dokumentation](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Den här diagnostiska funktionen för serverloggar är endast tillgänglig i generell användning och Minnesoptimerad [prisnivåer](concepts-pricing-tiers.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på den slutpunkt för indata som du väljer de fält som ingår och den ordning som de visas kan variera. 

|**Fält** | **Beskrivning** |
|---|---|
| TenantId | Ditt klient-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tidsstämpel när loggen registrerades i UTC |
| Typ | Typ av loggen. Alltid `AzureDiagnostics` |
| SubscriptionId | GUID för den prenumeration som servern tillhör |
| ResourceGroup | Namnet på den resursgrupp som servern tillhör |
| ResourceProvider | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resurs-URI |
| Resource | Namnet på servern |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Loggning nivå, exempel: LOGGEN, FEL, MEDDELANDE |
| Meddelande | Primär loggmeddelande | 
| Domain | Server-versionen, till exempel: postgres-10 |
| Information | Sekundär loggmeddelande (om tillämpligt) |
| columnName | Namnet på kolumnen (om tillämpligt) |
| SchemaName | Namnet på schemat (om tillämpligt) |
| DatatypeName | Namnet på datatypen (om tillämpligt) |
| LogicalServerName | Namnet på servern | 
| _ResourceId | Resurs-URI |

## <a name="next-steps"></a>Nästa steg
- Mer information om åtkomst till loggar från den [Azure-portalen](howto-configure-server-logs-in-portal.md) eller [Azure CLI](howto-configure-server-logs-using-cli.md).
- Läs mer om [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
