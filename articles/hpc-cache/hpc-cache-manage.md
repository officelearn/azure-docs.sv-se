---
title: Hantera och uppdatera Azure HPC-cache
description: Hantera och uppdatera Azure HPC-cache med Azure-portalen
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537958"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Hantera cacheminnet från Azure-portalen

Cacheöversiktssidan i Azure-portalen visar projektinformation, cachestatus och grundläggande statistik för cacheminnet. Den har också kontroller för att stoppa eller starta cacheminnet, ta bort cachen, spola data till långsiktig lagring och uppdatera programvara.

Om du vill öppna översiktssidan väljer du cacheresursen i Azure-portalen. Läs till exempel in sidan **Alla resurser** och klicka på cachenamnet.

![skärmbild av en översiktssida för Azure HPC-cacheinstans](media/hpc-cache-overview.png)

Knapparna högst upp på sidan kan hjälpa dig att hantera cacheminnet:

* **Start** och [**stopp**](#stop-the-cache) - Pausar cacheåtgärd
* [**Flush**](#flush-cached-data) - Skriver ändrade data till lagringsmål
* [**Uppgradering**](#upgrade-cache-software) - Uppdaterar cacheprogramvaran
* **Uppdatera** - Laddar om översiktssidan
* [**Ta bort**](#delete-the-cache) - Permanent förstör cacheminnet

Läs mer om dessa alternativ nedan.

## <a name="stop-the-cache"></a>Stoppa cacheminnet

Du kan stoppa cacheminnet för att minska kostnaderna under en inaktiv period. Du debiteras inte för drifttid medan cacheminnet stoppas, men du debiteras för cachens allokerade disklagring. (Mer [pricing](https://aka.ms/hpc-cache-pricing) information finns på prissidan.)

En stoppad cache svarar inte på klientbegäranden. Du bör avmontera klienter innan du stoppar cacheminnet.

**Stopp-knappen** pausar en aktiv cache. **Stoppknappen** är tillgänglig när en cachestatus är **Felfri** eller **Försämrad**.

![skärmdump av de översta knapparna med Stopp markerat och ett popup-meddelande som beskriver stoppåtgärden och frågar "vill du fortsätta?" med knapparna Ja (standard) och Inga](media/stop-cache.png)

När du klickar på Ja för att bekräfta att cacheminnet stoppas töms innehållet automatiskt i lagringsmålen. Den här processen kan ta lite tid, men den säkerställer datakonsekvens. Slutligen ändras cachestatusen till **Stoppad**.

Om du vill återaktivera en stoppad cache klickar du på **Start-knappen.** Ingen bekräftelse behövs.

![skärmbild av de översta knapparna med Start markerad](media/start-cache.png)

## <a name="flush-cached-data"></a>Rensa cachelagrade data

Knappen **Spolning** på översiktssidan talar om för cacheminnet att omedelbart skriva alla ändrade data som lagras i cacheminnet till backend-lagringsmålen. Cachen sparar rutinmässigt data till lagringsmålen, så det är inte nödvändigt att göra detta manuellt om du inte vill se till att backend-lagringssystemet är uppdaterat. Du kan till exempel använda **Flush** innan du tar en ögonblicksbild av lagring eller kontrollerar datauppsättningsstorleken.

> [!NOTE]
> Under tömningsprocessen kan cachen inte betjäna klientbegäranden. Cacheåtkomsten pausas och återupptas när åtgärden har slutförts.

![skärmbild av de översta knapparna med Flush markerat och ett popup-meddelande som beskriver färgåtgärden och frågar "vill du fortsätta?" med knapparna Ja (standard) och Inga](media/hpc-cache-flush.png)

När du startar cachens infällningsåtgärd slutar cachen att acceptera klientbegäranden och cachestatusen på översiktssidan ändras till **Tömning**.

Data i cacheminnet sparas i lämpliga lagringsmål. Beroende på hur mycket data som behöver rensas kan processen ta några minuter eller över en timme.

När alla data har sparats i lagringsmål börjar cachen automatiskt att ta klientbegäranden igen. Cachestatusen återgår till **Felfri**.

## <a name="upgrade-cache-software"></a>Uppgradera cacheprogram

Om det finns en ny programvaruversion blir **uppgraderingsknappen** aktiv. Du bör också se ett meddelande högst upp på sidan om att uppdatera programvara.

![skärmbild av den översta raden med knappar med knappen Uppgradera aktiverad](media/hpc-cache-upgrade-button.png)

Klientåtkomsten avbryts inte under en uppgradering av programvaran, men cacheprestandan försämras. Planera att uppgradera programvara under icke-rusningstid eller under en planerad underhållsperiod.

Programuppdateringen kan ta flera timmar. Cacheminnen som konfigurerats med högre dataflöde tar längre tid att uppgradera än cacheminnen med mindre toppdataflödesvärden.

När en uppgradering av programvaran är tillgänglig har du en vecka eller så att tillämpa den manuellt. Slutdatumet visas i uppgraderingsmeddelandet. Om du inte uppgraderar under den tiden tillämpar Azure automatiskt uppdateringen på din cache. Tidpunkten för den automatiska uppgraderingen kan inte konfigureras. Om du är orolig för cacheprestandapåverkan bör du uppgradera programvaran själv innan tidsperioden löper ut.

Om cacheminnet stoppas när slutdatumet går, uppgraderar cachen automatiskt programvara nästa gång den startas. (Uppdateringen kanske inte startar omedelbart, men den startar under den första timmen.)

Klicka på **knappen Uppgradera** för att starta programuppdateringen. Cachestatusen ändras till **Uppgradering** tills åtgärden är klar.

## <a name="delete-the-cache"></a>Ta bort cacheminnet

Knappen **Ta bort** förstör cacheminnet. När du tar bort en cache förstörs alla dess resurser och ådrar sig inte längre kontoavgifter.

De backend-lagringsvolymer som används som lagringsmål påverkas inte när du tar bort cachen. Du kan lägga till dem i en framtida cache senare eller inaktivera dem separat.

> [!NOTE]
> Azure HPC Cache skriver inte automatiskt ändrade data från cacheminnet till backend-lagringssystemen innan cachen tas bort.
>
> Om du vill vara säker på att alla data i cacheminnet har skrivits till långtidslagring [stoppar du cachen](#stop-the-cache) innan du tar bort den. Kontrollera att statusen **har stoppats** innan du klickar på borttagningsknappen.

## <a name="cache-metrics-and-monitoring"></a>Cachemått och övervakning

Översiktssidan visar diagram för grundläggande cachestatistik - cachedataflöde, åtgärder per sekund och svarstid.

![skärmdump av tre linjediagram som visar den statistik som nämns ovan för ett urval cache](media/hpc-cache-overview-stats.png)

Dessa diagram är en del av Azures inbyggda övervaknings- och analysverktyg. Ytterligare verktyg och varningar finns tillgängliga från sidorna under rubriken **Övervakning** i portalens sidofält. Läs mer i portalavsnittet i [Azure Monitoring-dokumentationen](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure-mått och statistikverktyg](../azure-monitor/index.yml)
* Få [hjälp med din Azure HPC-cache](hpc-cache-support-ticket.md)
