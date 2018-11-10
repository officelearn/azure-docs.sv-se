---
title: Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar vid haveriberedskap till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du redundansväxlar virtuella VMware-datorer och fysiska servrar till Azure och återställer dem till den lokala platsen vid haveriberedskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a574c28cae449526ddc8fe0fbb9323fb14b7b6f1
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215352"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar som replikeras till Azure

I den här självstudien beskrivs hur du redundansväxlar en virtuell VMware-dator till Azure. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera egenskaperna för den virtuella VMware-datorn för att se att den överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Skapa en processerver och huvudmålserver för återställning efter fel
> * Återaktivera skyddet av virtuella Azure-datorer till den lokala platsen
> * Redundansväxla från Azure till lokal plats
> * Återaktivera skyddet av lokala virtuella datorer för att börja replikera till Azure igen

>[!NOTE]
>Självstudierna är utformade för att visa den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill veta mer om redundansteststegen kan du läsa [Anvisningsguiden](site-recovery-failover.md).

Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](vmware-azure-tutorial-prepare-on-premises.md)
3. [Konfigurera haveriberedskap](vmware-azure-tutorial.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)
5. Utöver ovanstående steg är det bra att [granska arkitekturen](vmware-azure-architecture.md) för haveriberedskapscenariot.

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har fyra stadier:

