---
title: Hantera och uppdatera Azure HPC-cache
description: Hantera och uppdatera Azure HPC-cache med hjälp av Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: a166a904b2e63419efd5803fd54be1d1b59836fb
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867088"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Hantera din cache från Azure Portal

På sidan cache-översikt i Azure Portal visas projekt information, cache-status och grundläggande statistik för cacheminnet. Den har också kontroller för att ta bort cacheminnet, tömma data till långsiktig lagring eller uppdatera program vara.

Öppna översikts sidan genom att välja din cache-resurs i Azure Portal. Läs till exempel sidan **alla resurser** och klicka på cache-namnet.

![skärm bild av översikts sidan för Azure HPC-instansen](media/hpc-cache-overview.png)

Knapparna överst på sidan kan hjälpa dig att hantera cachen:

* [**Flush**](#flush-cached-data) -skriver ändrade data till lagrings mål
* [**Uppgradera**](#upgrade-cache-software) – uppdaterar cache-programvaran
* **Uppdatera** – Läs in sidan Översikt igen
* [**Ta bort**](#delete-the-cache) – förstör cachen permanent

Läs mer om de här alternativen nedan.

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
> Följ den här proceduren för att se till att alla data i cacheminnet har skrivits till långsiktig lagring:
>
> 1. [Ta bort](hpc-cache-edit-storage.md#remove-a-storage-target) varje lagrings mål från Azure HPC-cachen med hjälp av knappen Ta bort på sidan lagrings mål. Systemet skriver automatiskt alla ändrade data från cacheminnet till Server dels lagrings systemet innan målet tas bort.
> 1. Vänta tills lagrings målet har tagits bort helt. Processen kan ta en timme eller längre om det finns mycket data att skriva från cachen. När det är klart anger ett Portal meddelande att borttagnings åtgärden lyckades och lagrings målet försvinner från listan.
> 1. När alla berörda lagrings mål har tagits bort är det säkert att ta bort cacheminnet.
>
> Alternativt kan du använda alternativet [Flush](#flush-cached-data) för att spara cachelagrade data, men det finns en liten risk för att förlora arbete om en klient skriver en ändring i cachen När tömningen är klar, men innan cache-instansen förstörs.

## <a name="cache-metrics-and-monitoring"></a>Cachelagra mått och övervakning

På sidan Översikt visas diagram för viss grundläggande cache-statistik – cache-genomflöde, åtgärder per sekund och svars tid.

![skärm bild av tre linje diagram som visar ovanstående statistik för ett exempel-cache](media/hpc-cache-overview-stats.png)

Dessa diagram ingår i Azures inbyggda verktyg för övervakning och analys. Det finns ytterligare verktyg och aviseringar på sidorna under **övervaknings** rubriken i portalens marginal List. Läs mer i avsnittet portal i [Azures övervaknings dokumentation](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

<!-- * Learn more about metrics and statistics for hpc cache -->
* Lär dig mer om [Azures mått och statistik verktyg](../azure-monitor/index.yml)
* Få [hjälp med Azure HPC-cache](hpc-cache-support-ticket.md)
