---
title: Reprotera virtuella virtuella datorer med VMware till en lokal plats med Azure Site Recovery
description: Lär dig hur du roterar om virtuella virtuella datorer med VMware efter redundans till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257178"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Skydda datorer igen från Azure till lokalt

Efter [redundans](site-recovery-failover.md) av lokala virtuella datorer med vmware eller fysiska servrar till Azure, är det första steget i att misslyckas tillbaka till din lokala webbplats att återrotera de virtuella Azure-datorer som skapades under redundans. I den här artikeln beskrivs hur du gör detta. 

## <a name="before-you-begin"></a>Innan du börjar

1. Följ stegen i den [här artikeln](vmware-azure-prepare-failback.md) för att förbereda för omskydd och återställning, inklusive att konfigurera en processserver i Azure och en lokal huvudmålserver, och konfigurera en plats-till-plats-VPN eller ExpressRoute privat peering, för återställning av återställning av återställning av återställning.
2. Kontrollera att den lokala konfigurationsservern körs och ansluts till Azure. Under redundans till Azure kanske den lokala platsen inte är tillgänglig och konfigurationsservern kanske inte är tillgänglig eller stängs av. Under återställning efter fel måste den virtuella datorn finnas i konfigurationsserverdatabasen. Annars misslyckas återställningen av återställningen.
3. Ta bort alla ögonblicksbilder på den lokala huvudmålservern. Återskydd fungerar inte om det finns ögonblicksbilder.  Ögonblicksbilderna på den virtuella datorn slås automatiskt samman under ett reprotect-jobb.
4. Om du återbeskyddar virtuella datorer som samlats in i en replikeringsgrupp för konsekvens med flera virtuella datorer kontrollerar du att alla har samma operativsystem (Windows eller Linux) och ser till att huvudmålservern som du distribuerar har samma typ av operativsystem. Alla virtuella datorer i en replikeringsgrupp måste använda samma huvudmålserver.
5. Öppna [de portar som krävs](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) för återställning efter fel.
6. Kontrollera att vCenter-servern är ansluten före återställningen. Annars misslyckas det att koppla diskar och koppla tillbaka dem till den virtuella datorn.
7. Om en vCenter-server hanterar de virtuella datorer som du ska återställa ska kontrollera att du har de behörigheter som krävs. Om du utför en skrivskyddad användare vCenter-identifiering och skyddar virtuella datorer, lyckas skyddet och redundansfungering fungerar. Under återbeskydd misslyckas dock redundans eftersom datalagren inte kan identifieras och inte visas under återbeskyddning. LÃ¶s problemet genom att uppdatera vCenter-autentiseringsuppgifterna med [ett lämpligt konto/behörigheter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)och sedan försöka med jobbet igen. 
8. Om du har använt en mall för att skapa virtuella datorer kontrollerar du att varje virtuell dator har ett eget UUID för diskarna. Om den lokala VM UUID krockar med UUID för huvudmålservern eftersom båda skapades från samma mall, misslyckas återbeskydd. Distribuera från en annan mall.
9. Om du inte går tillbaka till en alternativ vCenter Server kontrollerar du att den nya vCenter-servern och huvudmålservern upptäcks. Vanligtvis om de inte är datalager inte är tillgängliga, eller inte visas i **Reprotect**.
10. Kontrollera följande scenarier där du inte kan växla tillbaka:
    - Om du använder antingen ESXi 5.5 gratis utgåva eller vSphere 6 Hypervisor gratis utgåva. Uppgradera till en annan version.
    - Om du har en fysisk server för Windows Server 2008 R2 SP1.
    - Virtuella datorer kan inte växla tillbaka till Hyper-V.
    - Virtuella datorer som [har migrerats](migrate-overview.md#what-do-we-mean-by-migration).
    - En virtuell dator som har flyttats till en annan resursgrupp.
    - En virtuell replik för Azure som har tagits bort.
    - En replik Azure VM som inte är skyddad (replikera till den lokala platsen).
10. [Granska de typer av återställning av fel som](concepts-types-of-failback.md) du kan använda – original återställning av plats och alternativ platsåterställning.


## <a name="enable-reprotection"></a>Aktivera återbeskydd

Aktivera replikering. Du kan reprotera specifika virtuella datorer eller en återställningsplan:

- Om du reprotect en återställningsplan, måste du ange värden för varje skyddad maskin.
- Om virtuella datorer tillhör en replikeringsgrupp för konsekvens för flera virtuella datorer kan de bara roteras igen med hjälp av en återställningsplan. Virtuella datorer i en replikeringsgrupp måste använda samma huvudmålserver

### <a name="before-you-start"></a>Innan du börjar

- När en virtuell dator startar i Azure efter redundans tar det lite tid för agenten att registrera sig tillbaka till konfigurationsservern (upp till 15 minuter). Under den här tiden kan du inte reprotera och ett felmeddelande indikerar att agenten inte är installerad. Om detta händer, vänta i några minuter och sedan reprotect.
- Om du vill återställa Azure-datorn till en befintlig lokal virtuell dator monterar du de lokala VM-datalager med läs-/skrivåtkomst på huvudmålserverns ESXi-värd.
- Om du vill växla tillbaka till en alternativ plats, till exempel om den lokala virtuella datorn inte finns, väljer du den kvarhållningsenhet och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen använder virtuella VMware-datorer i återställningsskyddet samma datalager som huvudmålservern. En ny virtuell dator skapas sedan i vCenter.

Aktivera återskydd enligt följande:

1. Välj **Replikerade arkivobjekt** > **Replicated items**. Högerklicka på den virtuella datorn som gick över och välj sedan **Skydda igen**. Du kan också välja maskinen på kommandoknapparna och välj sedan **Skydda igen**.
2. Kontrollera att **Azure till lokal** skyddsriktning har valts.
3. I **Huvudmålserver** och **processserver**väljer du den lokala målservern och processservern.  
4. För **Datastore**väljer du det datalager som du vill återställa diskarna lokalt till. Det här alternativet används när den lokala virtuella datorn tas bort och du måste skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste fortfarande ange ett värde.
5. Välj kvarhållningsenheten.
6. Failback-principen väljs automatiskt.
7. Välj **OK** för att börja skydda igen.

    ![Dialogrutan Reprotect](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Ett jobb börjar replikera Den virtuella Azure-datorn till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.
    - När återskyddet lyckas går den virtuella datorn in i ett skyddat tillstånd.
    - Den lokala virtuella datorn stängs av under återaktiveringen av skyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen.
    - Slå inte på den lokala virtuella datorn när återbeskyddningen är klar.
   

## <a name="next-steps"></a>Nästa steg

- Om du stöter på några problem läser du [felsökningsartikeln](vmware-azure-troubleshoot-failback-reprotect.md).
- När virtuella Azure-datorer har skyddats kan du [köra en återställning efter fel.](vmware-azure-failback.md) Återställning efter fel stänger av den virtuella azure-datorn och startar den lokala virtuella datorn. Räkna med några driftstopp för programmet, och välj en återställningstid i enlighet därmed.