1. **Redundansväxla till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Skydda virtuella Azure-datorer**: Skyddar virtuella Azure-datorer så att de börjar replikera tillbaka till de lokala virtuella VMware-datorerna. Den lokala virtuella datorn stängs av under återaktiveringen av skyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen.
3. **Redundansväxla till lokal plats**: Kör en redundansväxling för att återställa från Azure.
4. **Återaktivera skyddet av lokala virtuella datorer**: När data har återställts återaktiveras skyddet av de lokala virtuella datorer som du återställde till, så att de börjar replikera till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera VM-egenskaperna och se till att den virtuella datorn uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines).

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och [hanterade diskinställningar](#managed-disk-considerations)

4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **Redundans**.

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senaste bearbetade**: Alternativet redundansväxlar den virtuella datorn till den senaste återställningspunkt som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar den virtuella datorn till den senaste appkonsekventa återställningspunkten som bearbetats av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att **redundanstiden är längre** för virtuella VMWare-datorer som använder en mobilitetstjänst som är äldre än version 9.8, fysiska servrar, virtuella VMWare Linux-datorer, virtuella Hyper-V-datorer som skyddas som fysiska servrar, virtuella VMWare-datorer som inte har DHCP-tjänsten aktiverad och virtuella VMWare-datorer som inte har följande drivrutiner: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas så stoppas replikeringen av den virtuella datorn.
> Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Ansluta till redundansväxlade virtuella datorer i Azure

1. Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter en redundansväxling följer du kraven som sammanfattas i tabellen [här](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. After redundans går du till den virtuella datorn och verifierar den genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. När du har verifierat klickar du på **Genomför** och slutför återställningspunkten för den virtuella datorn efter redundans. När detta genomförs tas alla andra tillgängliga återställningspunkter bort. Nu är du klart med redundans-aktiviteten.

>[!TIP]
> Med **Ändra återställningspunkt** kan du välja en annan återställningspunkt efter redundans, om du inte är nöjd med den redundansväxlade virtuella datorn. När du har tryckt på **Genomför** är det här alternativet inte längre tillgängligt.

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Förbereda för återaktivering av den virtuella Azure-datorn

- Du kan använda den lokala processervern (den inbyggda processervern) som installerades automatiskt på konfigurationsservern som en del av konfigurationen **om du har en Azure ExpressRoute-anslutning**.

> [!IMPORTANT]
> Om du har en VPN-anslutning mellan din lokala miljö och Azure måste du konfigurera en virtuell Azure-dator som en processerver för återaktivering av skydd och återställning efter fel. Om du vill konfigurera en processerver i Azure följer du instruktionerna i [den här artikeln](vmware-azure-set-up-process-server-azure.md).

Mer information om kraven för återaktivering av skydd och återställning efter fel finns i det här [avsnittet] ](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Konfigurera huvudmålservern

Huvudmålservern tar emot och hanterar replikeringsdata vid återställning efter fel från Azure. Som standard finns den tillgänglig på den lokala konfigurationsservern. I den här självstudien använder vi standardhuvudmålservern.

>[!NOTE]
>Du behöver skapa en separat huvudmålserver om du vill skydda en Linux-baserad virtuell dator. [Klicka här](vmware-azure-install-linux-master-target.md) om du vill läsa mer.

Om den virtuella datorn finns på en **ESXi-värd som hanteras av en vCenter**-server, måste huvudmålservern ha åtkomst till den virtuella datorns datalager (VMDK) om den ska kunna skriva replikerade data till VM-diskarna. Kontrollera att VM-datalagret har monterats på huvudmålserverns värd med läs- och skrivåtkomst.

Om den virtuella datorn finns på en **ESXi som inte hanteras av en vCenter**-server, skapar Site Recovery-tjänsten en ny virtuell dator vid återaktiveringen av skyddet. Den virtuella datorn skapas på ESX-värden där du skapade huvudmålet.
Hårddisken på den virtuella datorn måste finnas i ett datalager som kan nås av den värd där huvudmålservern körs.

Om den virtuella datorn **inte använder vCenter** bör du genomföra identifiering av värden där huvudmålservern körs, innan du kan skydda datorn igen. Detta gäller även vid återställning av fysiska servrar. Ett annat alternativ, om det finns lokala virtuella datorer, är att ta bort den innan du gör en återställning efter fel. Återställningen efter fel skapar sedan en ny virtuell dator på samma värddator som är värd för huvudmålserverns ESX. När du återställer till en annan plats, återställs datan till samma datalager och samma ESX-värd som användes av den lokala huvudmålservern.

Du kan inte använda Storage vMotion på huvudmålservern. Om du gör detta fungerar återställningen inte, eftersom diskarna inte är tillgängliga för den. Ta bort huvudmålservrarna från vMotion-listan.

>[!Warning]
>Om du använder en annan huvudmålserver för att återaktivera skyddet av en replikeringsgrupp, får inte servern en gemensam tidpunkt.

## <a name="reprotect-azure-vms"></a>Återaktivera skyddet av virtuella Azure-datorer

Att återaktivera skyddet av virtuella Azure-datorer leder till replikering av data till den lokala virtuella datorn. Det här är ett obligatoriskt steg innan du kör en redundans från Azure till den lokala virtuella datorn. Följ anvisningarna nedan om du vill återaktivera skyddet.

1. I **Inställningar** > **Replikerade objekt** högerklickar du på den virtuella datorn som har redundansväxlats > **Återaktivera skydd**.
2. I **Återaktivera skydd** kontrollerar du att **Azure till lokal plats** är valt.
3. Ange den lokala huvudmålservern och processervern.
4. I **Datalager** välj du det huvudmåldatalager som du vill återställa diskarna till lokalt. Om den virtuella datorn har tagits bort skapas nya diskar på det här datalagret. Inställningen ignoreras om diskarna redan finns, men du måste ange ett värde.
5. Välj kvarhållningsenhetens huvudmål. Failback-principen väljs automatiskt.
6. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.
7. När, under **Replikerade objekt**, den virtuella datorns status ändras till **Skyddad** är datorn redo för redundans till lokalt.

> [!NOTE]
> Den virtuella Azure-datorn kan återställas till en befintlig lokal virtuell dator eller till en annan plats. Mer information finns i [den här artikeln](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Kör en redundans från Azure till en lokal plats

Om du vill replikera tillbaka lokalt används en failback-princip. Den här principen skapas automatiskt när du har skapat en replikeringsprincip för replikering till Azure:

- Principen associeras automatiskt med konfigurationsservern.
- Principen kan inte ändras.
- Principvärdena är:
    - Tröskelvärde för återställningspunktmålet = 15 minuter
    - Återställningspunkt för kvarhållning = 24 timmar
    - Frekvens för appkonsekvent ögonblicksbild = 60 minuter

Kör en redundans enligt följande:

1. På sidan **Replikerade objekt** högerklickar du på datorn > **Redundans**.
2. I **Bekräfta redundans** kontrollerar du att redundansriktningen är från Azure.
    ![redundansriktning](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Välj en återställningspunkt som ska användas för redundansen. En appkonsekvent återställningspunkt utförs före den senaste tidpunkten och den kommer att orsaka viss dataförlust.

    >[!WARNING]
    >När redundansen körs stänger Site Recovery av de virtuella Azure-datorerna och startar den lokala virtuella datorn. Det kommer att bli vissa avbrott, så du bör välja en lämplig tidpunkt.

4. Du kan följa förloppet för jobbet på **Recovery Services-valv** > **Övervakning och rapporter** > **Site Recovery-jobb**.
5. När redundans har slutförts högerklickar du på den virtuella datorn och klickar på **Genomför**. Detta utlöser ett jobb som tar bort de virtuella Azure-datorerna.
6. Kontrollera att de virtuella Azure-datorerna har stängts av som förväntat.

## <a name="reprotect-on-premises-machines-to-azure"></a>Återaktivera skyddet av lokala datorer till Azure

Dina data bör nu finnas lokalt igen, men de replikeras inte till Azure. Du kan påbörja replikeringen till Azure igen på följande sätt:

1. I valvet > **Skyddade objekt** >**Replikerade objekt**, väljer du den återställda virtuella datorn och klickar på **Återaktivera skydd**.
2. Välj den processerver som ska användas för att skicka replikerade data till Azure och klicka på **OK**.

När återaktiveringen av skyddet har slutförts kommer den virtuella datorn replikera tillbaka till Azure och du kan köra en redundans vid behov.
