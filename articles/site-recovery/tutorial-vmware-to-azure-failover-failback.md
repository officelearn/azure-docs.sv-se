---
title: "Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar som replikeras till Azure med Site Recovery | Microsoft Docs"
description: "Lär dig hur du redundansväxlar virtuella VMware-datorer och fysiska servrar till Azure och återställer dem till den lokala platsen med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f074312ecee39d4b3022df64b51aadd2bb8f968c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar som replikeras till Azure

I den här självstudien beskrivs hur du redundansväxlar en virtuell VMware-dator till Azure. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera egenskaperna för den virtuella VMware-datorn för att se att den överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Skapa en processerver och huvudmålserver för återställning efter fel
> * Återaktivera skyddet av virtuella Azure-datorer till den lokala platsen
> * Redundansväxla från Azure till lokal plats
> * Återaktivera skyddet av lokala virtuella datorer för att börja replikera till Azure igen

Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-vmware.md)
3. [Konfigurera haveriberedskap](tutorial-vmware-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Kontrollera att det inte finns några ögonblicksbilder på den virtuella datorn. Den lokala virtuella datorn stängs av under återaktiveringen av skyddet.
Detta hjälper att säkerställa datakonsekvens vid replikeringen. Sätt inte på den virtuella datorn förrän återaktiveringen av skyddet har slutförts. Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvudmålserver för att återaktivera skyddet av en replikeringsgrupp, får inte servern en gemensam tidpunkt.

Redundans och återställning efter fel består av fyra steg:

1. **Redundansväxla till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Skydda virtuella Azure-datorer**: Skyddar virtuella Azure-datorer så att de börjar replikera tillbaka till de lokala virtuella VMware-datorerna.
3. **Redundansväxla till lokal plats**: Kör en redundansväxling för att återställa från Azure.
4. **Återaktivera skyddet av lokala virtuella datorer**: När data har återställts återaktiveras skyddet av de lokala virtuella datorer som du återställde till, så att de börjar replikera till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera VM-egenskaperna och se till att den virtuella datorn uppfyller [Azure-kraven](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och [hanterade diskinställningar](#managed-disk-considerations)

4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.

2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste** (standard): Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senaste bearbetade**: Alternativet redundansväxlar den virtuella datorn till den senaste återställningspunkt som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar den virtuella datorn till den senaste appkonsekventa återställningspunkten som bearbetats av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.

4. Om du har förberett att ansluta till den virtuella Azure-datorn, ansluter du för att kontrollera den efter redundansen.

5. När du är klar väljer du **Genomför** för redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas stoppas replikeringen av den virtuella datorn.
> Om du avbryter en pågående redundans stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Skapa en processerver i Azure

Processervern tar emot data från den virtuella Azure-datorn och skickar den till den lokala platsen. Ett nätverk med kort svarstid krävs mellan processervern och den skyddade virtuella datorn.

- Om du har en Azure ExpressRoute-anslutning för testning, kan du använda den lokala processervern som installerades automatiskt på konfigurationsservern.
- Om du har en VPN-anslutning eller om du kör återställning efter fel i en produktionsmiljö, måste du konfigurera en virtuell dator i Azure som en Azure-baserad processerver för återställning efter fel.
- Om du vill konfigurera en processerver i Azure följer du instruktionerna i [den här artikeln](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Konfigurera huvudmålservern

Som standard körs huvudmålservern på konfigurationsservern lokalt. I den här självstudien använder vi standardhuvudservern. Huvudmålservern tar emot data vid återställning efter fel.

Om den virtuella datorn finns på en ESXi-värd som hanteras av en vCenter-server, måste huvudmålservern ha åtkomst till den virtuella datorns datalager (VMDK) för att kunna skriva replikerade data till VM-diskarna. Kontrollera att VM-datalagret har monterats på huvudmålserverns värd med läs- och skrivåtkomst.

Om den virtuella datorn finns på en ESXi som inte hanteras av en vCenter-server, skapar Site Recovery-tjänsten en ny virtuell dator vid återaktiveringen av skyddet. Den virtuella datorn skapas på ESX-värden där du skapade huvudmålet.
Hårddisken på den virtuella datorn måste finnas i ett datalager som kan nås av den värd där huvudmålservern körs.

Om den virtuella datorn inte använder vCenter, bör du genomföra identifiering av värden där huvudmålservern körs, innan du kan skydda datorn igen. Detta gäller även vid återställning av fysiska servrar. Ett annat alternativ, om det finns lokala virtuella datorer, är att ta bort den innan du gör en återställning efter fel. Återställningen efter fel skapar sedan en ny virtuell dator på samma värddator som är värd för huvudmålserverns ESX. När du återställer till en annan plats, återställs datan till samma datalager och samma ESX-värd som användes av den lokala huvudmålservern.

Du kan inte använda Storage vMotion på huvudmålservern. Om du gör detta fungerar återställningen inte, eftersom diskarna inte är tillgängliga för den. Ta bort huvudmålservrarna från vMotion-listan.

## <a name="reprotect-azure-vms"></a>Återaktivera skyddet av virtuella Azure-datorer

Den här proceduren förutsätter att den lokala virtuella datorn inte är tillgänglig och att du återaktiverar skyddet på en alternativ plats.

1. I **Inställningar** > **Replikerade objekt** högerklickar du på den virtuella datorn som har redundansväxlats > **Återaktivera skydd**.
2. I **Återaktivera skydd** kontrollerar du att **Azure till lokal plats** är valt.
3. Ange den lokala huvudmålservern och processervern.

4. I **Datalager** välj du det huvudmåldatalager som du vill återställa diskarna till lokalt. Använd det här alternativet när den lokala virtuella datorn har tagits bort och du behöver skapa nya diskar. Inställningarna ignoreras om diskarna redan finns, men du måste ange ett värde.
5. Välj kvarhållningsenhetens huvudmål. Failback-principen väljs automatiskt.
6. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.

> [!NOTE]
> Om du vill återställa den virtuella Azure-datorn till en befintlig lokal VM, monterar du den lokala virtuella datorns datalager med läs-/skrivåtkomst på huvudmålserverns ESXi-värd.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Kör en redundans från Azure till en lokal plats

Om du vill replikera tillbaka lokalt används en failback-princip. Den här principen skapas automatiskt när du har skapat en replikeringsprincip för replikering till Azure:

- Principen associeras automatiskt med konfigurationsservern.
- Principen kan inte ändras.
- Principvärdena är:
    - Tröskelvärde för återställningspunktmålet = 15 minuter
    - Återställningspunkt för kvarhållning = 24 timmar
    - Frekvens för appkonsekvent ögonblicksbild = 60 minuter

Kör en redundans enligt följande:

1. På sidan **Replikerade objekt** högerklickar du på datorn > **Oplanerad redundans**.
2. I **Bekräfta redundans** kontrollerar du att redundansriktningen är från Azure.

3. Välj en återställningspunkt som ska användas för redundansen. En appkonsekvent återställningspunkt utförs före den senaste tidpunkten och den kommer att orsaka viss dataförlust. När redundansen körs stänger Site Recovery av de virtuella Azure-datorerna och startar den lokala virtuella datorn. Det kommer att bli vissa avbrott, så du bör välja en lämplig tidpunkt.
4. Högerklicka på datorn och klicka på **Genomför**. Detta utlöser ett jobb som tar bort de virtuella Azure-datorerna.
5. Kontrollera att de virtuella Azure-datorerna har stängts av som förväntat.


## <a name="reprotect-on-premises-machines-to-azure"></a>Återaktivera skyddet av lokala datorer till Azure

Dina data bör nu finnas på den lokala platsen igen, men de replikeras inte till Azure. Du kan påbörja replikeringen till Azure igen på följande sätt:

1. I valvet > **Inställningar** >**Replikerade objekt** väljer du de återställda virtuella datorerna och klickar på **Återaktivera skydd**.
2. Välj den processerver som används för att skicka replikerade data till Azure och klicka på **OK**.

När återaktiveringen av skyddet har slutförts kommer den virtuella datorn replikera tillbaka till Azure och du kan köra en redundans vid behov.
