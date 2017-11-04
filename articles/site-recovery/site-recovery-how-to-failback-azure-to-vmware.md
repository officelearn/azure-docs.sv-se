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
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 1ca34b262a51b694cb9541750588bbea139eeae1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Växla tillbaka från Azure till en lokal plats

Den här artikeln beskriver hur du växla tillbaka virtuella datorer från Azure virtuella datorer till den lokala platsen. Följ instruktionerna i den här artikeln för att växla tillbaka din virtuella VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlats från lokalt platsen till Azure med hjälp av den [replikera VMware-datorer och fysiska servrar med Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md) kursen.

> [!WARNING]
> Det går inte att återställning efter fel när du har antingen [slutfört en migrering](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), flytta en virtuell dator till en annan resursgrupp eller ta bort den virtuella Azure-datorn. Om du inaktiverar skydd för den virtuella datorn, kan du inte återställning efter fel.

> [!NOTE]
> Om du redundansväxlade virtuella VMware-datorer inte återställning till en Hyper-v-värd.

## <a name="overview-of-failback"></a>Översikt över återställning efter fel
Här är hur återställningen fungerar. När du har redundansväxlats till Azure växla tillbaka till den lokala platsen i några steg:

1. [Skapa nytt](site-recovery-how-to-reprotect.md) de virtuella datorerna i Azure så att de startar replikeras till virtuella VMware-datorer i den lokala platsen. Som en del av den här processen kan behöva du också:
    1. Konfigurera ett lokalt huvudmålservern: Windows huvudmålservern för den virtuella Windows-datorer och [Linux-huvudmålserverns](site-recovery-how-to-install-linux-master-target.md) för Linux virtuella datorer.
    2. Konfigurera en [processervern](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Initiera [skyddar](site-recovery-how-to-reprotect.md). Detta inaktiverar den lokala virtuella datorn och synkronisera den Azure virtuella data med lokala diskar.
5. När dina virtuella datorer på Azure replikeras till den lokala platsen, har du initiera en misslyckas över från Azure till den lokala platsen.

När data har misslyckats tillbaka, skyddar lokala virtuella datorer som du inte tillbaka till så att de startar replikera till Azure.

Titta på följande videoklipp om hur du växla över från Azure till en lokal plats för en snabb överblick.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Växla tillbaka till den ursprungliga eller en annan platsen

Om du redundansväxlade en virtuell VMware-dator kan du växla tillbaka till samma lokala virtuella källdatorn om det fortfarande finns. I det här scenariot replikeras bara ändringarna tillbaka. Det här scenariot kallas för återställning av ursprunglig plats. Om den lokala virtuella datorn inte finns är i scenario en återställning till alternativ plats.

> [!NOTE]
> Du kan endast återställning till den ursprungliga servern vCenter och konfiguration. Du kan inte distribuera en ny konfigurationsservern och återställning med hjälp av den. Dessutom du inte lägga till en ny vCenter befintliga konfigurationsservern och återställning efter fel i den nya vCenter.

#### <a name="original-location-recovery"></a>Återställning av ursprunglig plats

Om du växlar tillbaka till den ursprungliga virtuella datorn, krävs följande:
* Om den virtuella datorn hanteras av en vCenter-server ska på huvudmålservern ESX-värden ha åtkomst till databasen för den virtuella datorn.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter, måste hårddisken på den virtuella datorn vara i ett datalager som har åtkomst till värden på huvudmålservern.
* Om den virtuella datorn finns på en ESX-värd och inte använder vCenter, bör du genomföra identifieringen av ESX-värd på Huvudmålet innan du skyddar. Detta gäller att om du växlar tillbaka fysiska servrar för.
* Du kan växla tillbaka till ett virtuellt SAN-nätverk (vSAN) eller en disk som baseras på raw-enhet mappning (RDM) om diskarna finns redan och är anslutna till den lokala virtuella datorn.

#### <a name="alternate-location-recovery"></a>Återställning alternativ plats
Om den lokala virtuella datorn inte finns innan du skydda den virtuella datorn kallas scenariot en återställning till alternativ plats. Skapa nytt arbetsflöde skapar den lokala virtuella datorn igen. Detta medför även fullständiga data hämtas.

* När du växlar tillbaka till en alternativ plats för återställs den virtuella datorn till samma ESX-värd som är distribuerad på huvudmålservern. Databasen som används för att skapa disken kommer att samma datalager som valdes när skydda den virtuella datorn.
* Kan du växla tillbaka endast till en virtuell dator file system (VMFS) eller ett virtuellt SAN-datalagret. Om du har en RDM fungerar inte skyddar och återställning efter fel.
* Skapa nytt innebär en stor inledande dataöverföringen som följs av ändringarna. Den här processen finns eftersom den virtuella datorn inte finns lokalt. Fullständiga data måste replikeras tillbaka. Detta skyddar också tar längre tid än en återställning av ursprunglig plats.
* Du kan inte växla tillbaka till RDM-baserade diskar. Den nya virtuella diskar (VMDKs) kan bara skapas på en VMFS/virtuellt SAN-datalagret.

En fysisk dator när växlas över till Azure, kan flyttas tillbaka endast som en VMware-dator (kallas även P2A2V). Detta flöde faller under den alternativa platsåterställningen.

* En fysisk server i Windows Server 2008 R2 SP1 kan inte om skyddade och växlas över till Azure, flyttas tillbaka.
* Se till att identifiera minst ett huvudmålservern och nödvändiga ESX/ESXi-värdar som du behöver återställas.

## <a name="have-you-completed-reprotection"></a>Har du slutfört återaktivera skydd?
Innan du fortsätter du slutföra steg skydda igen så att de virtuella datorerna är i tillståndet replikerade och du kan påbörja en växling till en lokal plats. Mer information finns i [så att skydda från Azure till lokala](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Krav

> [!IMPORTANT]
> Vid redundans till Azure lokal plats kanske inte är tillgängligt och därför konfigurationsservern kan vara antingen icke tillgängliga eller avstängning. Under skyddar och återställning efter fel ska lokalt konfigurationsservern körs och OK anslutet.

* En konfigurationsserver krävs lokalt när du gör en återställning efter fel. Servern måste vara körs och ansluten till tjänsten så att dess hälsa är OK. Den virtuella datorn måste finnas i configuration server-databasen under återställning efter fel, eller återställning lyckas inte. Se därför till att du vidtar regelbundna schemalagda säkerhetskopieringar av servern. Om det fanns en katastrofåterställning, måste du återställa servern med samma IP-adress för återställning efter fel ska fungera.
* Huvudmålservern ska inte ha eventuella ögonblicksbilder innan skyddar/återställning efter fel.

## <a name="steps-to-fail-back"></a>Steg för att återställa efter fel

> [!IMPORTANT]
> Se till att du har slutfört återaktivera skydd för de virtuella datorerna innan du startar återställning efter fel. De virtuella datorerna ska vara i ett skyddat läge och deras hälsa bör vara **OK**. Om du vill skydda igen de virtuella datorerna, läsa [så att skydda](site-recovery-how-to-reprotect.md).

1. Välj den virtuella datorn på sidan replikerade objekt och högerklicka på att markera **oplanerad växling**.
2. I **bekräfta redundans**kontrollerar redundansriktning (från Azure) och Välj återställningspunkt (senaste eller den senaste appkonsekvent) som du vill använda för redundans. Appen konsekvent punkten finns bakom den senaste punkten i tid och gör att data kan gå förlorade.
3. Site Recovery stängs av de virtuella datorerna i Azure under växling vid fel. När du har kontrollerat att återställningen har slutförts korrekt kan du kontrollera att de virtuella datorerna på Azure har stängts av.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Till vilken återställningspunkt kan jag växla tillbaka de virtuella datorerna?

Under återställning efter fel har två alternativ för att återställas virtuella/återställningsplanen.

Om du väljer den senaste bearbetade punkten i tiden kommer alla virtuella datorer att flyttas över till sina senaste tillgängliga tidpunkten i tid. Det är en replikeringsgrupp i återställningsplanen, sedan växlar varje virtuell dator i replikeringsgruppen över till dess oberoende senaste i tid.

Du kan inte växla tillbaka en virtuell dator tills det har minst en återställningspunkt. Du kan inte växla tillbaka en återställningsplan tills alla virtuella datorer har minst en återställningspunkt.

> [!NOTE]
> Senaste återställningspunkt är en kraschkonsekvent återställningspunkt.

Om du väljer den programkonsekvent återställningspunkten återställer återställning av en enskild virtuell dator till den senaste tillgängliga programkonsekventa återställningspunkten. När det gäller en återställningsplan med en replikeringsgrupp återställs varje replikeringsgrupp till sina vanliga tillgängliga återställningspunkten.
Observera att programkonsekventa återställningspunkter kan vara bakom i tid, och det är möjligt att förlust av data.

### <a name="what-happens-to-vmware-tools-post-failback"></a>Vad händer med VMware-verktyg efter återställning efter fel?

VMware-verktyg kan inte köras på Azure-dator vid redundans till Azure. Vid en Windows-dator inaktiverar automatisk Systemåterställning VMware-verktyg under växling vid fel. Vid virtuell Linux-dator avinstallerar ASR VMware-verktyg under växling vid fel.

VMware-verktyg kan återaktiveras när återställning efter fel under återställning efter fel för den virtuella datorn i Windows. På samma sätt för en virtuell linux-dator installeras VMware-verktyg på datorn under återställning efter fel.

## <a name="next-steps"></a>Nästa steg

Du måste genomföra den virtuella datorn för att säkerställa att den återställda virtuella datorer i Azure raderas när återställningen är klar.

### <a name="commit"></a>Checka in
Commit krävs för att ta bort den redundansväxlade virtuella datorn från Azure.
Högerklicka på det skyddade objektet och klicka sedan på **genomför**. Ett jobb tar bort den misslyckade över virtuella datorer i Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Skydda igen från lokal till Azure

När genomförande har slutförts, den virtuella datorn är tillbaka på den lokala platsen, men det kommer inte att skydda. Om du vill starta replikera till Azure igen, gör du följande:

1. I **valvet** > **inställningen** > **replikerade objekt**, väljer de virtuella datorer som har misslyckats tillbaka och klicka sedan på  **Skydda igen**.
2. Ge värdet som ska användas för att skicka data till Azure.
3. Klicka på **OK** att börja skapa nytt jobb.

> [!NOTE]
> När en lokal virtuell dator startas, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid skyddar misslyckas och returnerar ett felmeddelande om att agenten inte är installerad. Vänta några minuter och försök sedan skyddar igen.

När skyddar jobbet har slutförts, den virtuella datorn replikeras tillbaka till Azure och du kan göra en växling vid fel.

## <a name="common-issues"></a>Vanliga problem
Se till att vCenter är i anslutet tillstånd innan du gör en återställning efter fel. Annars misslyckas kopplar från diskar och koppla dem till den virtuella datorn.
