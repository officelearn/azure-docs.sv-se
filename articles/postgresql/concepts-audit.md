---
title: Gransknings loggning med pgAudit i Azure Database for PostgreSQL-enskild server
description: Begrepp för pgAudit gransknings loggning i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 198ab5f567652a76d209168041f305b9da4d0b43
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147174"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Gransknings loggning i Azure Database for PostgreSQL-enskild server

Gransknings loggning av databas aktiviteter i Azure Database for PostgreSQL – enskild server är tillgänglig via PostgreSQL gransknings tillägg: [pgAudit](https://www.pgaudit.org/). pgAudit tillhandahåller detaljerad loggning av sessioner och/eller objekt granskning.

> [!NOTE]
> pgAudit är en för hands version av Azure Database for PostgreSQL.
> Tillägget kan bara aktive ras på Generell användning och minnesoptimerade servrar.

## <a name="usage-considerations"></a>Användnings överväganden
Som standard genereras logg instruktioner för pgAudit tillsammans med dina reguljära logg instruktioner med hjälp av Postgress standard loggnings funktion. I Azure Database for PostgreSQL kan loggfilerna hämtas via Azure Portal eller CLI. Det maximala lagrings utrymmet för samling av filer är 1 GB och varje fil är tillgänglig i högst sju dagar (Standardvärdet är tre dagar). Den här tjänsten är ett alternativ för kortsiktig lagring.

Alternativt kan du konfigurera alla loggar så att de skickas till Azure Monitorens diagnostiska logg tjänst. Om du aktiverar Azure Monitor diagnostisk loggning skickas loggarna automatiskt (i JSON-format) till Azure Storage, Event Hubs och/eller Azure Monitor loggar, beroende på vad du väljer.

Aktivering av pgAudit genererar en stor volym loggning på en server som påverkar prestanda och logg lagring. Vi rekommenderar att du använder tjänsten Azure Diagnostic Log, som erbjuder lagrings alternativ på längre sikt, samt analys-och aviserings funktioner. Vi rekommenderar att du inaktiverar standard loggning för att minska prestanda påverkan för ytterligare loggning:

   1. Ange parametern `logging_collector` till av. 
   2. Starta om servern för att tillämpa den här ändringen.

Information om hur du ställer in loggning för Azure Storage, Event Hubs eller Azure Monitor loggar finns i avsnittet diagnostikloggar i [artikeln Server loggar](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Installerar pgAudit

Om du vill installera pgAudit måste du ta med den i serverns delade preload-bibliotek. En ändring av postgres `shared_preload_libraries` -parametern kräver att en omstart av servern börjar gälla. Du kan ändra parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).

Använda [Azure Portal](https://portal.azure.com):

   1. Välj din Azure Database for PostgreSQL-server.
   2. Välj **Server parametrar**på sid panelen.
   3. Sök efter `shared_preload_libraries` parametern.
   4. Välj **pgaudit**.
   5. Starta om servern för att tillämpa ändringen.

   6. Anslut till servern med hjälp av en klient (som psql) och aktivera pgAudit-tillägget
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Om du ser ett fel bekräftar du att du har startat om servern när du har `shared_preload_libraries`sparat.

## <a name="pgaudit-settings"></a>pgAudit-inställningar

med pgAudit kan du Konfigurera loggning av session-eller objekt granskning. I [gransknings loggen för sessioner](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) anges detaljerade loggar för körda instruktioner. Granskning av [objekt granskning](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) är begränsad till vissa relationer. Du kan välja att konfigurera en eller båda typerna av loggning. 

> [!NOTE]
> pgAudit-inställningar har angetts gloabally och kan inte anges på en databas eller roll nivå.

När du har [installerat pgAudit](#installing-pgaudit)kan du konfigurera dess parametrar för att starta loggningen. [PgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) innehåller definitionen av varje parameter. Testa parametrarna först och bekräfta att du får det förväntade beteendet.

> [!NOTE]
> Om `pgaudit.log_client` du ställer in på på omdirigeras loggarna till en klient process (som psql) i stället för att skrivas till filen. Den här inställningen bör normalt lämnas inaktive rad.

> [!NOTE]
> `pgaudit.log_level`är bara aktive `pgaudit.log_client` rad när är på. I Azure Portal finns det för närvarande en bugg med `pgaudit.log_level`: en kombinations ruta visas, vilket innebär att flera nivåer kan väljas. Men endast en nivå ska väljas. 

> [!NOTE]
> I Azure Database for PostgreSQL `pgaudit.log` kan inte anges med en `-` (minus) Sign-genväg enligt beskrivningen i pgAudit-dokumentationen. Alla obligatoriska instruktions klasser (läsa, skriva o.s.v.) måste anges var för sig.

### <a name="audit-log-format"></a>Gransknings logg format
Varje gransknings post anges i `AUDIT:` närheten av logg radens början. Formatet på resten av posten beskrivs i [pgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Om du behöver andra fält för att uppfylla gransknings kraven använder du parametern `log_line_prefix`postgres. `log_line_prefix`är en sträng som matas in i början av varje postgres-loggfil. Till exempel tillhandahåller följande `log_line_prefix` inställning tidstämpel, användar namn, databas namn och process-ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Mer `log_line_prefix`information finns i [dokumentationen för postgresql](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Komma igång
För att snabbt komma igång, `pgaudit.log` ange `WRITE`till och öppna dina loggar för att granska utdata. 


## <a name="next-steps"></a>Nästa steg
- [Läs mer om loggning i Azure Database for PostgreSQL](concepts-server-logs.md)
- Lär dig hur du ställer in parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).
