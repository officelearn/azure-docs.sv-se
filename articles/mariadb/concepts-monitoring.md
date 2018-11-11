---
title: Övervakning i Azure-databas för MariaDB
description: Den här artikeln beskriver mått för övervakning och avisering för Azure Database for MariaDB, inklusive statistik för CPU-, lagrings- och anslutning.
services: mariadb
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/10/2018
ms.openlocfilehash: 3daa4eb07e626e53d9106abf978174f505e785ab
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516276"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Övervakning i Azure-databas för MariaDB
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. Azure Database for MariaDB tillhandahåller olika mått som ger inblick i beteendet för din server.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut. varje mått ger 30 dagars historik. Du kan konfigurera aviseringar om måtten. Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i [Azure mått översikt] (.. /Monitoring-and-Diagnostics/Monitoring-Overview-Metrics.MD).

<!--For step by step guidance, see [How to set up alerts](howto-alert-on-metric.md). -->

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MariaDB:

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
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera långsamma fråga loggning på servern. Mer information om loggning finns i [serverloggar](concepts-server-logs.md) sidan.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<!-- - See [How to set up alerts](howto-alert-on-metric.md) for guidance on creating an alert on a metric.-->