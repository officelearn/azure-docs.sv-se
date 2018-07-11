---
title: ta med fil
description: ta med fil
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/05/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: abf10177f8ce86309043da92d1f2b690775b6d89
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37909934"
---
Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Dessa uppdateringar omfattar allt från korrigeringar av programvarukomponenter i värdmiljön (som operativsystem, hypervisor-program och olika agenter som distribuerats på värddatorn), uppdateringar av nätverkskomponenter till att ta maskinvara ur drift. Flesta av dessa uppdateringar genomförs utan någon inverkan på de virtuella datorerna. Men finns det fall där uppdateringar påverka:

- Om en omstart utan uppdatering är möjligt, använder Azure minne bevarande Underhåll för att pausa den virtuella datorn när värden har uppdaterats eller den virtuella datorn flyttas till en värd som redan är uppdaterad helt och hållet.

- Om en omstart krävs för underhåll, får du ett meddelande om när det planerade underhållet. I dessa fall kan också får du ett tidsfönster som där du kan starta underhållet själv, samtidigt som passar dig.

Den här sidan beskriver hur Microsoft Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott) finns i Hantera tillgängligheten för virtuella datorer för [Windows] (.. / articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).

Program som körs på en virtuell dator kan samla in information om kommande uppdateringar med hjälp av Azure Metadata Service för [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) eller [Linux] (.. / articles/virtual-machines/linux/instance-metadata-service.md).

”” Instruktioner om hur du hanterar planerat underhåll, finns i ”Hantera meddelanden planerat underhåll” för [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Minne bevarande Underhåll

När uppdateringar inte kräver en fullständig omstart, används minne preserving Underhåll mekanismer för att begränsa effekten till den virtuella datorn. Den virtuella datorn pausas i upp till 30 sekunder, bevarar minnet i RAM, medan den faktiska driftsmiljön gäller nödvändiga uppdateringar och korrigeringar, eller flyttar den virtuella datorn till en värd som redan är uppdaterad. Den virtuella datorn sedan återupptas och synkroniseras klockan på den virtuella datorn automatiskt. 

Dessa icke rebootful underhållsåtgärder är tillämpad feldomän av feldomän och förloppet stoppas om någon varning hälsotillstånd signaler tas emot.

Vissa program kan påverkas av dessa typer av uppdateringar. Program som utför händelsebearbetning i realtid, t.ex. mediedirektuppspelning eller transkodning eller hög genomströmning nätverk scenarier, kan inte utformas ska kunna hanteras en 30 andra paus. <!-- sooooo, what should they do? --> Om den virtuella datorn flyttas till en annan värd, kanske vissa känsliga arbetsbelastningar märker en liten prestandaförsämring i några minuter ledde till pausen virtuella datorn. 


## <a name="maintenance-requiring-a-reboot"></a>Underhåll som kräver en omstart

När virtuella datorer behöver startas om för planerat underhåll, meddelas du i förväg. Planerat underhåll har två faser: fönstret självbetjäning och en schemalagd underhållsperiod.

Den **självbetjäning fönstret** kan du starta underhållet på dina virtuella datorer. Under denna tid kan du fråga varje virtuell dator för att se deras status och kontrollera resultatet av din senaste begäran.

När du startar självbetjäningsunderhållet, flyttas den virtuella datorn till en nod som redan har uppdaterats och aktiverar den igen. Eftersom den virtuella datorn om den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med virtuella nätverksgränssnittet har uppdaterats.

Om du startar självbetjäningsunderhållet och det finns ett fel under åtgärden har stoppats, uppdateras inte den virtuella datorn och du får möjlighet att göra om Underhåll självbetjäning. 

När fönstret via självbetjäning har passerat den **schemalagda underhållsperiod** börjar. Under den här tidsperioden kan du fortfarande fråga efter underhållsfönstret, men inte längre att kunna starta underhållet själv.

Information om hur du hanterar underhåll som kräver en omstart, finns i ”Hantera meddelanden planerat underhåll” för [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Överväganden för tillgänglighet under schemalagt underhåll 

Om du vill vänta tills det schemalagda underhållsfönstret finns några saker att tänka på för att upprätthålla högsta möjliga tillgänglighet för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region paras ihop med en annan region inom samma geografiska område, tillsammans de se en regionala par. Under schemalagt underhåll uppdaterar Azure endast de virtuella datorerna i en enda region för en regionparet. När du till exempel uppdaterar de virtuella datorerna i regionen USA, norra centrala så uppdaterar inte Azure inte alla virtuella datorer i regionen USA, södra centrala på samma gång. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Förstå hur regionpar fungerar kan hjälpa dig att bättre att distribuera dina virtuella datorer mellan regioner. Mer information finns i [Azure regionpar](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighetsuppsättningar och skalningsuppsättningar

När du distribuerar en arbetsbelastning på Azure Virtual Machines kan skapa du virtuella datorer i en tillgänglighetsuppsättning för att ge hög tillgänglighet för ditt program. Detta garanterar att minst en virtuell dator under ett avbrott eller rebootful underhållshändelser är tillgänglig.

Enskilda virtuella datorer är fördelade på upp till 20 uppdateringsdomäner (ud) i en tillgänglighetsuppsättning. Under det schemalagda underhållet påverkas endast en enskild uppdateringsdomän vid en given tidpunkt. Tänk på att ordningen för de uppdateringsdomäner som påverkas inte nödvändigtvis sker sekventiellt. 

Virtual machine scale sets är en Azure-beräkningsresurs som hjälper dig att distribuera och hantera en uppsättning identiska virtuella datorer som en enskild resurs. Skalningsuppsättningen distribueras automatiskt över uppdateringsdomäner, som virtuella datorer i en tillgänglighetsuppsättning. Precis som med tillgänglighetsuppsättningar påverkas med skalningsuppsättningar en enda uppdateringsdomän vid en given tidpunkt under schemalagt underhåll.

Mer information om hur du konfigurerar dina virtuella datorer för hög tillgänglighet finns i Hantera tillgängligheten för dina virtuella datorer för [Windows](../articles/virtual-machines/windows/manage-availability.md) eller [Linux](../articles/virtual-machines/linux/manage-availability.md).
