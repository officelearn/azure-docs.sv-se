---
title: "Växla över och återställas tillbaka fysiska servrar som replikeras till Azure med Site Recovery | Microsoft Docs"
description: "Lär dig hur du växla över fysiska servrar till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: bbad2a0ea1a58834eaf32e0d3286f6e8a794d364
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Växla över och återställas tillbaka fysiska servrar som replikeras till Azure

Den här självstudiekursen beskrivs hur du växlar över en fysisk server till Azure. När du har redundansväxlats misslyckas servern tillbaka till den lokala platsen när den är tillgänglig. 

## <a name="preparing-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Fysiska servrar som replikeras till Azure med Site Recovery kan bara växla tillbaka som virtuella VMware-datorer. Du behöver en VMware-infrastruktur för att återställas. 

Redundans och återställning efter fel består av fyra steg:

1. **Redundansväxla till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Skyddar virtuella datorer i Azure**: skyddar Azure virtuella datorer, så att de startar replikeras tillbaka till lokala virtuella VMware-datorer.
3. **Redundansväxla till lokal plats**: Kör en redundansväxling för att återställa från Azure.
4. **Skapa nytt lokalt VMs**: när data har misslyckats tillbaka, skyddar det lokala virtuella VMware-datorer som du inte tillbaka till, så att de startar replikera till Azure.

## <a name="verify-server-properties"></a>Kontrollera serveregenskaperna för

Kontrollera serveregenskaperna för och kontrollera att den överensstämmer med [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) för virtuella Azure-datorer.

1. I **skyddade objekt**, klickar du på **replikerade objekt**, och välj datorn.

2. I den **replikerade objekt** , det finns en sammanfattning av information om datorn, hälsostatus, och den senaste tillgängliga återställningspunkter. Klicka på **Egenskaper** för att se mer information.
3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och [hanterade diskinställningar](#managed-disk-considerations)
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **diskar**, visas information om operativsystemets och datadiskar.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **inställningar** > **replikerade objekt** klickar du på datorn > **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste** (standard): Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senaste bearbetas**: det här alternativet växlar datorn till den senaste återställningspunkten som bearbetas av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste programkonsekventa**: det här alternativet växlar datorn till den senaste programkonsekventa återställningspunkten bearbetas av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att stänga av källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. Om du har förberett att ansluta till den virtuella Azure-datorn, ansluter du för att kontrollera den efter redundansen.
5. När du är klar väljer du **Genomför** för redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Inte avbryta en växling pågår. Innan du påbörjar för växling vid fel, stoppar replikeringen. Om du avbryter växling vid fel, stoppas, men datorn replikeras inte igen.
> Fysiska servrar kan ytterligare redundans bearbetningen ta cirka 8 till 10 minuter för att slutföra. 

## <a name="create-a-process-server-in-azure"></a>Skapa en processerver i Azure

Processervern tar emot data från den virtuella Azure-datorn och skickar den till den lokala platsen. Ett nätverk med låg latens krävs mellan processervern och den skydda datorn.

- Om du har en Azure ExpressRoute-anslutning för testning, kan du använda den lokala processervern som installerades automatiskt på konfigurationsservern.
- Om du har en VPN-anslutning eller om du kör återställning efter fel i en produktionsmiljö, måste du konfigurera en virtuell dator i Azure som en Azure-baserad processerver för återställning efter fel.
- Följ instruktionerna i [i den här artikeln](site-recovery-vmware-setup-azure-ps-resource-manager.md) att ställa in en processerver i Azure.

## <a name="configure-the-master-target-server"></a>Konfigurera huvudmålservern

Som standard får huvudmålservern återställning av data. Körs på konfigurationsservern lokalt.

- Om VMware VM som du växlar tillbaka finns på en ESXi-värd som hanteras av VMware vCenter-servern, måste huvudmålservern ha åtkomst till den virtuella datorn datalagret (VMDK) att skriva replikerade data till VM-diskarna. Kontrollera att VM-datalagret har monterats på huvudmålserverns värd med läs- och skrivåtkomst.
- Om ESXi-värd som inte hanteras av en vCenter-server, Site Recovery-tjänsten skapar en ny virtuell dator under återaktivera skydd. Den virtuella datorn skapas på ESX-värd som du skapar huvudmålservern VM. Hårddisken på den virtuella datorn måste finnas i ett datalager som kan nås av den värd där huvudmålservern körs.
- För fysiska datorer som du växlar tillbaka, bör du genomföra identifiering av värden som huvudmålservern körs, innan du kan skydda igen datorn.
- Ett annat alternativ är om den lokala virtuella datorn finns redan för återställning efter fel, att ta bort det innan du gör en återställning efter fel. Återställningen efter fel skapar sedan en ny virtuell dator på samma värddator som är värd för huvudmålserverns ESX. När du återställer till en annan plats, återställs datan till samma datalager och samma ESX-värd som användes av den lokala huvudmålservern.
- Du kan inte använda Storage vMotion på huvudmålservern. Om du gör detta fungerar återställningen inte, eftersom diskarna inte är tillgängliga för den. Ta bort huvudmålservrarna från vMotion-listan.

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

