---
title: Övervaka och justera – Hyperskala (Citus) - Azure-databas för PostgreSQL
description: I den här artikeln beskrivs övervaknings- och justeringsfunktioner i Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975524"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Övervaka och finjustera Azure Database för PostgreSQL - Hyperskala (Citus)

Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbetsbelastning. Hyperskala (Citus) innehåller olika övervakningsalternativ för att ge insikt i hur noder i en servergrupp uppstår.

## <a name="metrics"></a>Mått

Hyperskala (Citus) ger mått för varje nod i en servergrupp. Måtten ger insikt i hur du stöder resurser. Varje mått avges med en minuts frekvens och har upp till 30 dagars historia.

Förutom att visa diagram över måtten kan du konfigurera aviseringar. Steg för steg-vägledning finns i [Så här ställer du in aviseringar](howto-hyperscale-alert-on-metric.md).  Andra uppgifter är att konfigurera automatiserade åtgärder, köra avancerad analys och arkiveringshistorik. Mer information finns i [översikten över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått

Dessa mått är tillgängliga för Citus-noder (Hyperscale):

|Mått|Namn på mätdisplay|Enhet|Beskrivning|
|---|---|---|---|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|cpu_percent|CPU-procent|Procent|Procentandelen processor som används.|
|Iops|IOPS|Antal|Se [IOPS-definitionen](../virtual-machines/linux/premium-storage-performance.md#iops) och [hyperskaladataflödet](concepts-hyperscale-configuration-options.md)|
|memory_percent|Minne procent|Procent|Procentandelen minne som används.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i aktiva anslutningar.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|storage_percent|Lagringsprocent|Procent|Den procentandel lagringsutrymme som används av serverns maximala.|
|storage_used|Användning av lagring|Byte|Mängden lagringsutrymme som används. Den lagring som används av tjänsten kan innehålla databasfiler, transaktionsloggar och serverloggar.|

Azure tillhandahåller inga aggregerade mått för klustret som helhet, men mått för flera noder kan placeras i samma diagram.

## <a name="next-steps"></a>Nästa steg

- Se [hur du ställer in aviseringar](howto-hyperscale-alert-on-metric.md) för vägledning om hur du skapar en avisering för ett mått.
