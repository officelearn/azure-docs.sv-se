---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 193003cef0aed464596e913c0df86e6123292b9f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestanda och säkerhet för infrastrukturen för värd för virtuella datorer. Dessa uppdateringar mellan korrigering programvarukomponenter i värdmiljön (till exempel operativsystem, hypervisor och olika agenter distribueras på värden), uppgraderar nätverkskomponenter, till inaktivering av maskinvara. Flesta av de här uppdateringarna utförs utan någon inverkan på de virtuella datorerna. Det finns dock fall där uppdateringar har konsekvenser:

- Om underhåll som inte kräver en omstart, använder Azure migrering på plats för att pausa den virtuella datorn när värden har uppdaterats.

- Om en omstart krävs för underhåll, får du ett meddelande om vid underhåll är planerade. I dessa fall kan också får du ett tidsfönster där du kan starta underhållet själv, samtidigt som passar dig.

Den här sidan beskrivs hur Microsoft Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott) finns i Hantera tillgängligheten för virtuella datorer [Windows] (... / articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).

Program som körs på en virtuell dator kan samla in information om kommande uppdateringar genom att använda tjänsten Azure Metadata för [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) eller [Linux] (... / articles/virtual-machines/linux/instance-metadata-service.md).

”” Information om hur du hanterar planerade maintence, finns i ”Hantera planerat underhåll meddelanden” för [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>VM-migrering på plats

När uppdateringar inte kräver en omstart för fullständig, används en aktiv migrering på plats. Den virtuella datorn är pausad i 30 sekunder, bevarar minnet i RAM, medan värdmiljön gäller nödvändiga uppdateringar och korrigeringsfiler under uppdateringen. Den virtuella datorn sedan återupptas och klockan på den virtuella datorn synkroniseras automatiskt.

För virtuella datorer i tillgänglighetsuppsättningar kan är uppdatera domäner uppdaterade ett i taget. Alla virtuella datorer i en uppdateringsdomän (UD) pausas, uppdateras och återupptas innan planerat underhåll flyttar till nästa UD.

Vissa program kan påverkas av dessa typer av uppdateringar. Program som utför realtid händelsebearbetning, som direktuppspelning eller omkodning eller hög genomströmning nätverk scenarier kan inte konstrueras klarar en paus på 30 sekund. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>Underhåll som kräver en omstart

När virtuella datorer måste startas om för planerat underhåll, meddelas du i förväg. Planerat underhåll har två faser: fönstret självbetjäning och schemalagda underhållsperiod.

Den **självbetjäning fönstret** kan du initiera Underhåll på dina virtuella datorer. Under denna tid kan fråga du varje virtuell dator för att se deras status och kontrollera resultatet av din senaste begäran.

När du startar självbetjäning Underhåll flyttas den virtuella datorn till en nod som redan har uppdaterats och sedan aktiveras den tillbaka. Eftersom den virtuella datorn startas om tillfälligt disken går förlorad och dynamiska IP-adresser som är kopplade till virtuella nätverksgränssnittet har uppdaterats.

Om du startar självbetjäning underhåll och det finns ett fel under processen igen har stoppats, uppdateras inte den virtuella datorn och den också tas bort från upprepning planerat underhåll. Du kontakta i ett senare tillfälle med ett nytt schema och erbjuder en ny möjlighet att utföra underhåll för självbetjäning. 

När fönstret självbetjäning har passerat den **schemalagda underhållsperiod** börjar. Under den här tidsfönstret du fortfarande fråga efter underhållsfönstret, men inte längre att kunna starta underhållet själv.

## <a name="availability-considerations-during-planned-maintenance"></a>Tillgänglighet under planerat underhåll 

Om du vill vänta tills fönstret planerat underhåll finns det några saker att tänka på för att underhålla den högsta availabilty för dina virtuella datorer. 

### <a name="paired-regions"></a>Parad regioner

Varje Azure-region paras ihop med en annan region inom samma geografiska, samman de gör ett regionalt par. Under planerat underhåll uppdatera Azure bara virtuella datorer i en region i en region-par. När du till exempel uppdaterar de virtuella datorerna i regionen USA, norra centrala så uppdaterar inte Azure inte alla virtuella datorer i regionen USA, södra centrala på samma gång. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Så här fungerar region par kan hjälpa dig att förstå bättre distribuera din virtuella dator över regioner. Mer information finns i [Azure-region par](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Tillgänglighetsuppsättningar och skaluppsättningar

När du distribuerar en arbetsbelastningen på virtuella Azure-datorer kan skapa du de virtuella datorerna i en tillgänglighetsuppsättning för att tillhandahålla hög tillgänglighet för ditt program. Detta garanterar att minst en virtuell dator vid ett strömavbrott eller underhåll händelser är tillgänglig.

I en tillgänglighetsuppsättning sprids enskilda virtuella datorer över upp till 20 update domäner (UDs). Endast en enskild uppdateringsdomän påverkas vid en given tidpunkt under planerat underhåll. Tänk på att ordningen för update-domäner som påverkas inte nödvändigtvis sker sekventiellt. 

Skaluppsättningar för den virtuella datorn är en Azure compute-resurs som du kan distribuera och hantera en uppsättning identiska virtuella datorer som en enskild resurs. Skaluppsättning distribueras automatiskt uppdatera domäner som virtuella datorer i en tillgänglighetsuppsättning. Precis som med tillgänglighetsuppsättningar påverkas med skalningsuppsättningar i en enda uppdateringsdomän vid en given tidpunkt.

Mer information om hur du konfigurerar dina virtuella datorer för hög tillgänglighet finns i Hantera tillgängligheten för virtuella datorer för [Windows](../articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).
