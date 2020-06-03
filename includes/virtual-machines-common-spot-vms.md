---
title: ta med fil
description: ta med fil
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dbac1f4fad940b817befa3a45447cf7367c28c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317698"
---
Med hjälp av virtuella datorer kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar virtuella datorer, allokerar Azure de virtuella datorerna om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa virtuella datorer. En VM-VM ger inga garantier för hög tillgänglighet. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella platser med 30 sekunders varsel. 


## <a name="eviction-policy"></a>Borttagnings princip

Virtuella datorer kan avlägsnas baserat på kapacitet eller det högsta pris som du har angett. För virtuella datorer är borttagnings principen inställd på *frigörning* som flyttar de avlägsnade virtuella datorerna till statusen stoppad-frigjord, så att du kan distribuera om de avlägsnade virtuella datorerna vid ett senare tillfälle. Däremot kommer omtilldelning av virtuella datorer att vara beroende av att det finns tillgänglig dekor kapacitet. De friallokerade virtuella datorerna räknas över till din vCPU kvot och du debiteras för de underliggande diskarna. 

Användare kan välja att ta emot meddelanden i den virtuella datorn via [Azure schemalagda händelser](../articles/virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra jobben och utföra avstängnings uppgifter innan avlägsnandet. 


| Alternativ | Resultat |
|--------|---------|
| Högsta pris är inställt på >= det aktuella priset. | Den virtuella datorn distribueras om kapacitet och kvot är tillgängliga. |
| Högsta pris är inställt på < det aktuella priset. | Den virtuella datorn har inte distribuerats. Du får ett fel meddelande om att max priset måste vara >= aktuellt pris. |
| Starta om en virtuell dator för att stoppa/frigöra om max priset är >= aktuellt pris | Om det finns kapacitet och kvot distribueras den virtuella datorn. |
| Starta om en virtuell dator för att stoppa/frigöra om högsta pris är < det aktuella priset | Du får ett fel meddelande om att max priset måste vara >= aktuellt pris. | 
| Priset för den virtuella datorn har blivit nu > det högsta priset. | Den virtuella datorn har avlägsnats. Du får ett 30 s-meddelande innan du börjar med den faktiska borttagningen. | 
| Efter avlägsnandet går priset för den virtuella datorn tillbaka till < det högsta priset. | Den virtuella datorn kommer inte att startas om automatiskt. Du kan starta om den virtuella datorn själv och debiteras enligt det aktuella priset. |
| Om max priset är inställt på`-1` | Den virtuella datorn kommer inte att avlägsnas av prissättnings skäl. Det högsta priset är det aktuella priset, upp till priset för virtuella datorer med standard typ. Du debiteras aldrig enligt standard priset.| 
| Ändra Max priset | Du måste frigöra den virtuella datorn för att ändra det högsta priset. Frigör den virtuella datorn, ange ett nytt max pris och uppdatera den virtuella datorn. |

## <a name="limitations"></a>Begränsningar

Följande VM-storlekar stöds inte för virtuella datorer på platsen:
 - B-serien
 - Kampanj versioner av valfri storlek (t. ex. dv2, NV, NC, H kampanj storlek)

Virtuella datorer för virtuella datorer kan för närvarande inte använda tillfälliga OS-diskar.

Virtuella datorer kan distribueras till vilken region som helst, förutom Microsoft Azure Kina 21Vianet.

## <a name="pricing"></a>Prissättning

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.


##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** När den har skapats är en virtuell dator på samma sätt som vanlig standard-VM?

**A:** Ja, förutom att det inte finns något service avtal för virtuella datorer på plats och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder virtuella standard datorer i stället för virtuella datorer för virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för virtuella datorer med virtuella datorer?

**A:** Virtuella datorer med virtuella datorer kommer att ha en separat kvotmall. Kvoten för kvoten kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**F:** Kan jag begära ytterligare kvot för platsen?

**A:** Ja, du kommer att kunna skicka begäran om att öka din kvot för virtuella datorer med hjälp av [standard kvot processen](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**F:** Vilka kanaler stöder virtuella datorer?

**A:** Se tabellen nedan för tillgänglighet för dekor datorer.

<a name="channel"></a>

| Azure-kanaler               | Tillgänglighet för Azure-VM-VM       |
|------------------------------|-----------------------------------|
| Enterprise-avtal         | Yes                               |
| Betala per användning                | Yes                               |
| Cloud Service Provider (CSP) | [Kontakta din partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft-kundavtal | Yes                               |
| Fördelar                     | Inte tillgänglig                     |
| Sponsrat                    | Yes                               |
| Kostnadsfri utvärderingsversion                   | Inte tillgänglig                     |


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



