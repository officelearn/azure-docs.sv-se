---
title: ta med fil
description: ta med fil
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 34723a6ee37e54ea2d81e6d1143672e3ccb30d1e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53805763"
---
Azure uppdaterar regelbundet plattform för att förbättra tillförlitligheten, prestanda och säkerheten för infrastrukturen för värd för virtuella datorer. Dessa uppdateringar sträcker sig från korrigeringar programvarukomponenter i värdmiljön, uppgradera nätverkskomponenter, till maskinvara ta ur drift. Flesta av dessa uppdateringar har ingen inverkan på de virtuella datorerna. Men finns det fall där uppdateringar påverka och Azure väljer minst påverkar metoden efter uppdateringar:

- Om en icke-rebootful uppdatering är möjligt, den virtuella datorn pausas medan värden uppdateras eller den är aktiv migreras till en värd som redan är uppdaterad.

- Om en omstart krävs för underhåll, får du ett meddelande om när det planerade underhållet. Azure ger också ett tidsfönster som där du kan starta underhållet själv, samtidigt som passar dig. Azure investeringar i att minska fall när de virtuella datorerna måste startas om för plattform för planerat underhåll. 

Den här sidan beskriver hur Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott) finns i Hantera tillgängligheten för virtuella datorer för [Windows](../articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).

Du kan hämta i VM-meddelande om kommande Underhåll med schemalagda händelser för [Windows](../articles/virtual-machines/windows/scheduled-events.md) eller [Linux](../articles/virtual-machines/linux/scheduled-events.md).

”” Instruktioner om hur du hanterar planerat underhåll, finns i ”Hantera meddelanden planerat underhåll” för [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Minne bevarande Underhåll

Målet för de flesta icke rebootful uppdateringar är mindre än 10 sekunder pausa för den virtuella datorn. I vissa fall minnet bevarande Underhåll används mekanismer, vilket pausar den virtuella datorn i upp till 30 sekunder och bevarar minnet i RAM-minne. Den virtuella datorn sedan återupptas och synkroniseras klockan på den virtuella datorn automatiskt. Azure är allt med hjälp av Direktmigrering tekniker och förbättra minne bevarar Underhåll mekanism för att minska varaktighet för pausen.

Dessa icke rebootful underhållsåtgärder är tillämpad feldomän av feldomän och förloppet stoppas om någon varning hälsotillstånd signaler tas emot. 

Vissa program kan påverkas av dessa typer av uppdateringar. Den virtuella datorn är live migreras till en annan värd, kanske vissa känsliga arbetsbelastningar märker en liten prestandaförsämring i några minuter ledde till VM-pausa. Sådana program kan dra nytta av schemalagda händelser för [Windows](../articles/virtual-machines/windows/scheduled-events.md) eller [Linux](../articles/virtual-machines/linux/scheduled-events.md) att förbereda för underhåll av virtuell dator och har ingen påverkan under Azure-underhåll. Azure fungerar även på Underhåll styr funktioner för sådana mycket känsliga program. 


## <a name="maintenance-requiring-a-reboot"></a>Underhåll som kräver en omstart

I de sällsynta fall när virtuella datorer behöver startas om innan planerat underhåll meddelas du i förväg. Planerat underhåll har två faser: fönstret självbetjäning och en schemalagd underhållsperiod.

Den **självbetjäning fönstret** kan du starta underhållet på dina virtuella datorer. Under denna tid kan du fråga varje virtuell dator för att se deras status och kontrollera resultatet av din senaste begäran.

När du startar självbetjäningsunderhållet, om den virtuella datorn till en nod som redan är uppdaterad. Eftersom den virtuella datorn om den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med virtuella nätverksgränssnittet har uppdaterats.

Om du startar självbetjäningsunderhållet och det finns ett fel under åtgärden har stoppats, uppdateras inte den virtuella datorn och du får möjlighet att göra om Underhåll självbetjäning. 

När fönstret via självbetjäning har passerat den **schemalagda underhållsperiod** börjar. Under den här tidsperioden du fortfarande kan fråga efter underhållsfönstret, men det går inte att starta underhållet själv.

Information om hur du hanterar underhåll som kräver en omstart, finns i ”Hantera meddelanden planerat underhåll” för [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Överväganden för tillgänglighet under schemalagt underhåll 

Om du vill vänta tills det schemalagda underhållsfönstret finns några saker att tänka på för att upprätthålla högsta möjliga tillgänglighet för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region paras ihop med en annan region inom samma geografiska område och tillsammans de gör en regionala par. I fasen för schemalagt underhåll uppdaterar Azure endast de virtuella datorerna i en enda region för en regionparet. När du uppdaterar den virtuella datorn i norra centrala USA, uppdatera inte Azure exempelvis virtuella datorer i USA, södra centrala på samma gång. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Förstå hur regionpar fungerar kan hjälpa dig att bättre att distribuera dina virtuella datorer mellan regioner. Mer information finns i [Azure regionpar](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighetsuppsättningar och skalningsuppsättningar

När du distribuerar en arbetsbelastning på Azure Virtual Machines kan skapa du virtuella datorer i en tillgänglighetsuppsättning för att ge hög tillgänglighet för ditt program. Detta garanterar att minst en virtuell dator under ett avbrott eller rebootful underhållshändelser är tillgänglig.

Enskilda virtuella datorer är fördelade på upp till 20 uppdateringsdomäner (ud) i en tillgänglighetsuppsättning. Under det schemalagda underhållet uppdateras bara en enda uppdateringsdomän vid en given tidpunkt. Ordningen för de uppdateringsdomäner som håller på att uppdateras inträffa inte nödvändigtvis sekventiellt. 

Virtual machine scale sets är en Azure-beräkningsresurs som hjälper dig att distribuera och hantera en uppsättning identiska virtuella datorer som en enskild resurs. Skalningsuppsättningen distribueras automatiskt över uppdateringsdomäner, som virtuella datorer i en tillgänglighetsuppsättning. Precis som med tillgänglighetsuppsättningar uppdateras med skalningsuppsättningar en enda uppdateringsdomän vid en given tidpunkt under schemalagt underhåll.

Mer information om hur du konfigurerar dina virtuella datorer för hög tillgänglighet finns i Hantera tillgängligheten för dina virtuella datorer för [Windows](../articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).
