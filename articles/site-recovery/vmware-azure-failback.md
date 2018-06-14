---
title: Växla tillbaka från Azure till VMware med Azure Site Recovery | Microsoft Docs
description: Du kan starta en återställning efter fel om du vill flytta virtuella datorer lokala efter en redundansväxling av virtuella datorer till Azure. Lär dig stegen att återställas.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 8f580fa40bade2bf586dd116729881b249bbba88
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29944015"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Växla tillbaka från Azure till en lokal plats

Den här artikeln beskriver hur du växla tillbaka virtuella datorer från Azure virtuella datorer till en lokal VMware-miljön. Följ instruktionerna i den här artikeln för att växla tillbaka din virtuella VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlats från lokalt platsen till Azure med hjälp av den [redundans i Azure Site Recovery](site-recovery-failover.md) kursen.

## <a name="prerequisites"></a>Förutsättningar
- Kontrollera att du har läst informationen om den [olika typer av återställning](concepts-types-of-failback.md) och motsvarande varningar.

> [!WARNING]
> Du kan inte växla tillbaka när du har antingen [slutfört en migrering](migrate-overview.md#what-do-we-mean-by-migration), flytta en virtuell dator till en annan resursgrupp eller ta bort den virtuella Azure-datorn. Om du inaktiverar skydd för den virtuella datorn, kan inte du återställas.

> [!WARNING]
> En fysisk server i Windows Server 2008 R2 SP1 kan inte om skyddade och växlas över till Azure, flyttas tillbaka.

> [!NOTE]
> Om du har redundansväxlade virtuella VMware-datorer kan du växlar tillbaka till en Hyper-V-värd.


- Innan du fortsätter kan du slutföra steg skydda igen så att de virtuella datorerna är i tillståndet replikerade och du kan starta en redundansväxling tillbaka till en lokal plats. Mer information finns i [så att skydda från Azure till lokala](vmware-azure-reprotect.md).

- Se till att vCenter är i anslutet tillstånd innan du gör en återställning efter fel. Annars misslyckas kopplar från diskar och koppla dem till den virtuella datorn.

- Vid redundans till Azure lokal plats kanske inte är tillgänglig och konfigurationsservern kanske inte tillgänglig eller är avstängd ned. Under skyddar och återställning efter fel ska lokalt konfigurationsservern körs och OK anslutet. 

- Den virtuella datorn måste finnas i configuration server-databasen under återställning efter fel, eller återställning lyckas inte. Kontrollera att du vidtar regelbundna schemalagda säkerhetskopieringar av servern. Om en olycka inträffar måste du återställa servern med den ursprungliga IP-adressen för återställning efter fel ska fungera.

- Huvudmålservern ska inte ha eventuella ögonblicksbilder innan skyddar/återställning efter fel.

## <a name="overview-of-failback"></a>Översikt över återställning efter fel
När du har redundansväxlats till Azure, kan du växla tillbaka till den lokala platsen genom att köra följande steg:

1. [Skapa nytt](vmware-azure-reprotect.md) de virtuella datorerna i Azure så att de startar replikeras till virtuella VMware-datorer i den lokala platsen. Som en del av den här processen kan behöva du också:

    * Konfigurera en lokal huvudmålservern. Använda en Windows-huvudmålserver för Windows-datorer och en [Linux-huvudmålserverns](vmware-azure-install-linux-master-target.md) för Linux virtuella datorer.
    * Konfigurera en [processervern](vmware-azure-set-up-process-server-azure.md).
    * Starta [skyddar](vmware-azure-reprotect.md) att stänga av den lokala virtuella datorn och synkronisera data för den Azure virtuella datorer med lokala diskar.

2. När dina virtuella datorer på Azure replikeras till den lokala platsen, starta en växling vid fel från Azure till den lokala platsen.

3. När dina data återställs skyddar du lokala virtuella datorer igen så att de startar replikera till Azure.

Titta på följande videoklipp om hur du växlar tillbaka till en lokal plats för en snabb överblick:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Steg för att återställa efter fel

> [!IMPORTANT]
> Innan du börjar återställning efter fel, kontrollera att du klar att återaktivera skydd för virtuella datorer. De virtuella datorerna ska vara i ett skyddat läge och deras hälsa bör vara **OK**. Om du vill skydda igen de virtuella datorerna, läsa [så att skydda](vmware-azure-reprotect.md).

1. Välj den virtuella datorn på sidan replikerade objekt. Högerklicka på den och välj **oplanerad växling**.
2. I **bekräfta redundans**, kontrollera redundansriktning (från Azure). Välj återställningspunkt (senaste eller den senaste appkonsekvent) som du vill använda för redundans. Appen konsekvent punkten finns bakom den senaste punkten i tid och gör att data kan gå förlorade.
3. Site Recovery stängs av de virtuella datorerna i Azure under växling vid fel. När du har kontrollerat att återställningen slutfördes korrekt, kan du kontrollera att de virtuella datorerna på Azure avstängd.
4. **Genomför** krävs för att ta bort den virtuella datorn misslyckades över från Azure. Högerklicka på det skyddade objektet och välj sedan **genomför**. Ett jobb tar bort misslyckades över virtuella datorer i Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Till vilken återställningspunkt kan jag växla tillbaka de virtuella datorerna?

Under återställning efter fel har två alternativ för att återställas virtuella/återställningsplanen.

- Om du väljer den senaste bearbetade punkten i tiden växla alla virtuella datorer över till sina senaste tillgängliga tidpunkten i tid. Om det finns en replikeringsgrupp i återställningsplanen måste flyttas varje virtuell dator i replikeringsgruppen över till dess oberoende senaste i tid.

  Du kan inte växla tillbaka en virtuell dator tills det har minst en återställningspunkt. Du kan inte växla tillbaka en återställningsplan tills alla virtuella datorer har minst en återställningspunkt.

  > [!NOTE]
  > Senaste återställningspunkt är en kraschkonsekvent återställningspunkt.

- Om du väljer programkonsekvent återställningspunkt återställer återställning av en enskild virtuell dator till den senaste tillgängliga programkonsekventa återställningspunkten. Varje replikeringsgrupp återställer till dess vanliga tillgänglig återställningspunkt för en återställningsplan med en replikeringsgrupp.
Programkonsekventa återställningspunkter kan vara bakom i tid, och det är möjligt att förlust av data.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Vad händer med VMware-verktyg efter återställning efter fel?

En virtuell dator Windows inaktiverar Site Recovery VMware-verktyg under växling vid fel. VMware-verktygen är reenabled under återställning efter fel för den virtuella datorn i Windows. 


## <a name="reprotect-from-on-premises-to-azure"></a>Skydda igen från lokal till Azure
När återställningen är klar och du har startat commit raderas de återställda virtuella datorerna i Azure. Nu kan den virtuella datorn är tillbaka på den lokala platsen, men det kommer inte att skydda. Om du vill starta replikera till Azure igen, gör du följande:

1. Välj **valvet** > **inställningen** > **replikerade objekt**, väljer de virtuella datorer som misslyckats igen och välj sedan  **Skydda igen**.
2. Ange värdet för processervern som ska användas för att skicka data till Azure.
3. Välj **OK** att börja skapa nytt jobb.

> [!NOTE]
> När en lokal virtuell dator startas, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid skyddar misslyckas och returnerar ett felmeddelande om att agenten inte är installerad. Vänta några minuter och försök sedan skyddar igen.

## <a name="next-steps"></a>Nästa steg

När skyddar jobbet har slutförts, de virtuella datorn replikeras tillbaka till Azure och du kan göra en [redundans](site-recovery-failover.md) att flytta virtuella datorer till Azure igen.


