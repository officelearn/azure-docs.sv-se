---
title: Redundansväxla från Azure under haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du växlar tillbaka till den lokala platsen efter redundans till Azure, under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 7773a2f43eb076075be484d92fde31094a2b584b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318129"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Återställ virtuella VMware-datorer och fysiska servrar från Azure till en lokal plats

Den här artikeln beskriver hur du växlar tillbaka virtuella datorer från Azure-datorer till en lokal VMware-miljö. Följ anvisningarna i den här artikeln för att växla tillbaka din VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlat från lokalerna platsen till Azure med hjälp av den [redundans i Azure Site Recovery](site-recovery-failover.md) självstudien.

## <a name="prerequisites"></a>Nödvändiga komponenter
- Se till att du har läst informationen om den [olika typer av återställning efter fel](concepts-types-of-failback.md) och motsvarande varningar.

> [!WARNING]
> Du kan inte tillbaka när du har antingen [slutfört en migrering](migrate-overview.md#what-do-we-mean-by-migration), flyttas en virtuell dator till en annan resursgrupp eller ta bort virtuella Azure-datorer. Om du inaktiverar skydd för den virtuella datorn, kan du inte återställa.

> [!WARNING]
> En fysisk server i Windows Server 2008 R2 SP1 det går inte att om skyddat och växlas över till Azure, att växlas tillbaka.

> [!NOTE]
> Om du har redundansväxlat virtuella VMware-datorer kan kan du inte återställa till en Hyper-V-värd.


- Innan du fortsätter kan du slutföra återaktivering av skydd steg så att de virtuella datorerna är i ett replikerade tillstånd och du kan starta en redundansväxling till en lokal plats. Mer information finns i [hur du kan skydda datorer igen från Azure till lokala](vmware-azure-reprotect.md).

- Se till att vCenter är i anslutet tillstånd innan du gör en återställning efter fel. Annars misslyckas kopplar från diskarna och bifoga dem tillbaka till den virtuella datorn.

- Under en redundansväxling till Azure, lokala platsen kanske inte är åtkomlig och konfigurationsservern kanske inte tillgänglig eller är avstängd ned. Under återaktivering av skydd och återställning efter fel ska den lokala konfigurationsservern körs och är i anslutet OK. 

- Den virtuella datorn måste finnas i configuration server-databas eller återställning efter fel lyckas inte under återställningen. Se till att du utför regelbundet schemalagda säkerhetskopieringar av servern. Om en olycka inträffar, som du vill återställa servern med den ursprungliga IP-adressen för återställning efter fel ska fungera.

- Huvudmålservern ska inte ha eventuella ögonblicksbilder innan du utlöser skydda igen/återställning efter fel.

## <a name="overview-of-failback"></a>Översikt över återställning efter fel
När du har redundansväxlat till Azure kan växla du tillbaka till din lokala plats genom att köra följande steg:

1. [Återaktivera skyddet av](vmware-azure-reprotect.md) de virtuella datorerna i Azure så att de börjar replikera till VMware-datorer i din lokala plats. Som en del av den här processen kan behöva du också:

    * Ställ in en den lokala huvudmålservern. Använda ett Windows-huvudmål för Windows virtuella datorer och en [Linux huvudmålservern](vmware-azure-install-linux-master-target.md) för Linux-datorer.
    * Konfigurera en [processervern](vmware-azure-set-up-process-server-azure.md).
    * Starta [återaktivera skyddet av](vmware-azure-reprotect.md) att Stäng av den lokala virtuella datorn och synkronisera data i den Azure virtuella datorer med lokala diskar.

2. När dina virtuella datorer i Azure replikeras till den lokala platsen, starta en redundansväxling från Azure till lokal plats.

3. När dina data återställs du återaktivera skyddet av lokala virtuella datorer igen så att de börjar replikera till Azure.

Titta på följande videoklipp om hur du växlar tillbaka till en lokal plats för en snabb överblick:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Steg för att växla tillbaka

> [!IMPORTANT]
> Kontrollera att du slutförde återaktiveringen av skyddet av de virtuella datorerna innan du börjar återställning efter fel. De virtuella datorerna ska vara i ett skyddat läge och deras hälsa bör vara **OK**. Om du vill återaktivera skyddet av de virtuella datorerna, läsa [hur du skyddar](vmware-azure-reprotect.md).

1. På sidan replikerade objekt väljer du den virtuella datorn. Högerklicka på den och välj **oplanerad redundans**.
2. I **bekräfta redundans**, kontrollera redundansriktning (från Azure). Välj återställningspunkten (senaste eller appen senaste konsekvent) som du vill använda för redundans. App konsekvent återställningspunkt är bakom den senaste tidpunkten i tid och gör att data kan gå förlorade.
3. Under redundansväxlingen stänger Site Recovery av de virtuella datorerna i Azure. När du har kontrollerat att återställningen har slutförts som förväntat kan du kontrollera att de virtuella datorerna på Azure avstängd.
4. **Genomför** krävs för att ta bort den redundansväxlade virtuella datorn från Azure. Högerklicka på det skyddade objektet och välj sedan **genomför**. Ett jobb tar bort de redundansväxlade virtuella datorerna i Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Till vilken återställningspunkt kan jag återställa dit de virtuella datorerna?

Under återställning efter fel har du två alternativ för att återställa den virtuella datorn/återställningsplanen.

- Om du väljer den senaste bearbetade tidpunkten i tid, växlar alla virtuella datorer över till sina senaste tillgängliga tidpunkten. Om det finns en replikeringsgrupp i återställningsplanen måste växlar varje virtuell dator i replikeringsgruppen över till dess oberoende senaste tidpunkt.

  Du kan inte återställa en virtuell dator förrän den har minst en återställningspunkt. Du kan inte återställa en återställningsplan förrän alla virtuella datorer har minst en återställningspunkten.

  > [!NOTE]
  > Senaste återställningspunkt är en kraschkonsekvent återställningspunkt.

- Om du väljer den programkonsekventa återställningspunkten återställer en enskild virtuell dator och återställning efter fel till sina senaste tillgängliga programkonsekventa återställningspunkten. När det gäller en återställningsplan med en replikeringsgrupp återställer varje replikeringsgrupp till dess vanliga tillgänglig återställningspunkt.
Programkonsekventa återställningspunkter kan ligga i tid och det kan finnas förlust i data.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Vad händer med VMware-verktyg efter återställning efter fel?

Om det finns en virtuell dator Windows inaktiverar Site Recovery VMware-verktyg under redundansväxlingen. VMware-verktyg är reenabled under återställningen av den virtuella datorn i Windows. 


## <a name="reprotect-from-on-premises-to-azure"></a>Skydda datorer igen från en lokal plats till Azure
När återställningen är klar och du har startat incheckning, raderas de återställda virtuella datorerna i Azure. Nu kan den virtuella datorn är tillbaka på den lokala platsen, men det skyddas inte. Så här startar kan du replikera till Azure igen, gör du följande:

1. Välj **Vault** > **inställningen** > **replikerade objekt**, Välj de virtuella datorerna som växlas tillbaka och välj sedan  **Skydda igen**.
2. Ange värdet för processervern som ska användas för att skicka data till Azure.
3. Välj **OK** att starta jobbet återaktivering av skydd.

> [!NOTE]
> När en lokal virtuell dator startas, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid återaktivering av skydd misslyckas och returnerar ett felmeddelande om att agenten inte är installerad. Vänta några minuter och försök sedan återaktivering av skydd igen.

## <a name="next-steps"></a>Nästa steg

Efter att återaktivera skydd jobbet har slutförts, de virtuella datorn replikeras tillbaka till Azure, och du kan göra en [redundans](site-recovery-failover.md) att flytta dina virtuella datorer till Azure igen.


