---
title: Server loggar i Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur Azure Database for PostgreSQL-enskild server genererar frågor och fel loggar och hur logg kvarhållning konfigureras.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091124"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Server loggar i Azure Database for PostgreSQL-enskild server
Azure Database for PostgreSQL genererar fråge- och felloggar. Frågor och fel loggar kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda. (Åtkomst till transaktions loggar ingår inte). 

## <a name="configure-logging"></a>Konfigurera loggning 
Du kan konfigurera loggning på servern med hjälp av loggnings server parametrarna. På varje ny server **log_checkpoints** och **log_connections** är aktiverat som standard. Det finns ytterligare parametrar som du kan justera så att de passar dina loggnings behov: 

![Parametrar för Azure Database for PostgreSQL-loggning](./media/concepts-server-logs/log-parameters.png)

Mer information om dessa parametrar finns i PostgreSQL [fel rapportering och logg](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentation. Information om hur du konfigurerar Azure Database for PostgreSQL parametrar finns i [Portal dokumentationen](howto-configure-server-parameters-using-portal.md) eller CLI- [dokumentationen](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Åtkomst till Server loggar via portalen eller CLI
Om du har aktiverat loggar kan du komma åt dem från Azure Database for PostgreSQL logg lagring med hjälp av [Azure Portal](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md)och Azure REST API: er. Loggfilerna roteras varje timme eller varje 100 MB, beroende på vilket som kommer först. Du kan ställa in kvarhållningsperioden för den här logg lagringen med hjälp av parametern **\_logg kvarhållningsperiod\_** som är associerad med servern. Standardvärdet är 3 dagar; det maximala värdet är 7 dagar. Servern måste ha tillräckligt mycket allokerat lagrings utrymme för att kunna lagra loggfilerna. (Denna bevarande parameter styr inte Azure-diagnostikloggar.)


## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for PostgreSQL är integrerat med Azure Monitor diagnostikloggar. När du har aktiverat loggar på PostgreSQL-servern kan du välja att de ska skickas till [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md), Event Hubs eller Azure Storage. 

> [!IMPORTANT]
> Den här Diagnostic-funktionen för Server loggar är bara tillgänglig i Generell användning och minnesoptimerade [pris nivåer](concepts-pricing-tiers.md).

Så här aktiverar du diagnostikloggar med hjälp av Azure Portal:

   1. I portalen går du till *diagnostikinställningar* i navigerings menyn på postgres-servern.
   2. Välj *Lägg till diagnostisk inställning*.
   3. Ge den här inställningen ett namn. 
   4. Välj önskad plats för överordnad plats (lagrings konto, händelsehubben, Log Analytics). 
   5. Välj de data typer som du vill använda.
   6. Spara inställningen.

I följande tabell beskrivs vad som finns i varje logg. Beroende på vilken slut punkt för utdata du väljer kan de fält som ingår och i vilken ordning de visas variera. 

|**Fält** | **Beskrivning** |
|---|---|
| TenantId | Ditt klient-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tidstämpel när loggen registrerades i UTC |
| Type | Loggens typ. Alltid `AzureDiagnostics` |
| SubscriptionId | GUID för den prenumeration som servern tillhör |
| ResourceGroup | Namnet på den resurs grupp som servern tillhör |
| ResourceProvider | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resurs-URI |
| Resource | Namnet på servern |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Loggnings nivå, exempel: LOGG, FEL, MEDDELANDE |
| Message | Primärt logg meddelande | 
| Domain | Server version, exempel: postgres-10 |
| Detalj | Sekundärt logg meddelande (om tillämpligt) |
| Kolumn | Namnet på kolumnen (om tillämpligt) |
| SchemaName | Schemats namn (om tillämpligt) |
| DatatypeName | Namnet på data typen (om tillämpligt) |
| LogicalServerName | Namnet på servern | 
| _ResourceId | Resurs-URI |
| Prefix | Logg radens prefix |



## <a name="next-steps"></a>Nästa steg
- Lär dig mer om att komma åt loggar från [Azure Portal](howto-configure-server-logs-in-portal.md) eller [Azure CLI](howto-configure-server-logs-using-cli.md).
- Läs mer om [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).
