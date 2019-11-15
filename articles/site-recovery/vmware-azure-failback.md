---
title: Återställa virtuella VMware-datorer/fysiska servrar från Azure med Azure Site Recovery
description: Lär dig hur du växlar tillbaka till den lokala platsen efter en redundansväxling till Azure, under haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084066"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Återställa virtuella VMware-datorer och fysiska servrar från Azure till en lokal plats

Den här artikeln beskriver hur du växlar tillbaka virtuella datorer från Azure Virtual Machines till en lokal VMware-miljö. Följ anvisningarna i den här artikeln om du vill återställa dina virtuella VMware-datorer eller Windows/Linux-fysiska servrar när de har redundansväxlats från den lokala platsen till Azure med hjälp av [redundansväxlingen i Azure Site Recovery](site-recovery-failover.md) själv studie kursen.

## <a name="prerequisites"></a>Krav
- Se till att du har läst informationen om de [olika typerna av återställning efter fel](concepts-types-of-failback.md) och motsvarande varningar.

> [!WARNING]
> Du kan inte växla tillbaka när du har [slutfört migreringen](migrate-overview.md#what-do-we-mean-by-migration), flyttat en virtuell dator till en annan resurs grupp eller tagit bort den virtuella Azure-datorn. Om du inaktiverar skyddet av den virtuella datorn går det inte att återställa.

> [!WARNING]
> En fysisk server för Windows Server 2008 R2 SP1, som är skyddad och misslyckad till Azure, kan inte återställas.

> [!NOTE]
> Om du har redundansväxlats virtuella VMware-datorer kan du inte växla tillbaka till en Hyper-V-värd.


- Innan du fortsätter ska du slutföra återställnings stegen så att de virtuella datorerna är i ett replikerat tillstånd och du kan starta en redundansväxling till en lokal plats. Mer information finns i [så här skyddar du från Azure till lokalt](vmware-azure-reprotect.md).

- Se till att vCenter är i ett anslutet tillstånd innan du gör en återställning efter fel. Annars Miss lyckas det att koppla bort diskar och koppla tillbaka dem till den virtuella datorn.

- Vid redundansväxling till Azure kanske den lokala platsen inte är tillgänglig, och konfigurations servern kan vara antingen otillgänglig eller avstängd. Vid återaktivering och återställning efter fel bör den lokala konfigurations servern köras och vara i ett anslutet tillstånd. 

- Under återställning efter fel måste den virtuella datorn finnas i konfigurations serverns databas eller så går det inte att återställa efter fel. Se till att du utför regelbundna schemalagda säkerhets kopieringar av servern. Om en katastrof inträffar måste du återställa servern med den ursprungliga IP-adressen för att återställning efter fel ska fungera.

- Huvud mål servern ska inte ha några ögonblicks bilder innan återställningen/återställning efter fel utlöses.

## <a name="overview-of-failback"></a>Översikt över återställning efter fel
När du har växlat till Azure kan du växla tillbaka till din lokala plats genom att köra följande steg:

1. [Återaktivera skyddet](vmware-azure-reprotect.md) för de virtuella datorerna i Azure så att de börjar replikera till virtuella VMware-datorer på din lokala plats. Som en del av den här processen måste du också:

    * Konfigurera ett lokalt huvud mål. Använd ett Windows huvud mål för virtuella Windows-datorer och ett [Linux-huvud mål](vmware-azure-install-linux-master-target.md) för virtuella Linux-datorer.
    * Konfigurera en [processerver](vmware-azure-set-up-process-server-azure.md).
    * Börja [skydda](vmware-azure-reprotect.md) igen för att stänga av den lokala virtuella datorn och synkronisera data för den virtuella Azure-datorn med de lokala diskarna.

2. När dina virtuella datorer på Azure replikeras till din lokala plats startar du en redundansväxling från Azure till den lokala platsen.

3. När dina data har återställts igen skyddar du de lokala virtuella datorerna igen så att de börjar replikera till Azure.

Om du vill ha en snabb översikt tittar du på följande video om hur du växlar tillbaka till en lokal plats:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Steg för att återställa

> [!IMPORTANT]
> Innan du startar failback måste du se till att du har återskapat återställningen av de virtuella datorerna. De virtuella datorerna bör vara i skyddat tillstånd och deras hälsa bör vara **OK**. Om du vill återaktivera skyddet av de virtuella datorerna läser [du skydda](vmware-azure-reprotect.md).

1. På sidan replikerade objekt väljer du den virtuella datorn. Högerklicka på den för att välja **oplanerad redundansväxling**.
2. I **Bekräfta redundans**kontrollerar du riktningen för redundans (från Azure). Välj sedan återställnings punkten (senaste eller den senaste appen konsekvent) som du vill använda för redundansväxlingen. Appens konsekventa punkt ligger bakom den senaste tidpunkten och leder till data förlust.
3. Under redundansväxlingen stänger Site Recovery ned de virtuella datorerna i Azure. När du har markerat att återställning efter fel har slutförts som förväntat kan du kontrol lera att de virtuella datorerna i Azure stängs av
4. **Incheckning** krävs för att ta bort den felande virtuella datorn från Azure. Högerklicka på det skyddade objektet och välj sedan **genomför**. Ett jobb tar bort de misslyckade virtuella datorerna i Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Vilken återställnings punkt kan jag återställa de virtuella datorerna?

Under återställning efter fel har du två alternativ för att återställa den virtuella datorn/återställnings planen.

- Om du väljer den senaste bearbetade tidpunkten växlar alla virtuella datorer över till sin senaste tillgängliga tidpunkt. Om det finns en replikeringsgrupp i återställnings planen växlar varje virtuell dator i replikeringsgruppen över till dess oberoende senaste tidpunkt.

  Det går inte att återställa en virtuell dator förrän den har minst en återställnings punkt. Du kan inte återställa en återställnings plan förrän alla virtuella datorer har minst en återställnings punkt.

  > [!NOTE]
  > En senaste återställnings punkt är en krasch-konsekvent återställnings punkt.

- Om du väljer den programkonsekventa återställnings punkten återställs en enskild återställning av en virtuell dator till den senaste tillgängliga programkonsekventa återställnings punkten. Om det finns en återställnings plan med en replikeringsgrupp återställs varje replikeringsgrupp till den gemensamma tillgängliga återställnings punkten.
Programkonsekventa återställnings punkter kan ligga efter i tiden och data kan gå förlorade.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Vad händer med VMware-verktyg efter återställning efter fel?

Om du använder en virtuell Windows-dator inaktiverar Site Recovery VMware-verktygen under redundansväxlingen. Vid återställning av den virtuella Windows-datorn aktive ras VMware-verktygen igen. 


## <a name="reprotect-from-on-premises-to-azure"></a>Återaktivera skydd från lokala datorer till Azure
När återställningen har slutförts och du har startat genomför tas de återställda virtuella datorerna i Azure bort. Nu är den virtuella datorn tillbaka på den lokala platsen, men den skyddas inte. Starta för att replikera till Azure igen genom att göra följande:

1. Välj **valv** > **inställningen** > **replikerade objekt**, Välj de virtuella datorer som inte kunde återställas och välj sedan **skydda igen**.
2. Ange värdet för processervern som ska användas för att skicka tillbaka data till Azure.
3. Välj **OK** för att starta återskydds jobbet.

> [!NOTE]
> När en lokal virtuell dator startas, tar det lite tid för agenten att registrera tillbaka till konfigurations servern (upp till 15 minuter). Under den här tiden Miss lyckas skydds åtgärden och returnerar ett fel meddelande om att agenten inte är installerad. Vänta några minuter och försök sedan att skydda igen.

## <a name="next-steps"></a>Nästa steg

När återskydds jobbet är klart replikeras den virtuella datorn tillbaka till Azure och du kan göra en [redundansväxling](site-recovery-failover.md) för att flytta dina virtuella datorer till Azure igen.


