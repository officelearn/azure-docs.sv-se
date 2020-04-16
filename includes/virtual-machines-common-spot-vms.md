---
title: ta med fil
description: ta med fil
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421909"
---
Med hjälp av Spot virtuella datorer kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spot-datorer. Därför är spot-virtuella datorer bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid på dygnet och mycket mer. När du distribuerar spot-virtuella datorer allokerar Azure de virtuella datorerna om det finns kapacitet tillgänglig, men det finns inget serviceavtal för dessa virtuella datorer. En spot-vm ger inga garantier för hög tillgänglighet. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att vräkas Spot-virtuella datorer med 30 sekunders varsel. 


## <a name="eviction-policy"></a>Policy för vräkning

Virtuella datorer kan vräkas baserat på kapacitet eller maxpris du anger. För virtuella datorer är vräkningsprincipen inställd på *Deallocate* som flyttar dina vräkta virtuella datorer till tillståndet stoppad deallocated, så att du kan distribuera om de vräkta virtuella datorerna vid ett senare tillfälle. Omfördelning av start-och returmaskiner beror dock på att det finns tillgänglig platskapacitet. De utdelade virtuella datorerna räknas mot din plats vCPU-kvot och du debiteras för dina underliggande diskar. 

Användare kan välja att ta emot aviseringar via [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer håller på att vräkas och du kommer att ha 30 sekunder på dig att slutföra alla jobb och utföra avstängningsuppgifter före vräkningen. 


| Alternativ | Resultat |
|--------|---------|
| Maxpriset är inställt på >= det aktuella priset. | Vm distribueras om kapacitet och kvot är tillgängliga. |
| Maxpriset är inställt på < det aktuella priset. | Den virtuella datorn har inte distribuerats. Du får ett felmeddelande om att maxpriset måste vara >= aktuellt pris. |
| Starta om en stop/deallocate VM om maxpriset är >= det aktuella priset | Om det finns kapacitet och kvot distribueras den virtuella datorn. |
| Starta om en stop/deallocate VM om maxpriset är < det aktuella priset | Du får ett felmeddelande om att maxpriset måste vara >= aktuellt pris. | 
| Priset för den virtuella datorn har gått upp och är nu > maxpriset. | Den virtuella datorn blir vräkt. Du får en 30-talsavisering innan själva vräkningen. | 
| Efter vräkning priset för den virtuella datorn går tillbaka till att vara < maxpriset. | Den virtuella datorn startas inte automatiskt igen. Du kan starta om den virtuella datorn själv, och den debiteras till det aktuella priset. |
| Om maxpriset är inställt på`-1` | Den virtuella datorn kommer inte att vräkas av prisskäl. Maxpriset blir det aktuella priset, upp till priset för vanliga virtuella datorer. Du kommer aldrig att debiteras över standardpriset.| 
| Ändra maxpriset | Du måste frigöra den virtuella datorn för att ändra maxpriset. Deallocate den virtuella datorn, ange t ett nytt maxpris och uppdatera sedan den virtuella datorn. |

## <a name="limitations"></a>Begränsningar

Följande vm-storlekar stöds inte för start-och returmaskiner för plats:
 - B-serien
 - Promo versioner av alla storlekar (som Dv2, NV, NC, H promo storlekar)

Spot-virtuella datorer kan för närvarande inte använda efemära OS-diskar.

Spot virtuella datorer kan distribueras till valfri region, förutom Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Prissättning

Priserna för spot-virtuella datorer varierar, baserat på region och SKU. Mer information finns i VM-priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Med rörlig prissättning har du möjlighet att ange ett maxpris, i US-dollar (USD), med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du ställer in `-1`maxpriset så kommer den virtuella datorn inte att vräkas baserat på priset. Priset för den virtuella datorn blir det aktuella priset för avista eller priset för en vanlig virtuell dator, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig.


##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** När skapas, är en Spot VM samma som vanlig standard VM?

**A.** Ja, förutom att det inte finns något serviceavtal för start-och-start-datorer och de kan när som helst vräkas.


**F:** Vad ska man göra när du blir vräkt, men fortfarande behöver kapacitet?

**A.** Vi rekommenderar att du använder vanliga virtuella datorer i stället för Spot-virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för start-VM?

**A.** Spot-virtuella datorer har en separat kvotpool. Spotkvoten delas mellan virtuella datorer och skalningsuppsättningsinstanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**F:** Kan jag begära ytterligare kvot för Spot?

**A.** Ja, du kommer att kunna skicka begäran om att öka din kvot för spot-datorer genom [standardprocessen för kvotbegäran.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**F:** Vilka kanaler har stöd för virtuella spotds?

**A.** Se tabellen nedan för Spot VM-tillgänglighet.

<a name="channel"></a>

| Azure-kanaler               | Azure Spot virtuella datorer tillgänglighet       |
|------------------------------|-----------------------------------|
| Enterprise-avtal         | Ja                               |
| Betala per användning                | Ja                               |
| Molntjänstleverantör (CSP) | [Kontakta din partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft-kundavtal | Ja                               |
| Fördelar                     | Inte tillgängligt                     |
| Sponsrade                    | Inte tillgängligt                     |
| Kostnadsfri utvärderingsversion                   | Inte tillgängligt                     |


**F:** Var kan jag ställa frågor?

**A.** Du kan lägga upp `azure-spot` och tagga din fråga med på [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



