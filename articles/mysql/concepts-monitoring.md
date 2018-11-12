---
title: Övervakning i Azure Database for MySQL
description: Den här artikeln beskriver mått för övervakning och avisering för Azure Database för MySQL, inklusive statistik för CPU-, lagrings- och anslutning.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/05/2018
ms.openlocfilehash: a66311cb35815b5db7724a277677ad41d7afec77
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035741"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Övervakning i Azure Database for MySQL
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. Azure Database for MySQL tillhandahåller olika mått som ger inblick i beteendet för din server.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut. varje mått ger 30 dagars historik. Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns i [hur du konfigurerar aviseringar](howto-alert-on-metric.md). Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MySQL:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn som används.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o som används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagring som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Lagring som används av tjänsten kan omfatta databasfiler och transaktionsloggar serverloggarna.|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Procentandelen av server logglagring som används av serverns maximala server log lagring.|
|serverlog_storage_usage|Server logglagring som används|Byte|Mängden log-serverlagring används.|
|serverlog_storage_limit|Log storage gränsen|Byte|Det maximala server log lagringsutrymmet för den här servern.|
|storage_limit|Gränsen för lagring|Byte|Det maximala lagringsutrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antal aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antal misslyckade anslutningar till servern.|
|seconds_behind_master|Replikeringsfördröjning i sekunder|Antal|Hur många sekunder som replikservern släpar mot huvudservern.|
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Mängden lagring av säkerhetskopior används.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera långsamma fråga loggning på servern. Dessa loggar är också tillgängliga via Azure-diagnostikloggar i OMS Log Analytics, Event Hubs och Storage-konto. Mer information om loggning finns i [serverloggar](concepts-server-logs.md) sidan.

## <a name="next-steps"></a>Nästa steg
- Se [hur du konfigurerar aviseringar](howto-alert-on-metric.md) anvisningar om hur du skapar en avisering på ett mått.
- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg på [bästa praxis när du övervakar servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
