---
title: "Övervakning i Azure-databas för MySQL | Microsoft Docs"
description: "Den här artikeln beskriver mätvärden för övervakning och avisering för Azure-databas för MySQL, inklusive CPU, gränser, lagring och anslutningsstatistik."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 9af447d54faa8ee96e4b79beb274b437eea57626
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Övervakning i Azure-databas för MySQL
Övervakning av data om dina servrar hjälper dig att felsöka och optimera för din arbetsbelastning. Azure-databas för MySQL innehåller olika mått som ger inblick i beteendet för de resurser som stödjer MySQL-servern. 

## <a name="metrics"></a>Mått
Alla mätvärden för Azure har en minut frekvens och varje mått ger 30 dagar tidigare. 

Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns [hur du konfigurerar aviseringar](howto-alert-on-metric.md). 

Andra uppgifter inkluderar ställa in automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i [översikt över Azure mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Listan över mått
De här måtten är tillgängliga för Azure-databas för MySQL:

|Mått|Mått visningsnamn|Enhet|Beskrivning|
|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn används.|
|compute_limit|Compute-gränsen för enhet|Antal|Den här servern maximala antalet beräknings-enheter|
|compute_consumption_percent|Beräkna procentandelen enhet|Procent|Procentandelen beräknings-enheter som används av servern är maximalt.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagringsutrymme som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Det lagringsutrymme som används av tjänsten innehåller databasfiler och transaktionsloggar i serverloggen.|
|storage_limit|Lagringsgränsen|Byte|Maximalt lagringsutrymme för den här servern.|
|active_connections|Totalt antal aktiva anslutningar|Antal|Antal aktiva anslutningar till servern.|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Antal misslyckade anslutningar till servern.|


> [!NOTE]
> Beräkna enhet består av minne och processor. Beräkna enhet procentandelen är max (% minne, cpu i %). Granska minne och cpu-diagram för att identifiera vilket bidrar till Compute enhet procenttal ändras. Mer information finns i [compute enheter](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Nästa steg
- Stegvisa anvisningar finns [hur du konfigurerar aviseringar](howto-alert-on-metric.md). 
- Mer information om hur du åt och exportera mått med Azure-portalen, REST-API eller CLI finns i [översikt över Azure mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
