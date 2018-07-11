---
title: Redundans och återställning av fysiska servrar som replikeras till Azure med Site Recovery att misslyckas | Microsoft Docs
description: Lär dig hur du växlar över fysiska servrar till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 93f62bac3e2207caa265b3fca6634656d64b1491
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918245"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Redundans och växla tillbaka fysiska servrar som replikeras till Azure

Den här självstudien beskrivs hur du växlar över en fysisk server till Azure. När du har redundansväxlat kan växla du servern tillbaka till din lokala plats när den är tillgänglig. 

## <a name="preparing-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Fysiska servrar som replikeras till Azure med Site Recovery kan bara växla tillbaka som virtuella VMware-datorer. Du behöver en VMware-infrastruktur för att återställas. 

Redundans och återställning efter fel består av fyra steg:

1. **Redundansväxla till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Återaktivera skyddet av virtuella Azure-datorer**: återaktivera skyddet för den virtuella Azure-datorer så att de börjar replikera tillbaka till den lokala virtuella VMware-datorer.
3. **Redundansväxla till lokal plats**: Kör en redundansväxling för att återställa från Azure.
4. **Återaktivering av skydd lokala virtuella datorer**: när data har återställts, återaktivera skyddet för den lokala virtuella VMware-datorer som du återställde till, så att de börjar replikera till Azure.

## <a name="verify-server-properties"></a>Kontrollera serveregenskaperna för

Kontrollera egenskaperna för servern och se till att den överensstämmer med [krav för Azure](vmware-physical-azure-support-matrix.md#replicated-machines) för virtuella Azure-datorer.

1. I **skyddade objekt**, klickar du på **replikerade objekt**, och välj datorn.

2. I den **replikerat objekt** rutan finns en sammanfattning av informationen, hälsostatus för datorn och den senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och [hanterade diskinställningar](#managed-disk-considerations)
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **diskar**, du kan se information om datorns operativsystem och datadiskar.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste** (standard): Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senaste bearbetade**: det här alternativet redundansväxlar datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste appkonsekventa**: det här alternativet redundansväxlar datorn till den senaste appkonsekventa återställningspunkten som bearbetats av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery försöker stänga av källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. Om du har förberett att ansluta till den virtuella Azure-datorn, ansluter du för att kontrollera den efter redundansen.
5. När du är klar väljer du **Genomför** för redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Avbryt inte en pågående redundansväxling. Innan redundansväxling har startat, stoppar replikeringen av datorn. Om du avbryter växling vid fel, stoppas, men datorn kommer inte att replikeras igen.
> För fysiska servrar ta redundans ytterligare bearbetning cirka 8 till 10 minuter för att slutföra. 

## <a name="create-a-process-server-in-azure"></a>Skapa en processerver i Azure

Processervern tar emot data från den virtuella Azure-datorn och skickar den till den lokala platsen. Ett nätverk med låg latens krävs mellan processervern och den skyddade datorn.

- Om du har en Azure ExpressRoute-anslutning för testning, kan du använda den lokala processervern som installerades automatiskt på konfigurationsservern.
- Om du har en VPN-anslutning eller om du kör återställning efter fel i en produktionsmiljö, måste du konfigurera en virtuell dator i Azure som en Azure-baserad processerver för återställning efter fel.
- Följ instruktionerna i [i den här artikeln](vmware-azure-set-up-process-server-azure.md) att ställa in en processerver i Azure.

## <a name="configure-the-master-target-server"></a>Konfigurera huvudmålservern

Som standard huvudmålservern tar emot data för återställning efter fel. Den körs på den lokala konfigurationsservern.

- Om VMware-VM som du inte återställa är på en ESXi-värd som hanteras av VMware vCenter-Server, måste huvudmålservern ha åtkomst till den Virtuella datorns datalager (VMDK) att skriva replikerade data till VM-diskarna. Kontrollera att VM-datalagret har monterats på huvudmålserverns värd med läs- och skrivåtkomst.
- Om ESXi-värden som inte hanteras av en vCenter-server, Site Recovery-tjänsten skapar en ny virtuell dator vid återaktiveringen av skyddet. Den virtuella datorn skapas på ESX-värden som du skapade Huvudmålet VM. Hårddisken på den virtuella datorn måste vara i ett datalager som kan nås av den värd där huvudmålservern körs.
- För fysiska datorer som du inte återställa, bör du genomföra identifiering av den värd där huvudmålservern körs, innan du kan skydda datorn igen.
- Ett annat alternativ är om det finns redan en lokal virtuell dator för återställning efter fel, att ta bort den innan du gör en återställning efter fel. Återställningen efter fel skapar sedan en ny virtuell dator på samma värddator som är värd för huvudmålserverns ESX. När du återställer till en annan plats, återställs datan till samma datalager och samma ESX-värd som användes av den lokala huvudmålservern.
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

Data bör nu vara tillbaka på din lokala plats, men de replikeras inte till Azure. Du kan påbörja replikeringen till Azure igen på följande sätt:

1. I valvet > **Inställningar** >**Replikerade objekt** väljer du de återställda virtuella datorerna och klickar på **Återaktivera skydd**.
2. Välj den processerver som används för att skicka replikerade data till Azure och klicka på **OK**.

När återaktiveringen av skyddet har slutförts kommer den virtuella datorn replikera tillbaka till Azure och du kan köra en redundans vid behov.

