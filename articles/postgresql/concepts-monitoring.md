---
title: Övervaka och finjustera i Azure Database för PostgreSQL
description: Den här artikeln beskriver övervakning och justering funktioner i Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e29186d07d9a060e45ed051d6f7ed0ac81a5e15b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982672"
---
# <a name="monitor-and-tune"></a>Övervaka och finjustera
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. 

## <a name="metrics"></a>Mått
Azure Database för PostgreSQL innehåller olika mått som ger inblick i beteendet för de resurser som stödjer PostgreSQL-servern. Varje mått har genererats med en frekvens för en minut och har upp till 30 dagars historik. Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns i [hur du konfigurerar aviseringar](howto-alert-on-metric.md). Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database för PostgreSQL:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn som används.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o som används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagring som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Lagring som används av tjänsten kan omfatta databasfiler och transaktionsloggar serverloggarna.|
|storage_limit|Gränsen för lagring|Byte|Det maximala lagringsutrymmet för den här servern.|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Procentandelen av server logglagring som används av serverns maximala server log lagring.|
|serverlog_storage_usage|Server logglagring som används|Byte|Mängden log-serverlagring används.|
|serverlog_storage_limit|Log storage gränsen|Byte|Det maximala server log lagringsutrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antal aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antal misslyckade anslutningar till servern.|
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) är en funktion i offentlig förhandsversion som håller reda på frågan prestanda över tid, inklusive fråga efter körningsstatistik och vänta händelser. Funktionen kvarstår fråga information om körningsprestanda i en databas med namnet **azure_sys** under query_store schemat. Du kan styra insamling och lagring av data via olika configuration rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fungerar tillsammans med Query Store att tillhandahålla visualiseringar som är tillgängliga från Azure-portalen. Dessa diagram kan du identifiera viktiga frågor som påverkas prestanda. Query Performance Insight är allmänt tillgänglig förhandsversion och är tillgänglig i den **Support och felsökning** delen av din Azure Database for PostgreSQL-server portalsidan.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den [Prestandarekommendationer](concepts-performance-recommendations.md) funktionen identifierar möjligheter att förbättra prestanda för arbetsbelastningen. Den offentliga förhandsversionen av Prestandarekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbetsbelastningar. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna. 

## <a name="next-steps"></a>Nästa steg
- Se [hur du konfigurerar aviseringar](howto-alert-on-metric.md) anvisningar om hur du skapar en avisering på ett mått.
- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
