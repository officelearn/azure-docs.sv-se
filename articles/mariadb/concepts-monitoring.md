---
title: Övervakning i Azure-databas för MariaDB
description: Den här artikeln beskriver mått för övervakning och avisering för Azure Database for MariaDB, inklusive statistik för CPU-, lagrings- och anslutning.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: fb998edffed290bb7bc59945163f0fd48c55cbf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612534"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Övervakning i Azure-databas för MariaDB
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. Azure Database for MariaDB tillhandahåller olika mått som ger inblick i beteendet för din server.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut. varje mått ger 30 dagars historik. Du kan konfigurera aviseringar om måtten. Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Stegvisa anvisningar finns i [hur du konfigurerar aviseringar](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MariaDB:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Procentandelen av Processorn som används.|
|memory_percent|Minne|Percent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Percent|Procentandelen av i/o som används.|
|storage_percent|Lagringsprocent|Percent|Procentandelen av lagring som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Lagring som används av tjänsten kan omfatta databasfiler och transaktionsloggar serverloggarna.|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Percent|Procentandelen av server logglagring som används av serverns maximala server log lagring.|
|serverlog_storage_usage|Server logglagring som används|Byte|Mängden log-serverlagring används.|
|serverlog_storage_limit|Log storage gränsen|Byte|Det maximala server log lagringsutrymmet för den här servern.|
|storage_limit|Gränsen för lagring|Byte|Det maximala lagringsutrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Count|Antal aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Count|Antal misslyckade anslutningar till servern.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i över aktiva anslutningar.|

## <a name="server-logs"></a>Serverloggar

Du kan aktivera långsamma fråga loggning på servern. Dessa loggar är också tillgängliga via Azure-diagnostikloggar i Azure Monitor-loggar, Händelsehubbar och Storage-konto. Mer information om loggning finns i [serverloggar](concepts-server-logs.md) sidan.

## <a name="query-store"></a>Query Store

[Query Store](concepts-query-store.md) är en funktion i offentlig förhandsversion som håller reda på frågan prestanda över tid, inklusive fråga efter körningsstatistik och vänta händelser. Funktionen kvarstår fråga information om körningsprestanda i den **mysql** schema. Du kan styra insamling och lagring av data via olika configuration rattar.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) fungerar tillsammans med Query Store att tillhandahålla visualiseringar som är tillgängliga från Azure-portalen. Dessa diagram kan du identifiera viktiga frågor som påverkas prestanda. Query Performance Insight är allmänt tillgänglig förhandsversion och är tillgänglig i den **Intelligent prestanda** delen av din Azure Database for MariaDB server portalsidan.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Den [Prestandarekommendationer](concepts-performance-recommendations.md) funktionen identifierar möjligheter att förbättra prestanda för arbetsbelastningen. Den offentliga förhandsversionen av Prestandarekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbetsbelastningar. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Se [hur du konfigurerar aviseringar](howto-alert-metric.md) anvisningar om hur du skapar en avisering på ett mått.
