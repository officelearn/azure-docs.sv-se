---
title: Misslyckas med att återställa virtuella datorer/fysiska servrar från Azure med Azure Site Recovery
description: Lär dig hur du växlar tillbaka till den lokala platsen efter redundans till Azure, under haveribeställning av virtuella datorer och fysiska servrar till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495323"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Misslyckas med virtuella virtuella datorer med VMware till lokal webbplats

I den här artikeln beskrivs hur du reder tillbaka virtuella Azure-datorer till en lokal plats, efter [redundans för](site-recovery-failover.md) lokala virtuella datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). När du har misslyckats med att återställa lokala aktiverar du replikering så att de lokala virtuella datorerna börjar replikera till Azure.

## <a name="before-you-start"></a>Innan du börjar

1. Läs mer om [felåtergång för VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Kontrollera att du har granskat och slutfört stegen för att [förbereda för återställning efter fel](vmware-azure-prepare-failback.md)och att alla nödvändiga komponenter distribueras. Komponenter inkluderar en processserver i Azure, en lokal huvudmålserver och en VPN-anslutning från plats till plats (eller ExpressRoute-privat peering) för återställning av återställning av återställning.
3. Kontrollera att du har slutfört [kraven](vmware-azure-reprotect.md#before-you-begin) för återskydd och återställning av återställning och att du har [aktiverat reprotection](vmware-azure-reprotect.md#enable-reprotection) av virtuella Azure-datorer, så att de replikerar från Azure till den lokala platsen. Virtuella datorer måste vara i ett replikerat tillstånd är för att misslyckas tillbaka.




## <a name="run-a-failover-to-fail-back"></a>Kör en redundans för att växla tillbaka

1. Kontrollera att virtuella Azure-datorer replikeras och replikeras till den lokala platsen. 
    - En virtuell dator behöver minst en återställningspunkt för att kunna återställas igen.
    - Om du misslyckas med en återställningsplan bör alla datorer i planen ha minst en återställningspunkt.
2. Välj den virtuella datorn i valvet > **replikerade objekt.** Högerklicka på den virtuella datorn > **Oplanerad redundans**.
3. Kontrollera redundansriktningen (från Azure) i **Bekräfta redundans.**
4. Välj en återställningspunkt som ska användas för redundansen.
    - Vi rekommenderar att du använder den **senaste återställningspunkten.** Den appkonsekventa punkten ligger bakom den senaste tidpunkten och orsakar viss dataförlust.
    - **Senaste** är en krasch-konsekvent återhämtning punkt.
    - Med **Senaste**misslyckas en virtuell dator över till sin senaste tillgängliga tidpunkt. Om du har en replikeringsgrupp för konsekvens med flera virtuella datorer i en återställningsplan, växlar varje virtuell dator i gruppen över till sin oberoende senaste tidpunkt.
    - Om du använder en appkonsekvent återställningspunkt går varje virtuell dator tillbaka till sin senaste tillgängliga punkt. Om en återställningsplan har en replikeringsgrupp återställs varje grupp till sin gemensamma tillgängliga återställningspunkt.
5. Redundans börjar. Site Recovery stänger av virtuella Azure-datorer.
6. När redundansen är klar kontrollerar du att allt fungerar som förväntat. Kontrollera att virtuella Azure-datorer har stängts av. 
7. Med allt verifierat högerklickar du på den virtuella datorn > **Commit**för att slutföra redundansprocessen. Commit tar bort den misslyckade Azure-virtuella datorn. 

> [!NOTE]
> För virtuella Windows-datorer inaktiverar Site Recovery VMware-verktygen under redundans. Under återställning av Windows VM aktiveras VMware-verktygen igen. 




## <a name="reprotect-from-on-premises-to-azure"></a>Reprotera från lokalt till Azure

När du har gjort det återställde tas virtuella Azure-datorer bort. Den virtuella datorn är tillbaka på den lokala platsen, men den är inte skyddad. Så här börjar du replikera virtuella datorer till Azure igen enligt följande:

1. I valvet > **replikerade objekt**väljer du misslyckade virtuella datorer i ryggen och väljer sedan **Skydda igen**.
2. Ange den processserver som används för att skicka tillbaka data till Azure.
3. Välj **OK** om du vill starta reprotect-jobbet.

> [!NOTE]
> När en lokal virtuell dator startar tar det upp till 15 minuter för agenten att registrera sig tillbaka till konfigurationsservern. Under den här tiden misslyckas reprotect och returnerar ett felmeddelande om att agenten inte är installerad. Om detta inträffar, vänta i några minuter och reprotect.

## <a name="next-steps"></a>Nästa steg

När reprotect-jobbet är klart replikerar den lokala virtuella datorn till Azure. Efter behov kan du [köra en annan redundans](site-recovery-failover.md) till Azure.

