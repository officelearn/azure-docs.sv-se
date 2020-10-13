---
title: Skalnings Server grupp – storskalig (citus)-Azure Database for PostgreSQL
description: Justera minne, disk och processor resurser i Server gruppen för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: dd59d0b09a28febfc0afe35d9f008ba0e0ee19ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295722"
---
# <a name="server-group-size"></a>Servergruppstorlek

Citus-distributions alternativet använder sig av samdrifts databas servrar för att parallellisera frågekörning och lagra mer data. Server gruppens storlek avser både antalet servrar och maskin varu resurserna för var och en.

## <a name="picking-initial-size"></a>Start storlek för plockning

Storleken på en Server grupp, vad gäller antal noder och deras maskin varu kapacitet, är enkelt att ändra ([se nedan](#scale-a-hyperscale-citus-server-group)). Du behöver dock fortfarande välja en ursprunglig storlek för en ny server grupp. Här följer några tips för ett rimligt val.

### <a name="multi-tenant-saas-use-case"></a>SaaS för flera innehavare Use-Case

För de som migreras till storskalig (citus) från en befintlig instans av en PostgreSQL-databas, rekommenderar vi att du väljer ett kluster där antalet arbets virtuella kärnor och RAM-minne totalt är lika med den ursprungliga instansen. I sådana scenarier har vi sett prestanda förbättringar på 2 – 3 – 3 gånger eftersom horisontell Partitionering förbättrar resursutnyttjande och ger mindre index osv.

Antalet virtuella kärnor som krävs för koordinator-noden beror på din befintliga arbets belastning (Skriv-/läsnings data flöde). Koordinator-noden kräver inte lika mycket RAM som arbetsnoder, men RAM-allokering bestäms baserat på antalet vCore (enligt beskrivningen i [konfigurations alternativen för citus)](concepts-hyperscale-configuration-options.md)så att antalet vCore är i grunden det faktiska beslutet.

### <a name="real-time-analytics-use-case"></a>Real-Time analys Use-Case

Totalt antal virtuella kärnor: när du arbetar med data i RAM-minnet kan du förvänta en linjär prestanda förbättring på storskaligheten (citus) som är proportionell mot antalet arbets kärnor. Om du vill fastställa rätt antal virtuella kärnor för dina behov kan du tänka på den aktuella svars tiden för frågor i databasen med en nod och den svars tid som krävs i storskalig (citus). Dela den nuvarande svarstiden med den önskade svarstiden och avrunda resultatet.

RAM-arbetsminne: det bästa är att ha så mycket minne att majoriteten av arbetsuppsättningen får plats i minnet. Vilken typ av frågor som programmet använder påverkar minnes kraven. Du kan köra FÖRKLARINGs analys av en fråga för att avgöra hur mycket minne som krävs. Kom ihåg att virtuella kärnor och RAM skalas tillsammans enligt beskrivningen i artikeln om [konfigurations alternativ för storskaliga (citus)](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-Server grupp

Azure Database for PostgreSQL-Scale (citus) tillhandahåller självbetjänings skalning för att hantera ökad belastning. Azure Portal gör det enkelt att lägga till nya arbetsnoder och öka virtuella kärnor för befintliga noder. Att lägga till noder medför ingen stillestånds tid, och till och med att flytta Shards till de nya noderna (kallas [Shard balansering](#rebalance-shards)) utan att avbryta frågor.

### <a name="add-worker-nodes"></a>Lägg till arbetsnoder

Om du vill lägga till noder går du till fliken **Compute + Storage** i Server gruppen för citus-servern.  Om du drar skjutreglaget för **antal arbets noder** ändras värdet.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Resurs skjutreglage":::

Klicka på knappen **Spara** om du vill att det ändrade värdet ska börja gälla.

> [!NOTE]
> När du har ökat och sparat det går det inte att minska antalet arbetsnoder med skjutreglaget.

#### <a name="rebalance-shards"></a>Balansera om Shards

Om du vill dra nytta av nyligen tillagda noder måste du balansera om Distributed Table [Shards](concepts-hyperscale-distributed-data.md#shards), vilket innebär att vissa Shards flyttas från befintliga noder till de nya. Kontrol lera först att de nya arbets tagarna har slutfört etableringen. Starta sedan Shard-ombalanseringen genom att ansluta till noden kluster koordinatorer med psql och köra:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`Funktionen balanserar alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Öka eller minska virtuella kärnor på noder

Förutom att lägga till nya noder kan du öka funktionerna i befintliga noder. Det kan vara bra att justera beräknings kapaciteten för prestanda experiment samt kortsiktiga eller långsiktiga ändringar av trafik kraven.

Om du vill ändra virtuella kärnor för alla arbetsnoder justerar du skjutreglaget **virtuella kärnor** under **konfiguration (per nod)**. Koordinator nodens virtuella kärnor kan justeras oberoende av varandra. Justera **virtuella kärnor** -skjutreglaget under  **konfiguration (koordinator nod)**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
