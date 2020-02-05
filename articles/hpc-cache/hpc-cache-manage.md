---
title: Hantera och uppdatera Azure HPC-cache
description: Hantera och uppdatera Azure HPC-cache med hjälp av Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 9ad6348e15c8a25f721a89be7eab3e17c58ae17c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988915"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Hantera din cache från Azure Portal

På sidan cache-översikt i Azure Portal visas projekt information, cache-status och grundläggande statistik för cacheminnet. Den har också kontroller för att stoppa eller starta cacheminnet, ta bort cachen, tömma data till långsiktig lagring och uppdatera program vara.

Öppna översikts sidan genom att välja din cache-resurs i Azure Portal. Läs till exempel sidan **alla resurser** och klicka på cache-namnet.

![skärm bild av översikts sidan för Azure HPC-instansen](media/hpc-cache-overview.png)

Knapparna överst på sidan kan hjälpa dig att hantera cachen:

* **Starta** och [**stoppa**](#stop-the-cache) – inaktiverar cache-åtgärd
* [**Flush**](#flush-cached-data) -skriver ändrade data till lagrings mål
* [**Uppgradera**](#upgrade-cache-software) – uppdaterar cache-programvaran
* **Uppdatera** – Läs in sidan Översikt igen
* [**Ta bort**](#delete-the-cache) – förstör cachen permanent

Läs mer om de här alternativen nedan.

## <a name="stop-the-cache"></a>Stoppa cachen

Du kan stoppa cacheminnet för att minska kostnaderna under en inaktiv period. Du debiteras inte för drift tid medan cachen stoppas, men du debiteras för cachens allokerade disk lagring. (Mer information finns på sidan med [priser](https://aka.ms/hpc-cache-pricing) .)

Ett stoppat cacheminne svarar inte på klient begär Anden. Du bör demontera klienter innan du stoppar cacheminnet.

**Stopp** knappen pausar en aktiv cache. **Stopp** knappen är tillgänglig när statusen för ett cacheminne är **felfri** eller **försämrad**.

![skärm bild av de översta knapparna med stoppa markerat och ett popup-meddelande som beskriver stopp åtgärden och där vill du fortsätta? med Ja (standard) och inga knappar](media/stop-cache.png)

När du har klickat på Ja för att bekräfta att du vill stoppa cacheminnet tömmer cachen automatiskt innehållet till lagrings målen. Den här processen kan ta lite tid, men den garanterar data konsekvens. Slutligen ändras cachens status till **stoppad**.

Om du vill återaktivera en stoppad cache klickar du på knappen **Starta** . Ingen bekräftelse krävs.

![skärm bild av de översta knapparna med start markerat](media/start-cache.png)

## <a name="flush-cached-data"></a>Rensa cachelagrade data

Knappen **Töm** på översikts sidan visar cacheminnet för att omedelbart skriva alla ändrade data som lagras i cacheminnet till backend-lagrings målen. Cachen sparar regelbundet data till lagrings målen, så det är inte nödvändigt att göra detta manuellt, såvida du inte vill se till att Server delens lagrings system är aktuellt. Du kan till exempel använda **Flush** innan du tar en lagrings ögonblicks bild eller kontrollerar data uppsättningens storlek.

> [!NOTE]
> Under tömnings processen kan cachen inte hantera klient begär Anden. Cache-åtkomst är inaktive rad och återupptas när åtgärden har slutförts.

![skärm bild av de översta knapparna med Flush markerat och ett popup-meddelande som beskriver tömnings åtgärden och där du ombeds att fortsätta? med Ja (standard) och inga knappar](media/hpc-cache-flush.png)

När du startar rensningen av cachen slutar cachen att acceptera klient begär Anden och cache-statusen på översikts sidan ändras till **tömning**.

Data i cacheminnet sparas till lämpliga lagrings mål. Beroende på hur mycket data som behöver tömmas kan processen ta några minuter eller mer än en timme.

När alla data har sparats på lagrings målen börjar cachen automatiskt ta emot klient begär Anden. Cache-statusen återgår till **felfritt**.

## <a name="upgrade-cache-software"></a>Uppgradera cache-programvara

Om det finns en ny program varu version blir **uppgraderings** knappen aktiv. Du bör också se ett meddelande längst upp på sidan om uppdatering av program vara.

![skärm bild av den översta raden med knappar med uppgraderings knappen aktive rad](media/hpc-cache-upgrade-button.png)

Klient åtkomst avbryts inte under en program uppgradering, men cache-prestandan går långsamt. Planera för att uppgradera program vara under tider med låg belastning eller under en planerad underhålls period.

Program uppdateringen kan ta flera timmar. Cacheminnen som kon figurer ATS med högre genomflöde tar längre tid att uppgradera än cacheminnen med mindre högsta data flödes värden.

När en program uppgradering är tillgänglig, kommer du att ha en vecka eller så tillämpar du den manuellt. Slutdatumet anges i uppgraderings meddelandet. Om du inte uppgraderar under den tiden tillämpar Azure automatiskt uppdateringen i cacheminnet. Det går inte att konfigurera tids inställningen för automatisk uppgradering. Om du är orolig över prestanda påverkan från cachen bör du uppgradera program varan själv innan tids perioden går ut.

Klicka på **Uppgradera** om du vill starta program uppdateringen. Cachens status ändras till **uppgraderingen** tills åtgärden har slutförts.

## <a name="delete-the-cache"></a>Ta bort cachen

Med knappen **ta bort** förstörs cachen. När du tar bort en cache förstörs alla dess resurser och debiteras inte längre.

De Server dels lagrings volymer som används som lagrings mål påverkas inte när du tar bort cacheminnet. Du kan lägga till dem i en framtida cache senare eller ställa av dem separat.

> [!NOTE]
> Azure HPC cache skriver inte automatiskt över ändrade data från cacheminnet till backend Storage-System innan cachen tas bort.
>
> För att se till att alla data i cacheminnet har skrivits till långsiktig lagring [stoppar du cacheminnet](#stop-the-cache) innan du tar bort det. Kontrol lera att den visar statusen **stoppad** innan du klickar på knappen Ta bort.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Cachelagra mått och övervakning

På sidan Översikt visas diagram för viss grundläggande cache-statistik – cache-genomflöde, åtgärder per sekund och svars tid.

![skärm bild av tre linje diagram som visar ovanstående statistik för ett exempel-cache](media/hpc-cache-overview-stats.png)

Dessa diagram ingår i Azures inbyggda verktyg för övervakning och analys. Det finns ytterligare verktyg och aviseringar på sidorna under **övervaknings** rubriken i portalens marginal List. Läs mer i avsnittet portal i [Azures övervaknings dokumentation](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

<!-- * Learn more about metrics and statistics for hpc cache -->
* Lär dig mer om [Azures mått och statistik verktyg](../azure-monitor/index.yml)
* Få [hjälp med Azure HPC-cache](hpc-cache-support-ticket.md)
