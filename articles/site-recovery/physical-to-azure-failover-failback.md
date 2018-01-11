---
title: "Växla över och återställas tillbaka fysiska servrar som replikeras till Azure med Site Recovery | Microsoft Docs"
description: "Lär dig hur du växla över fysiska servrar till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 08/01/2018
ms.author: raynew
ms.openlocfilehash: 2fbad3b53319c8dc8be3480162a4fa67bab94306
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Växla över och återställas tillbaka fysiska servrar som replikeras till Azure

Den här självstudiekursen beskrivs hur du växlar över en fysisk server till Azure. När du har redundansväxlats misslyckas servern tillbaka till den lokala platsen när den är tillgänglig. 

## <a name="preparing-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Fysiska servrar som replikeras till Azure med Site Recovery kan bara växla tillbaka som virtuella VMware-datorer. Du behöver en VMware-infrastruktur för att återställas. 

Redundans och återställning efter fel har fyra steg:

1. **Växla över till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Skyddar virtuella datorer i Azure**: skyddar Azure virtuella datorer, så att de startar replikeras tillbaka till lokala virtuella VMware-datorer.
3. **Växla över till lokala**: kör en redundansväxling för att växla tillbaka från Azure.
4. **Skapa nytt lokalt VMs**: när data har misslyckats tillbaka, skyddar det lokala virtuella VMware-datorer som du inte tillbaka till, så att de startar replikera till Azure.

## <a name="verify-server-properties"></a>Kontrollera serveregenskaperna för

Kontrollera serveregenskaperna för och kontrollera att den överensstämmer med [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) för virtuella Azure-datorer.

1. I **skyddade objekt**, klickar du på **replikerade objekt**, och välj datorn.

