---
title: Skala servergrupp – hyperskala (Citus) - Azure-databas för PostgreSQL
description: Justera servergruppsminne, disk och CPU-resurser för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063103"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-servergrupp (Hyperscale)

Azure Database for PostgreSQL - Hyperscale (Citus) ger självbetjäningsskalning för att hantera ökad belastning. Azure-portalen gör det enkelt att lägga till nya arbetsnoder och att öka virtuella kärnor för befintliga noder.

## <a name="add-worker-nodes"></a>Lägg till arbetsnoder

Om du vill lägga till noder går du till fliken **Konfigurera** i servergruppen Hyperskala (Citus).  Om du drar skjutreglaget för **antalet arbetsnoder** ändras värdet.

![Reglage för resurser](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klicka på knappen **Spara** om du vill att det ändrade värdet ska börja gälla.

> [!NOTE]
> När det har ökats och sparats kan antalet arbetsnoder inte minskas med hjälp av skjutreglaget.

### <a name="rebalance-shards"></a>Balansera om shards

Om du vill dra nytta av nyligen tillagda noder måste du balansera om delade [tabellsvantar,](concepts-hyperscale-distributed-data.md#shards)vilket innebär att flytta vissa shards från befintliga noder till de nya. Kontrollera först att de nya arbetarna har slutfört etableringen. Starta sedan fragmentbalansen genom att ansluta till klusterkoordinatornsnoden med psql och köra:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funktionen `rebalance_table_shards` balanserar om alla tabeller i [samlokaliseringsgruppen](concepts-hyperscale-colocation.md) i tabellen som nämns i dess argument. Således behöver du inte anropa funktionen för varje distribuerad tabell, bara kalla det på en representativ tabell från varje samlokaliseringsgrupp.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Öka eller minska virtuella kärnor på noder

> [!NOTE]
> Den här funktionen är för närvarande en förhandsversion. Om du vill begära en ändring av virtuella kärnor för noder i servergruppen kontaktar du [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Förutom att lägga till nya noder kan du öka funktionerna för befintliga noder. Att justera beräkningskapacitet upp och ned kan vara användbart för prestandaexperiment samt kortsiktiga eller långsiktiga ändringar av trafikkraven.

Om du vill ändra vCore för alla arbetsnoder justerar du skjutreglaget **vCores** under **Konfiguration (per arbetsnod).** Koordinatorns nods virtuella kärnor kan justeras oberoende av dem. Klicka på länken **Ändra konfiguration** under **koordinatornoden**. En dialogruta visas med skjutreglage för samordnarens virtuella kärnor och lagringskapacitet. Ändra skjutreglagen efter behov och välj **OK**.

## <a name="next-steps"></a>Nästa steg

Läs mer om [prestandaalternativ](concepts-hyperscale-configuration-options.md)för servergrupp .
