---
title: "Hur du växlar tillbaka från Azure till VMware | Microsoft Docs"
description: "Du kan starta en återställning efter fel om du vill flytta virtuella datorer lokala efter en redundansväxling av virtuella datorer till Azure. Lär dig stegen att återställas."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: 7a76ff45fe6209698532a7abc2d561bf7a7b3284
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Växla tillbaka från Azure till en lokal plats

Den här artikeln beskriver hur du växla tillbaka virtuella datorer från virtuella datorer i Azure till lokala VMware-miljön. Följ instruktionerna i den här artikeln för att växla tillbaka din virtuella VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlats från lokalt platsen till Azure med hjälp av den [redundans i Site Recovery](site-recovery-failover.md) kursen.

## <a name="prerequisites"></a>Förutsättningar
- Se till att du har läst informationen om den [olika typer av återställning](concepts-types-of-failback.md) och motsvarande varningar.

> [!WARNING]
> Det går inte att återställning efter fel när du har antingen [slutfört en migrering](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), flytta en virtuell dator till en annan resursgrupp eller ta bort den virtuella Azure-datorn. Om du inaktiverar skydd för den virtuella datorn, kan du inte återställning efter fel.

> [!WARNING]
> En fysisk server i Windows Server 2008 R2 SP1 kan inte om skyddade och växlas över till Azure, flyttas tillbaka.

> [!NOTE]
> Om du redundansväxlade virtuella VMware-datorer inte återställning till en Hyper-v-värd.


- Innan du fortsätter du slutföra steg skydda igen så att de virtuella datorerna är i tillståndet replikerade och du kan påbörja en växling till en lokal plats. Mer information finns i [så att skydda från Azure till lokala](site-recovery-how-to-reprotect.md).

- Se till att vCenter är i anslutet tillstånd innan du gör en återställning efter fel. Annars misslyckas kopplar från diskar och koppla dem till den virtuella datorn.

- Vid redundans till Azure lokal plats kanske inte är tillgängligt och därför konfigurationsservern kan vara antingen otillgänglig eller avstängning. Under skyddar och återställning efter fel ska lokalt konfigurationsservern körs och OK anslutet. 

- Den virtuella datorn måste finnas i configuration server-databasen under återställning efter fel, eller återställning lyckas inte. Se därför till att du vidtar regelbundna schemalagda säkerhetskopieringar av servern. Om det fanns en katastrofåterställning, måste du återställa servern med den ursprungliga IP-adressen för återställning efter fel ska fungera.

- Huvudmålservern ska inte ha eventuella ögonblicksbilder innan skyddar/återställning efter fel.

## <a name="overview-of-failback"></a>Översikt över återställning efter fel
När du har redundansväxlats till Azure, kan du växla tillbaka till den lokala platsen genom att köra följande steg:

1. [Skapa nytt](site-recovery-how-to-reprotect.md) de virtuella datorerna i Azure så att de startar replikeras till virtuella VMware-datorer i den lokala platsen. Som en del av den här processen kan behöva du också:
    1. Konfigurera ett lokalt huvudmålservern: Windows huvudmålservern för den virtuella Windows-datorer och [Linux-huvudmålserverns](site-recovery-how-to-install-linux-master-target.md) för Linux virtuella datorer.
    2. Konfigurera en [processervern](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Initiera [skyddar](site-recovery-how-to-reprotect.md). Detta inaktiverar den lokala virtuella datorn och synkronisera den Azure virtuella data med lokala diskar.

1. När dina virtuella datorer på Azure replikeras till den lokala platsen, har du initiera en misslyckas över från Azure till den lokala platsen.

1. När data har misslyckats tillbaka skyddar du lokala virtuella datorer igen, så att de startar replikera till Azure.

Titta på följande videoklipp om hur du växlar tillbaka till en lokal plats för en snabb överblick.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Steg för att återställa efter fel

> [!IMPORTANT]
> Se till att du har slutfört återaktivera skydd för de virtuella datorerna innan du startar återställning efter fel. De virtuella datorerna ska vara i ett skyddat läge och deras hälsa bör vara **OK**. Om du vill skydda igen de virtuella datorerna, läsa [så att skydda](site-recovery-how-to-reprotect.md).

1. Välj den virtuella datorn på sidan replikerade objekt och högerklicka på att markera **oplanerad växling**.
2. I **bekräfta redundans**kontrollerar redundansriktning (från Azure) och Välj återställningspunkt (senaste eller den senaste appkonsekvent) som du vill använda för redundans. Appen konsekvent punkten finns bakom den senaste punkten i tid och gör att data kan gå förlorade.
3. Site Recovery stängs av de virtuella datorerna i Azure under växling vid fel. När du har kontrollerat att återställningen har slutförts korrekt kan du kontrollera att de virtuella datorerna på Azure har stängts av.
4. **Genomför** krävs för att ta bort den redundansväxlade virtuella datorn från Azure.Right klickar du på det skyddade objektet och klicka sedan på **genomför**. Ett jobb tar bort den misslyckade över virtuella datorer i Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Till vilken återställningspunkt kan jag växla tillbaka de virtuella datorerna?

Under återställning efter fel har två alternativ för att återställas virtuella/återställningsplanen.

- Om du väljer den senaste bearbetade punkten i tiden kommer alla virtuella datorer att flyttas över till sina senaste tillgängliga tidpunkten i tid. I händelse av, en replikeringsgrupp i återställningsplanen, sedan växlar varje virtuell dator i replikeringsgruppen över till dess oberoende senaste i tid.

    Du kan inte växla tillbaka en virtuell dator tills det har minst en återställningspunkt. Du kan inte växla tillbaka en återställningsplan tills alla virtuella datorer har minst en återställningspunkt.

> [!NOTE]
> Senaste återställningspunkt är en kraschkonsekvent återställningspunkt.

- Om du väljer den programkonsekvent återställningspunkten återställer återställning av en enskild virtuell dator till den senaste tillgängliga programkonsekventa återställningspunkten. Varje replikeringsgrupp återställer till dess vanliga tillgänglig återställningspunkt för en återställningsplan med en replikeringsgrupp.
Programkonsekventa återställningspunkter kan vara bakom i tid, och det är möjligt att förlust av data.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Vad händer med VMware-verktyg efter återställning efter fel?

Vid en Windows-dator inaktiverar VMware-verktyg i Azure Site Recovery under växling vid fel. VMware-verktyg kan återaktiveras under återställning efter fel för den virtuella datorn i Windows. 


## <a name="reprotect-from-on-premises-to-azure"></a>Skydda igen från lokal till Azure
När återställningen är klar och du har initierat commit bort de återställda virtuella datorerna i Azure. Nu kan den virtuella datorn är tillbaka på den lokala platsen, men det kommer inte att skydda. Om du vill starta replikera till Azure igen, gör du följande:

1. I **valvet** > **inställningen** > **replikerade objekt**, väljer de virtuella datorer som har misslyckats tillbaka och klicka sedan på  **Skydda igen**.
2. Ge värdet som ska användas för att skicka data till Azure.
3. Klicka på **OK** att börja skapa nytt jobb.

> [!NOTE]
> När en lokal virtuell dator startas, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid skyddar misslyckas och returnerar ett felmeddelande om att agenten inte är installerad. Vänta några minuter och försök sedan skyddar igen.

## <a name="next-steps"></a>Nästa steg

När jobbet skyddar är klar, den virtuella datorn replikeras tillbaka till Azure och du kan göra en [redundans](site-recovery-failover.md) att flytta virtuella datorer till Azure igen.