2. I den **replikerade objekt** , det finns en sammanfattning av information om datorn, hälsostatus, och den senaste tillgängliga återställningspunkter. Klicka på **egenskaper** visa mer information.
3. I **beräknings- och nätverksinställningar**, kan du ändra Azure namn, resursgruppens namn, Målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md), och [hanteras Diskinställningar](#managed-disk-considerations)
4. Du kan visa och ändra inställningar för nätverk, inklusive de undernät eller det nätverk som virtuella Azure-datorn kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den.
5. I **diskar**, visas information om operativsystemets och datadiskar.

## <a name="run-a-failover-to-azure"></a>Kör en redundansväxling till Azure

1. I **inställningar** > **replikerade objekt** klickar du på datorn > **redundans**.
2. I **redundans** väljer en **återställningspunkt** att växla över till. Du kan använda något av följande alternativ:
   - **Senaste** (standard): det här alternativet först bearbetar alla data som skickas till Site Recovery. De ger den lägsta RPO (mål för återställningspunkt) eftersom Azure VM skapas efter växling vid fel har alla data som har replikerats till Site Recovery när redundans utlöstes.
   - **Senaste bearbetas**: det här alternativet växlar datorn till den senaste återställningspunkten som bearbetas av Site Recovery. Det här alternativet ger en låga RTO (mål), eftersom ingen tid läggs obearbetade data bearbetas.
   - **Senaste programkonsekventa**: det här alternativet växlar datorn till den senaste programkonsekventa återställningspunkten bearbetas av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att stänga av källdatorn innan växling vid fel. Redundans fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
4. Om du förberett att ansluta till den virtuella Azure-datorn ansluta för att verifiera efter växling vid fel.
5. När du har kontrollerat **genomför** växling vid fel. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Inte avbryta en växling pågår. Innan du påbörjar för växling vid fel, stoppar replikeringen. Om du avbryter växling vid fel, stoppas, men datorn replikeras inte igen.
> Fysiska servrar kan ytterligare redundans bearbetningen ta cirka 8 till 10 minuter för att slutföra. 

## <a name="create-a-process-server-in-azure"></a>Skapa en processerver i Azure

Processervern tar emot data från den virtuella Azure-datorn och skickar det till den lokala platsen. Ett nätverk med låg latens krävs mellan processervern och den skydda datorn.

- Om du har en Azure ExpressRoute-anslutning för testning, kan du använda lokala processervern som installeras automatiskt på konfigurationsservern.
- Om du har en VPN-anslutning eller om du kör återställning efter fel i en produktionsmiljö, måste du konfigurera en virtuell dator i Azure som en Azure-baserade processerver för återställning efter fel.
- Följ instruktionerna i [i den här artikeln](site-recovery-vmware-setup-azure-ps-resource-manager.md) att ställa in en processerver i Azure.

## <a name="configure-the-master-target-server"></a>Konfigurera huvudmålservern

Som standard får huvudmålservern återställning av data. Körs på konfigurationsservern lokalt.

- Om VMware VM som du växlar tillbaka finns på en ESXi-värd som hanteras av VMware vCenter-servern, måste huvudmålservern ha åtkomst till den virtuella datorn datalagret (VMDK) att skriva replikerade data till VM-diskarna. Kontrollera att VM-datalagret har monterats på den huvudmålservern värd med läs-/ skrivbehörighet.
- Om ESXi-värd som inte hanteras av en vCenter-server, Site Recovery-tjänsten skapar en ny virtuell dator under återaktivera skydd. Den virtuella datorn skapas på ESX-värd som du skapar huvudmålservern VM. Hårddisken på den virtuella datorn måste vara i ett datalager som kan nås av de värden som huvudmålservern körs.
- För fysiska datorer som du växlar tillbaka, bör du genomföra identifiering av värden som huvudmålservern körs, innan du kan skydda igen datorn.
- Ett annat alternativ är om den lokala virtuella datorn finns redan för återställning efter fel, att ta bort det innan du gör en återställning efter fel. Återställning efter fel skapar sedan en ny virtuell dator på samma värddator som huvudmålservern ESX-värd. Data återställs till samma datalager och samma ESX-värden som används av den lokala huvudmålservern när du växlar tillbaka till en alternativ plats.
- Du kan använda Storage vMotion på huvudmålservern. Om du gör fungerar återställningen inte, eftersom diskarna inte är tillgängliga för den. Exkludera master målservrarna vMotion-listan.

## <a name="reprotect-azure-vms"></a>Skyddar virtuella Azure-datorer

Den här proceduren förutsätter att den lokala virtuella datorn inte är tillgänglig och att du skydda till en alternativ plats.

1. I **inställningar** > **replikerade objekt**, högerklicka på den virtuella datorn som har redundansväxlats > **skydda igen**.
2. I **skydda igen**, kontrollera att **Azure till lokala**, väljs.
3. Ange den lokala huvudmålservern och processervern.

4. I **Datastore**, Välj huvudmålservern databasen som du vill återställa den diskar lokalt. Använd det här alternativet när den lokala virtuella datorn har tagits bort och du behöver skapa nya diskar. Den här inställningen ignoreras om diskarna finns redan, men du behöver ange ett värde.
5. Välj kvarhållningsenhetens huvudmålservern. Principen för återställning väljs automatiskt.
6. Klicka på **OK** ska börja återaktivera skydd. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på den **jobb** fliken.

> [!NOTE]
> Om du vill återställa virtuella Azure-datorn till en befintlig lokal VM, montera den lokala virtuella datalagret med läs-/ skrivbehörighet på huvudmålservern serverns ESXi-värd.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Kör en redundans från Azure till lokala

Om du vill replikera tillbaka till lokala används en princip för återställning efter fel. Den här principen skapas automatiskt när du har skapat en replikeringsprincip för replikering till Azure:

- Principen associeras automatiskt med konfigurationsservern.
- Principen kan inte ändras.
- Principvärden är:
    - Återställningspunktmålet tröskelvärde = 15 minuter
    - Kvarhållningstid för återställningspunkten = 24 timmar
    - Frekvens av programkonsekventa ögonblicksbilder = 60 minuter

Kör en redundansväxling enligt följande:

1. På den **replikerade objekt** högerklickar du på datorn > **oplanerad växling**.
2. I **bekräfta redundans**, kontrollera att redundans-riktningen är från Azure.

3. Välj den återställningspunkt som du vill använda för redundans. En programkonsekventa återställningspunkt sker innan den senaste punkten i tid och den kommer att orsaka dataförluster. När redundans körs Site Recovery stängs av virtuella Azure-datorer och startar den virtuella datorn lokalt. Det kommer att vissa avbrott, så Välj en lämplig tidpunkt.
4. Högerklicka på datorn och på **genomför**. Detta utlöser ett jobb som tar bort den virtuella Azure-datorer.
5. Kontrollera att virtuella Azure-datorer har stängts av som förväntat.


## <a name="reprotect-on-premises-machines-to-azure"></a>Skapa nytt lokala datorer till Azure

Data bör nu vara tillbaka på den lokala platsen, men också replikeras den inte till Azure. Du kan börja replikering till Azure igen på följande sätt:

1. I valvet > **inställningar** > **replikerade objekt**, Välj misslyckade bakåt virtuella datorer som har misslyckats tillbaka och klicka på **skydda igen**.
2. Välj som används för att skicka replikerade data till Azure och klicka på **OK**.

När det återaktivera skyddet har slutförts den virtuella datorn replikeras tillbaka till Azure och du kan köra en växling vid fel efter behov.

