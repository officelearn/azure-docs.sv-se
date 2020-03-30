---
title: Loggar - Azure-databas för PostgreSQL - Single Server
description: Beskriver loggningskonfiguration, lagring och analys i Azure Database för PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280500"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Loggar i Azure Database för PostgreSQL - Single Server
Azure Database för PostgreSQL kan du konfigurera och komma åt Postgres standardloggar. Loggarna kan användas för att identifiera, felsöka och reparera konfigurationsfel och underoptimala prestanda. Loggningsinformation som du kan konfigurera och komma åt innehåller fel, frågeinformation, autovacuum-poster, anslutningar och kontrollpunkter. (Åtkomst till transaktionsloggar är inte tillgänglig).

Granskningsloggning görs tillgängligt genom en Postgres-förlängning, pgaudit. Mer information finns i artikeln [om granskningsbegrepp.](concepts-audit.md)


## <a name="configure-logging"></a>Konfigurera loggning 
Du kan konfigurera Postgres standardloggning på servern med hjälp av parametrarna för loggningsservern. På varje Azure-databas för PostgreSQL-server `log_checkpoints` och `log_connections` är aktiverad som standard. Det finns ytterligare parametrar som du kan justera för att passa dina loggningsbehov: 

![Azure Database för PostgreSQL - Loggningsparametrar](./media/concepts-server-logs/log-parameters.png)

Om du vill veta mer om Postgres loggparametrar kan du besöka avsnitten [När du ska logga](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) och logga och logga [in](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) i Postgres-dokumentationen. De flesta, men inte alla, Postgres loggningsparametrar är tillgängliga för konfiguration i Azure Database för PostgreSQL.

Mer information om hur du konfigurerar parametrar i Azure Database för PostgreSQL finns i [portaldokumentationen](howto-configure-server-parameters-using-portal.md) eller [CLI-dokumentationen](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Om du konfigurerar en stor mängd loggar, till exempel programinloggning, kan det lägga till betydande prestandakostnader. 

## <a name="access-log-files"></a>Komma åt .-loggfiler
Standardloggformatet i Azure Database for PostgreSQL är .log. En exempelrad från den här loggen ser ut som:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL tillhandahåller en kortvarig lagringsplats för .-loggfilerna. En ny fil börjar var 1 timme eller 100 MB, beroende på vilket som inträffar först. Loggar läggs till i den aktuella filen när de skickas ut från Postgres.  

Du kan ange kvarhållningsperioden för den här `log_retention_period` korttidslogglagringen med parametern. Standardvärdet är 3 dagar och maxvärdet är 7 dagar. Den kortsiktiga lagringsplatsen kan innehålla upp till 1 GB loggfiler. Efter 1 GB tas de äldsta filerna, oavsett kvarhållningsperiod, bort för att ge plats åt nya loggar. 

För långsiktig kvarhållning av loggar och logganalys kan du hämta LOGGFILERNA och flytta dem till en tjänst från tredje part. Du kan hämta filerna med [Azure-portalen](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Alternativt kan du konfigurera diagnostikinställningar för Azure Monitor som automatiskt avger dina loggar (i JSON-format) till längre platser. Läs mer om det här alternativet i avsnittet nedan. 

Du kan sluta generera .log-filer `logging_collector` genom att ange parametern till AV. Det rekommenderas att inaktivera generering av loggfiler om du använder diagnostikinställningarna för Azure Monitor. Den här konfigurationen minskar prestandapåverkan av ytterligare loggning.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for PostgreSQL är integrerad med diagnostikinställningar för Azure Monitor. Med diagnostikinställningar kan du skicka dina Postgres-loggar i JSON-format till Azure Monitor Logs för analys och avisering, eventhubbar för direktuppspelning och Azure Storage för arkivering. 

> [!IMPORTANT]
> Den här diagnostikfunktionen för serverloggar är endast tillgänglig i [prisnivåerna](concepts-pricing-tiers.md)Allmänt ändamål och Minne Optimerad .


### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar
Du kan aktivera diagnostikinställningar för Din Postgres-server med Hjälp av Azure-portalen, CLI, REST API och Powershell. Loggkategorin som du vill välja är **PostgreSQLLogs**. (Det finns andra loggar som du kan konfigurera om du använder [Query Store](concepts-query-store.md).)

Så här aktiverar du diagnostikloggar med Azure-portalen:

   1. Gå till *Diagnostikinställningar i* navigeringsmenyn på din Postgres-server i portalen.
   2. Välj *Lägg till diagnostikinställning*.
   3. Ge den här inställningen ett namn. 
   4. Välj önskad slutpunkt (lagringskonto, händelsehubb, logganalys). 
   5. Välj **loggtypen PostgreSQLLogs**.
   7. Spara inställningen.

Om du vill aktivera diagnostikloggar med Powershell,CLI eller REST API besöker du artikeln [med diagnostikinställningar.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

Hur du kommer åt loggarna beror på vilken slutpunkt du väljer. För Azure Storage finns i artikeln [för loggar lagringskonto.](../azure-monitor/platform/resource-logs-collect-storage.md) Information om händelsehubbar finns i artikeln [strömma Azure-loggar.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

För Azure Monitor-loggar skickas loggar till arbetsytan du valde. Postgres-loggarna använder **insamlingsläget AzureDiagnostics,** så att de kan efterfrågas från tabellen AzureDiagnostics. Fälten i tabellen beskrivs nedan. Läs mer om att fråga och avisera i frågaöversikten [för Azure Monitor Logs.](../azure-monitor/log-query/log-query-overview.md)

Följande är frågor som du kan försöka komma igång med. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla Postgres loggar för en viss server under den sista dagen
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Sök efter alla icke-localhost-anslutningsförsök
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Frågan ovan visar resultat under de senaste 6 timmarna för alla Postgres-serverloggning på den här arbetsytan.

### <a name="log-format"></a>Loggformat

I följande tabell beskrivs fälten för typen **PostgreSQLLogs.** Beroende på vilken utdataslutpunkt du väljer kan de fält som ingår och i vilken ordning de visas variera. 

|**Field** | **Beskrivning** |
|---|---|
| TenantId | Ditt klient-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tidsstämpel när loggen spelades in i UTC |
| Typ | Typ av logg. Alltid`AzureDiagnostics` |
| SubscriptionId | GUID för den prenumeration som servern tillhör |
| ResourceGroup | Namnet på den resursgrupp som servern tillhör |
| ResourceProvider | Resursleverantörens namn. Alltid`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resurs URI |
| Resurs | Serverns namn |
| Kategori | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Errorlevel | Loggningsnivå, exempel: LOG, FEL, MEDDELANDE |
| Meddelande | Primärt loggmeddelande | 
| Domain | Serverversion, exempel: postgres-10 |
| Information | Sekundärt loggmeddelande (om tillämpligt) |
| ColumnName | Kolumnens namn (om tillämpligt) |
| SchemaName | Schemats namn (om tillämpligt) |
| DatatypeName (DatatypeName) | Datatypens namn (om tillämpligt) |
| LogicalServerName | Serverns namn | 
| _ResourceId | Resurs URI |
| Prefix | Prefix för loggraden |


## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du använder loggar från [Azure-portalen](howto-configure-server-logs-in-portal.md) eller [Azure CLI](howto-configure-server-logs-using-cli.md).
- Läs mer om [Azure Monitor-priser](https://azure.microsoft.com/pricing/details/monitor/).
- Läs mer om [granskningsloggar](concepts-audit.md)
