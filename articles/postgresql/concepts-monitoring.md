---
title: Övervaka och finjustera – Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs övervaknings- och justeringsfunktioner i Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252574"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Övervaka och finjustera Azure Database för PostgreSQL - Single Server
Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbetsbelastning. Azure Database for PostgreSQL innehåller olika övervakningsalternativ för att ge insikt i serverns beteende.

## <a name="metrics"></a>Mått
Azure Database for PostgreSQL tillhandahåller olika mått som ger insikt i beteendet hos de resurser som stöder PostgreSQL-servern. Varje mått avges med en minuts frekvens och har upp till 30 dagars historia. Du kan konfigurera aviseringar på måtten. Steg för steg-vägledning finns i [Så här ställer du in aviseringar](howto-alert-on-metric.md). Andra uppgifter är att konfigurera automatiserade åtgärder, utföra avancerad analys och arkiveringshistorik. Mer information finns i [översikten över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
Dessa mått är tillgängliga för Azure Database för PostgreSQL:

|Mått|Namn på mätdisplay|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen processor som används.|
|memory_percent|Minne procent|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO procent|Procent|Procentandelen I/o som används.|
|storage_percent|Lagringsprocent|Procent|Den procentandel lagringsutrymme som används av serverns maximala.|
|storage_used|Användning av lagring|Byte|Mängden lagringsutrymme som används. Den lagring som används av tjänsten kan innehålla databasfiler, transaktionsloggar och serverloggar.|
|storage_limit|Lagringsgräns|Byte|Maximal lagring för den här servern.|
|serverlog_storage_percent|Lagringsprocent för serverlogg|Procent|Procentandelen serverlogglagring som används av serverns maximala serverlogglagring.|
|serverlog_storage_usage|Serverlogglagring används|Byte|Mängden serverlogglagring som används.|
|serverlog_storage_limit|Lagringsgräns för serverlogg|Byte|Den maximala serverlogglagringen för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antalet misslyckade anslutningar till servern.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i aktiva anslutningar.|
|backup_storage_used|Lagring för säkerhetskopiering används|Byte|Mängden lagringsutrymme för säkerhetskopiering som används.|
|pg_replica_log_delay_in_bytes|Max fördröjning över repliker|Byte|Fördröjningen i byte mellan mastern och den mest eftersläpande repliken. Det här måttet är endast tillgängligt på huvudservern.|
|pg_replica_log_delay_in_seconds|Replik lag|Sekunder|Tiden sedan den senast återspelade transaktionen. Det här måttet är endast tillgängligt för replikservrar.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera loggning på servern. Dessa loggar är också tillgängliga via Azure Diagnostic Logs i [Azure Monitor loggar,](../azure-monitor/log-query/log-query-overview.md)eventhubbar och lagringskonto. Mer information om loggning finns på [sidan serverloggar.](concepts-server-logs.md)

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) håller reda på frågeprestanda över tid, inklusive frågekörningsstatistik och vänta-händelser. Funktionen beständig prestandainformation för frågekörning i en systemdatabas med namnet **azure_sys** under det query_store schemat. Du kan styra insamling och lagring av data via olika konfigurationsrattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fungerar tillsammans med Query Store för att tillhandahålla visualiseringar som är tillgängliga från Azure-portalen. Med dessa diagram kan du identifiera viktiga frågor som påverkar prestanda. Query Performance Insight är tillgängligt från avsnittet **Support + felsökning** i portalsidan för Azure-databasen för PostgreSQL-servern.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) identifierar möjligheter att förbättra arbetsbelastningens prestanda. Prestandarekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestanda för dina arbetsbelastningar. För att ta fram indexrekommendationer tar funktionen hänsyn till olika databasegenskaper, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du har implementerat en prestandarekommendation bör kunderna testa prestanda för att utvärdera effekten av dessa ändringar. 

## <a name="planned-maintenance-notification"></a>Anmälan om planerat underhåll

**Med planerade underhållsmeddelanden** kan du få aviseringar om kommande planerat underhåll till din Azure-databas för PostgreSQL - Single Server. Dessa meddelanden är integrerade med [Service Healths](../service-health/overview.md) planerade underhåll och gör att du kan visa allt schemalagt underhåll för dina prenumerationer på ett ställe. Det hjälper också att skala aviseringen till rätt målgrupper för olika resursgrupper, eftersom du kan ha olika kontakter som ansvarar för olika resurser. Du kommer att få meddelandet om det kommande underhållet 72 timmar före evenemanget.

> [!Note]
> Vi kommer att göra varje försök att tillhandahålla **planerad underhåll anmälan** 72 timmars varsel för alla händelser. I fall av kritiska eller säkerhetskorrigeringar kan dock meddelanden skickas närmare händelsen eller utelämnas.

### <a name="to-receive-planned-maintenance-notification"></a>Så här får du en anmälan om planerat underhåll

1. Välj **Servicehälsa**i [portalen](https://portal.azure.com).
2. I avsnittet **Aviseringar** väljer du **Hälsovarningar**.
3. Välj **+ Lägg till hälsoavisering för tjänsten** och fyll i fälten.
4. Fyll i de obligatoriska fälten. 
5. Välj **händelsetyp,** välj **Planerat underhåll** eller **Markera alla**
6. I **åtgärdsgrupper** definierar du hur du vill få aviseringen (få ett e-postmeddelande, utlösa en logikapp etc.)  
7. Kontrollera att aktivera regeln när du skapar är inställd på Ja.
8. Välj **Skapa aviseringsregel** för att slutföra aviseringen

Detaljerade steg om hur du skapar **hälsoaviseringar**för tjänsten finns i [Skapa aktivitetsloggaviseringar för tjänstmeddelanden](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Meddelanden om planerat underhåll förhandsgranskas för närvarande

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering för ett mått.
- Mer information om hur du kommer åt och exporterar mått med Azure-portalen, REST API eller CLI finns i [översikten över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg om [bästa praxis för att övervaka din server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
