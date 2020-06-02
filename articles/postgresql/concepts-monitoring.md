---
title: Övervaka och finjustera – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver övervaknings-och justerings funktionerna i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 935fba3869367bff971e6067b22b05b3a0d9119f
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266890"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Övervaka och finjustera Azure Database for PostgreSQL-enskild server
Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Azure Database for PostgreSQL innehåller olika övervaknings alternativ för att ge inblick i serverns beteende.

## <a name="metrics"></a>Mått
Azure Database for PostgreSQL tillhandahåller olika mått som ger inblick i funktionerna i resurserna som stöder PostgreSQL-servern. Varje mått genereras med en minuters frekvens och har upp till 30 dagars historik. Du kan konfigurera aviseringar för måtten. Steg för steg-anvisningar finns i [så här konfigurerar du aviseringar](howto-alert-on-metric.md). Andra uppgifter är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for PostgreSQL:

|Mått|Mått visnings namn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procent andelen CPU som används.|
|memory_percent|Minnes procent|Procent|Procent andelen minne som används.|
|io_consumption_percent|I/o procent|Procent|Procent andelen av IO som används.|
|storage_percent|Lagrings procent|Procent|Procent andelen lagring som används av serverns högsta värde.|
|storage_used|Använt lagrings utrymme|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
|storage_limit|Lagrings gräns|Byte|Det maximala lagrings utrymmet för den här servern.|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Procent andelen Server logg lagring som används av serverns maximala Server logg lagring.|
|serverlog_storage_usage|Server logg lagring används|Byte|Mängden Server logg lagring som används.|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Den maximala Server logg lagringen för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antalet misslyckade anslutningar till servern.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Mängden lagring av säkerhets kopior som används.|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Fördröjningen i byte mellan huvud servern och den mest isolerings repliken. Detta mått är bara tillgängligt på huvud servern.|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Tiden sedan den senaste återspelade transaktionen. Det här måttet är endast tillgängligt för replik servrar.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera loggning på servern. Dessa resurs loggar kan skickas till [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md), Event Hubs och ett lagrings konto. Läs mer om loggning på sidan [Server loggar](concepts-server-logs.md) .

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) håller reda på frågornas prestanda över tid, inklusive körnings statistik för frågor och vänta-händelser. Funktionen fortsätter att köra prestanda information för kör tid i en system databas med namnet **azure_sys** under schemat för query_store. Du kan styra insamling och lagring av data via olika konfigurations rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) arbetar tillsammans med Query Store för att tillhandahålla visualiseringar som är tillgängliga från Azure Portal. Med de här diagrammen kan du identifiera viktiga frågor som påverkar prestanda. Query Performance Insight kan nås från avsnittet **support och fel sökning** på din Azure Database for PostgreSQL servers Portal sida.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [prestanda rekommendationer](concepts-performance-recommendations.md) identifierar möjligheter att förbättra arbets Belastningens prestanda. Prestanda rekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbets belastningar. För att skapa index rekommendationer tar funktionen hänsyn till olika databas egenskaper, inklusive schema och arbets belastningen som rapporteras av Frågearkivet. När du har implementerat en prestanda rekommendation bör kunderna testa prestanda för att utvärdera effekten av dessa ändringar. 

## <a name="planned-maintenance-notification"></a>Meddelande om planerat underhåll

Med **planerade underhålls aviseringar** kan du få aviseringar om kommande planerat underhåll till din Azure Database for PostgreSQL-enskild server. Dessa meddelanden är integrerade med [service Health](../service-health/overview.md) planerat underhåll och gör att du kan visa alla schemalagda underhåll för dina prenumerationer på ett och samma ställe. Den hjälper också till att skala meddelandet till rätt mål grupper för olika resurs grupper, eftersom du kan ha olika kontakter som är ansvariga för olika resurser. Du får ett meddelande om kommande underhåll 72 timmar före händelsen.

> [!Note]
> Vi kommer att göra varje försök att tillhandahålla **planerat underhåll meddelande** 72 timmar meddelande om alla händelser. I fall av kritiska uppdateringar eller säkerhets korrigeringar kan du dock skicka meddelanden närmare händelsen eller utelämnas.

### <a name="to-receive-planned-maintenance-notification"></a>För att få ett meddelande om planerat underhåll

1. I [portalen](https://portal.azure.com)väljer du **service Health**.
2. I avsnittet **aviseringar** väljer du **hälso aviseringar**.
3. Välj **+ Lägg till tjänstens hälso tillstånds avisering** och fyll i fälten.
4. Fyll i de obligatoriska fälten. 
5. Välj **händelse typ**, Välj **planerat underhåll** eller **Välj alla**
6. I **Åtgärds grupper** definierar du hur du vill få aviseringen (få ett e-postmeddelande, utlösa en Logic app osv.)  
7. Se till att aktivera regel vid skapande har angetts till Ja.
8. Välj **skapa aviserings regel** för att slutföra aviseringen

Detaljerade anvisningar om hur du skapar **tjänstens hälso aviseringar**finns i [skapa aktivitets logg aviseringar för tjänst meddelanden](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Meddelanden om planerade underhåll är för närvarande tillgängliga i för hands versionen i alla regioner **utom** västra centrala USA

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- Mer information om hur du får åtkomst till och exporterar mått med hjälp av Azure Portal, REST API eller CLI finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg om [bästa praxis för att övervaka servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
