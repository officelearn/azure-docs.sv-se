---
title: Konfigurera redundans och redundans för fysiska servrar med platsåterställning
description: Lär dig hur du växlar över fysiska servrar till Azure och växlar tillbaka till den lokala platsen för haveriberedskap med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497853"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Växla över och återställa fysiska servrar som replikeras till Azure

Den här självstudien beskriver hur du växlar över lokala fysiska servrar som replikerar till Azure med [Azure Site Recovery](site-recovery-overview.md). När du har misslyckats över kan du växla tillbaka från Azure till din lokala webbplats när den är tillgänglig.

## <a name="before-you-start"></a>Innan du börjar

- [Lär dig mer](failover-failback-overview.md) om redundansprocessen vid haveriberedskap.
- Om du vill växla över flera datorer [kan du läsa om](recovery-plan-overview.md) hur du samlar datorer i en återställningsplan.
- Innan du gör en fullständig redundans, kör en [katastrofåterställningsövning](site-recovery-test-failover-to-azure.md) för att säkerställa att allt fungerar som förväntat.
- Följ [dessa instruktioner](site-recovery-failover.md#prepare-to-connect-after-failover) för att förbereda att ansluta till virtuella Azure-datorer efter redundans.



## <a name="run-a-failover"></a>Köra en redundansväxling

### <a name="verify-server-properties"></a>Verifiera serveregenskaper

Verifiera serveregenskaperna och se till att den uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines) för virtuella Azure-datorer.

1. Klicka på **Replikerade objekt i** **Skyddade objekt**och välj datorn.
2. I fönstret **Replikerade objekt** finns en sammanfattning av maskininformation, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **Beräkning och Nätverk**kan du ändra Azure-namn, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md)och hanterade diskinställningar
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I Diskar kan du se information om **datorns**operativsystem och datadiskar.

### <a name="fail-over-to-azure"></a>Redundansväxla till Azure

1. Klicka på datorn > **Redundans**i **Inställningar** > **replikerade objekt** .
2. I **Redundans** väljer du en **återställningspunkt** att växla över till. Du kan välja något av följande alternativ:
   - **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senast bearbetad:** Det här alternativet växlar över datorn till den senaste återställningspunkten som bearbetas av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste app-konsekvent:** Det här alternativet misslyckas över maskinen till den senaste app-konsekvent återställningspunkt som bearbetas av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng av datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka stänga av källmaskinen innan du utlöser redundansen. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
4. Om du har förberett att ansluta till den virtuella Azure-datorn, ansluter du för att kontrollera den efter redundansen.
5. När du är klar väljer du **Genomför** för redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Avbryt inte en pågående redundans. Innan redundansen börjar stoppas datorreplikeringen. Om du avbryter redundansen stoppas den, men datorn replikeras inte igen.
> För fysiska servrar kan ytterligare redundansbearbetning ta cirka åtta till tio minuter att slutföra.

## <a name="automate-actions-during-failover"></a>Automatisera åtgärder under redundans

Du kanske vill automatisera åtgärder under redundans. För att göra detta kan du använda skript eller Azure automation runbooks i återställningsplaner.

- [Läs mer](site-recovery-create-recovery-plans.md) om hur du skapar och anpassar återställningsplaner, inklusive att lägga till skript.
- [Lär dig](site-recovery-runbook-automation.md) abut att lägga till Azure Automation-runbooks i återställningsplaner.

## <a name="configure-settings-after-failover"></a>Konfigurera inställningar efter redundans

