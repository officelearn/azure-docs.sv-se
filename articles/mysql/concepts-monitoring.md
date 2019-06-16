---
title: Övervakning i Azure Database for MySQL
description: Den här artikeln beskriver mått för övervakning och avisering för Azure Database för MySQL, inklusive statistik för CPU-, lagrings- och anslutning.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062434"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Övervakning i Azure Database for MySQL
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. Azure Database for MySQL tillhandahåller olika mått som ger inblick i beteendet för din server.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut. varje mått ger 30 dagars historik. Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns i [hur du konfigurerar aviseringar](howto-alert-on-metric.md). Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MySQL:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn som används.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o som används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagring som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Lagring som används av tjänsten kan omfatta databasfiler och transaktionsloggar serverloggarna.|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Procentandelen av server logglagring som används av serverns maximala server log lagring.|
|serverlog_storage_usage|Server logglagring som används|Byte|Mängden log-serverlagring används.|
|serverlog_storage_limit|Log storage gränsen|Byte|Det maximala server log lagringsutrymmet för den här servern.|
|storage_limit|Gränsen för lagring|Byte|Det maximala lagringsutrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Count|Antal aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Count|Antal misslyckade anslutningar till servern.|
|seconds_behind_master|Replikeringsfördröjning i sekunder|Count|Hur många sekunder som replikservern släpar mot huvudservern.|
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Mängden lagring av säkerhetskopior används.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera långsamma fråga och granskningsloggning på servern. Dessa loggar är också tillgängliga via Azure-diagnostikloggar i Azure Monitor-loggar, Händelsehubbar och Storage-konto. Mer information om loggning finns i [granskningsloggar](concepts-audit-logs.md) och [långsamma frågeloggar](concepts-server-logs.md) artiklar.

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) är en funktion i offentlig förhandsversion som håller reda på frågan prestanda över tid, inklusive fråga efter körningsstatistik och vänta händelser. Funktionen kvarstår fråga information om körningsprestanda i den **mysql** schema. Du kan styra insamling och lagring av data via olika configuration rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fungerar tillsammans med Query Store att tillhandahålla visualiseringar som är tillgängliga från Azure-portalen. Dessa diagram kan du identifiera viktiga frågor som påverkas prestanda. Query Performance Insight är allmänt tillgänglig förhandsversion och är tillgänglig i den **Intelligent prestanda** delen av din Azure Database for MySQL-server portalsidan.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den [Prestandarekommendationer](concepts-performance-recommendations.md) funktionen identifierar möjligheter att förbättra prestanda för arbetsbelastningen. Den offentliga förhandsversionen av Prestandarekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbetsbelastningar. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna.

## <a name="next-steps"></a>Nästa steg
- Se [hur du konfigurerar aviseringar](howto-alert-on-metric.md) anvisningar om hur du skapar en avisering på ett mått.
- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg på [bästa praxis när du övervakar servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
