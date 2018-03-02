---
title: "Övervakning i Azure-databas för PostgreSQL"
description: "Den här artikeln beskriver mätvärden för övervakning och avisering för Azure-databas för PostgreSQL, inklusive CPU, lagring och anslutningen statistik."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d0a57fe6d7b1040c32f6d67e2bf0259176c72099
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Övervakning i Azure-databas för PostgreSQL
Övervakning av data om dina servrar hjälper dig att felsöka och optimera för din arbetsbelastning. Azure-databas för PostgreSQL innehåller olika mått som ger inblick i beteendet för de resurser som stödjer PostgreSQL-server. 

## <a name="metrics"></a>Mått
Alla mätvärden för Azure har en minut frekvens och varje mått ger 30 dagar tidigare. Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns [hur du konfigurerar aviseringar](howto-alert-on-metric.md). Andra uppgifter inkluderar ställa in automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i [översikt över Azure mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Listan över mått
De här måtten är tillgängliga för Azure-databas för PostgreSQL:

|Mått|Mått visningsnamn|Enhet|Beskrivning|
|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn används.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagringsutrymme som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Det lagringsutrymme som används av tjänsten innehåller databasfiler och transaktionsloggar i serverloggen.|
|storage_limit|Lagringsgränsen|Byte|Maximalt lagringsutrymme för den här servern.|
|active_connections|Totalt antal aktiva anslutningar|Antal|Antal aktiva anslutningar till servern.|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Antal misslyckade anslutningar till servern.|


## <a name="next-steps"></a>Nästa steg
- Se [hur du konfigurerar aviseringar](howto-alert-on-metric.md) anvisningar om hur du skapar en avisering på ett mått.
- Mer information om hur du åt och exportera mått med Azure-portalen, REST-API eller CLI finns i [översikt över Azure mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
