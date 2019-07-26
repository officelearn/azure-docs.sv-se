---
title: Övervakning i Azure Database for MySQL
description: Den här artikeln beskriver måtten för övervakning och aviseringar för Azure Database for MySQL, inklusive CPU, lagring och anslutnings statistik.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "67062434"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Övervakning i Azure Database for MySQL
Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Azure Database for MySQL tillhandahåller olika mått som ger inblick i serverns beteende.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Steg för steg-anvisningar finns i [så här konfigurerar du aviseringar](howto-alert-on-metric.md). Andra uppgifter är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MySQL:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Procent andelen CPU som används.|
|memory_percent|Minnes procent|Percent|Procent andelen minne som används.|
|io_consumption_percent|I/o procent|Percent|Procent andelen av IO som används.|
|storage_percent|Lagrings procent|Percent|Procent andelen lagring som används av serverns högsta värde.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
|serverlog_storage_percent|Server logg lagrings procent|Percent|Procent andelen Server logg lagring som används av serverns maximala Server logg lagring.|
|serverlog_storage_usage|Server logg lagring används|Byte|Mängden Server logg lagring som används.|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Den maximala Server logg lagringen för den här servern.|
|storage_limit|Lagrings gräns|Byte|Det maximala lagrings utrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Count|Antalet aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Count|Antalet misslyckade anslutningar till servern.|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Count|Antalet sekunder som replik servern avbildar mot huvud servern.|
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Mängden lagring av säkerhets kopior som används.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera långsam fråga och gransknings loggning på servern. Dessa loggar är också tillgängliga via Azure-diagnostikloggar i Azure Monitor loggar, Event Hubs och lagrings konto. Mer information om loggning finns i artiklarna [gransknings loggar](concepts-audit-logs.md) och [långsam frågekörning](concepts-server-logs.md) .

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) är en offentlig förhands gransknings funktion som håller koll på frågans prestanda över tid, inklusive statistik för körning av frågor och vänta-händelser. Funktionen fortsätter att köra prestanda information om körningen i **MySQL** -schemat. Du kan styra insamling och lagring av data via olika konfigurations rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) arbetar tillsammans med Query Store för att tillhandahålla visualiseringar som är tillgängliga från Azure Portal. Med de här diagrammen kan du identifiera viktiga frågor som påverkar prestanda. Query Performance Insight finns i en offentlig för hands version och är tillgänglig i avsnittet **intelligent prestanda** på Azure Database for MySQL servers Portal sida.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [prestanda rekommendationer](concepts-performance-recommendations.md) identifierar möjligheter att förbättra arbets Belastningens prestanda. Den offentliga för hands versionen av prestanda rekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra arbets Belastningens prestanda. För att skapa index rekommendationer tar funktionen hänsyn till olika databas egenskaper, inklusive schema och arbets belastningen som rapporteras av Frågearkivet. När du har implementerat en prestanda rekommendation bör kunderna testa prestanda för att utvärdera effekten av dessa ändringar.

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- Mer information om hur du får åtkomst till och exporterar mått med hjälp av Azure Portal, REST API eller CLI finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg om [bästa praxis för att övervaka servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