Efter redundans måste du [konfigurera Azure-inställningar](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) för att ansluta till replikerade Virtuella Azure-datorer. Dessutom ställa in [intern och offentlig](site-recovery-failover.md#set-up-ip-addressing) IP-adressering.

## <a name="prepare-for-reprotection-and-failback"></a>Förbered för återskydd och återställning

När du har misslyckats med Azure, du reprotect Azure virtuella datorer genom att replikera dem till den lokala platsen. När de replikeras kan du växla tillbaka dem till lokalt genom att köra en redundans från Azure till din lokala webbplats.

1. Fysiska servrar som replikeras till Azure med site recovery kan bara växla tillbaka som virtuella datorer för VMware. Du behöver en VMware-infrastruktur för att kunna växla tillbaka. Följ stegen i den [här artikeln](vmware-azure-prepare-failback.md) för att förbereda för omskydd och återställning, inklusive att konfigurera en processserver i Azure och en lokal huvudmålserver, och konfigurera en plats-till-plats-VPN eller ExpressRoute privat peering, för återställning av återställning av återställning av återställning.
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
    - Virtuella datorer som [har migrerats](migrate-overview.md#what-do-we-mean-by-migration).
    - En virtuell dator som har flyttats till en annan resursgrupp.
    - En virtuell replik för Azure som har tagits bort.
    - En replik Azure VM som inte är skyddad (replikera till den lokala platsen).
10. [Granska de typer av återställning av fel som](concepts-types-of-failback.md) du kan använda – original återställning av plats och alternativ platsåterställning.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Rotera azure-virtuella datorer till en alternativ plats

Den här proceduren förutsätter att den lokala virtuella datorn inte är tillgänglig.

1. Högerklicka på den dator som misslyckades över > **Skydda**om i valvet > **inställningar.** > **Replicated items**
2. I **Återaktivera skydd** kontrollerar du att **Azure till lokal plats** är valt.
3. Ange den lokala huvudmålservern och processervern.
4. I **Datalager** välj du det huvudmåldatalager som du vill återställa diskarna till lokalt.
       - Använd det här alternativet om den lokala virtuella datorn har tagits bort eller inte finns, och du måste skapa nya diskar.
       - Den här inställningen ignoreras om diskarna redan finns, men du måste ange ett värde.
5. Välj kvarhållningsenhetens huvudmål. Failback-principen väljs automatiskt.
6. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar replikera Den virtuella Azure-datorn till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.

> [!NOTE]
> Om du vill återställa den virtuella Azure-datorn till en befintlig lokal VM, monterar du den lokala virtuella datorns datalager med läs-/skrivåtkomst på huvudmålserverns ESXi-värd.


## <a name="fail-back-from-azure"></a>Redundansväxla från Azure

Kör en redundans enligt följande:

1. På sidan **Replikerade objekt** högerklickar du på datorn > **Oplanerad redundans**.
2. I **Bekräfta redundans** kontrollerar du att redundansriktningen är från Azure.
3. Välj den återställningspunkt som du vill använda för redundansen.
    - Vi rekommenderar att du använder den **senaste återställningspunkten.** Den appkonsekventa punkten ligger bakom den senaste tidpunkten och orsakar viss dataförlust.
    - **Senaste** är en krasch-konsekvent återhämtning punkt.
    - När redundansen körs stänger Site Recovery av de virtuella Azure-datorerna och startar den lokala virtuella datorn. Det kommer att bli vissa avbrott, så du bör välja en lämplig tidpunkt.
4. Högerklicka på datorn och klicka på **Genomför**. Detta utlöser ett jobb som tar bort de virtuella Azure-datorerna.
5. Kontrollera att de virtuella Azure-datorerna har stängts av som förväntat.


## <a name="reprotect-on-premises-machines-to-azure"></a>Återaktivera skyddet av lokala datorer till Azure

Dina data bör nu finnas lokalt igen, men de replikeras inte till Azure. Du kan påbörja replikeringen till Azure igen på följande sätt:

1. I valvet > **Inställningar** >**Replikerade objekt** väljer du de återställda virtuella datorerna och klickar på **Återaktivera skydd**.
2. Välj den processerver som används för att skicka replikerade data till Azure och klicka på **OK**.


## <a name="next-steps"></a>Nästa steg

När reprotect-jobbet är klart replikerar den lokala virtuella datorn till Azure. Efter behov kan du [köra en annan redundans](site-recovery-failover.md) till Azure.
