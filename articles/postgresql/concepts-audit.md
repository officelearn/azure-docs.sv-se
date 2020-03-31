---
title: Granskningsloggning – Azure-databas för PostgreSQL – enkel server
description: Begrepp för pgAudit-granskningsloggning i Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842477"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Granskningsloggning i Azure Database för PostgreSQL - Single Server

Granskningsloggning av databasaktiviteter i Azure Database för PostgreSQL - Single Server är tillgänglig via PostgreSQL Audit Extension: [pgAudit](https://www.pgaudit.org/). pgAudit tillhandahåller detaljerad sessions- och/eller objektgranskningsloggning.

> [!NOTE]
> pgAudit är i förhandsversion på Azure Database för PostgreSQL.
> Tillägget kan endast aktiveras på servrar med allmänt syfte och minnesoptimerade.

Om du vill ha Azure-loggar på resursnivå för åtgärder som beräknings- och lagringsskalning läser du [Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Användningsöverväganden
Som standard genereras pgAudit-logginstruktioner tillsammans med dina vanliga logginstruktioner med hjälp av standardfunktionen för loggning i Postgres. I Azure Database for PostgreSQL kan dessa .log-filer laddas ned via Azure-portalen eller CLI. Den maximala lagringen för samlingen av filer är 1 GB, och varje fil är tillgänglig i högst sju dagar (standardvärdet är tre dagar). Den här tjänsten är ett kortsiktigt lagringsalternativ.

Alternativt kan du konfigurera alla loggar som ska skickas ut till Azure Monitors diagnostikloggtjänst. Om du aktiverar diagnostikloggning för Azure Monitor skickas dina loggar automatiskt (i JSON-format) till Azure Storage-, Event Hubs och/eller Azure Monitor-loggar, beroende på ditt val.

Aktivering av pgAudit genererar en stor volym av loggning på en server, vilket påverkar prestanda och logglagring. Vi rekommenderar att du använder tjänsten för diagnostikloggning i Azure, som har alternativ för mer långvarig lagring samt funktioner för analys och avisering. Vi rekommenderar att du inaktiverar standardloggning för att minska prestandapåverkan från ytterligare loggning:

   1. Ställ in `logging_collector` parametern på AV. 
   2. Starta om servern för att tillämpa den här ändringen.

Mer information om hur du konfigurerar loggning till Azure Storage-, Event Hubs- eller Azure Monitor-loggar finns i avsnittet diagnostikloggar i [artikeln serverloggar](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Installera pgAudit

Om du vill installera pgAudit måste du inkludera det i serverns delade förinläsningsbibliotek. En ändring av Postgres `shared_preload_libraries` parameter kräver att en omstart av servern börjar gälla. Du kan ändra parametrar med [Azure-portalen,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).

Använda [Azure-portalen:](https://portal.azure.com)

   1. Välj din Azure Database for PostgreSQL-server.
   2. Välj **Serverparametrar**i sidofältet .
   3. Sök efter `shared_preload_libraries` parametern.
   4. Välj **pgaudit**.
   5. Starta om servern för att tillämpa ändringen.

   6. Anslut till servern med en klient (som psql) och aktivera pgAudit-tillägget
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Om ett fel visas bekräftar du att du `shared_preload_libraries`har startat om servern efter att du sparat .

## <a name="pgaudit-settings"></a>pgAudit-inställningar

pgAudit kan du konfigurera session eller objekt granskning loggning. [Sessionsgranskningsloggning](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) avger detaljerade loggar över utförda utdrag. [Loggning av objektgranskning](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) är granskningsscope för specifika relationer. Du kan välja att ställa in en eller båda typerna av loggning. 

> [!NOTE]
> pgAudit-inställningar anges gloabally och kan inte anges på en databas- eller rollnivå.

När du har [installerat pgAudit](#installing-pgaudit)kan du konfigurera dess parametrar för att starta loggningen. [Dokumentationen för pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) innehåller definitionen av varje parameter. Testa parametrarna först och bekräfta att du får det förväntade beteendet.

> [!NOTE]
> Om `pgaudit.log_client` du ställer in på ON omdirigeras loggar till en klientprocess (till exempel psql) i stället för att skrivas till fil. Den här inställningen bör normalt vara inaktiverad. <br> <br>
> `pgaudit.log_level`aktiveras endast `pgaudit.log_client` när den är på.

> [!NOTE]
> I Azure Database for PostgreSQL `pgaudit.log` kan `-` det inte anges med hjälp av en (minus) signera genväg som beskrivs i dokumentationen till pgAudit. Alla obligatoriska instruktionsklasser (READ, WRITE osv.) ska anges var för sig.

### <a name="audit-log-format"></a>Format för granskningslogg
Varje granskningspost anges `AUDIT:` i närheten av början av loggraden. Formatet för resten av posten beskrivs i [dokumentationen för pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Om du behöver andra fält för att uppfylla granskningskraven använder du parametern `log_line_prefix`Postgres . `log_line_prefix`är en sträng som matas ut i början av varje Postgres log line. Följande `log_line_prefix` inställning innehåller till exempel tidsstämpel, användarnamn, databasnamn och process-ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Mer information `log_line_prefix`om finns i [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Komma igång
Om du snabbt vill `pgaudit.log` `WRITE`komma igång ställer du in på och öppnar loggarna för att granska utdata. 

## <a name="viewing-audit-logs"></a>Visa granskningsloggar
Om du använder .log-filer inkluderas dina granskningsloggar i samma fil som dina PostgreSQL-felloggar. Du kan hämta loggfiler från [Azure-portalen](howto-configure-server-logs-in-portal.md) eller [CLI](howto-configure-server-logs-using-cli.md). 

Om du använder Azure-diagnostikloggning beror hur du öppnar loggarna på vilken slutpunkt du väljer. För Azure Storage finns i artikeln [för loggar lagringskonto.](../azure-monitor/platform/resource-logs-collect-storage.md) Information om händelsehubbar finns i artikeln [strömma Azure-loggar.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

För Azure Monitor-loggar skickas loggar till arbetsytan du valde. Postgres-loggarna använder **insamlingsläget AzureDiagnostics,** så att de kan efterfrågas från tabellen AzureDiagnostics. Fälten i tabellen beskrivs nedan. Läs mer om att fråga och avisera i frågaöversikten [för Azure Monitor Logs.](../azure-monitor/log-query/log-query-overview.md)

Du kan använda den här frågan för att komma igång. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla Postgres loggar för en viss server under den sista dagen
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om att logga in i Azure Database för PostgreSQL](concepts-server-logs.md)
- Lär dig hur du ställer in parametrar med [Azure-portalen,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).
