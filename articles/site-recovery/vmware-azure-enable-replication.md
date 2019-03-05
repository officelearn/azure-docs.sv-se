---
title: Aktivera replikering av virtuella VMware-datorer för VMware-haveriberedskap till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar replikering av virtuella VMware-datorer för haveriberedskap till Azure med Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/3/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 043a49e679f2b8ec48213e28d229121d3f0e070d
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338645"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Azure för virtuella VMware-datorer


Den här artikeln beskriver hur du aktiverar replikering av lokala virtuella VMware-datorer till Azure.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har:

1.  [Konfigurera källmiljön för lokala](vmware-azure-set-up-source.md).
2.  [Konfigurera målmiljön i Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Innan du börjar
När du replikerar virtuella VMware-datorer:

* Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en ny virtuell dator till Azure.
* Virtuella VMware-datorer identifieras var 15: e minut. Det kan ta 15 minuter eller längre att visas i Azure portal efter identifieringen. På samma sätt kan identifieringen ta 15 minuter eller mer när du lägger till en ny vCenter-servern eller vSphere-värd.
* Ändringar i miljön på den virtuella datorn (till exempel VMware-verktyg-installation) kan ta 15 minuter eller mer att uppdateras i portalen.
* Du kan kontrollera senast identifierades för virtuella VMware-datorer i den **senaste kontakt på** fältet för vCenter server/vSphere-värden på den **Konfigurationsservrar** sidan.
* Om du vill lägga till datorer för replikering utan att vänta på den schemalagda identifieringen, markerar du konfigurationsservern (Klicka inte på den), och klicka på den **uppdatera** knappen.
* När du aktiverar replikering, om datorn förbereds installerar processervern automatiskt Mobilitetstjänsten på den.


## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **steg 2: Replikera program** > **källa**. När du har aktiverat replikering för första gången klickar du på **+Replikera** i valvet för att aktivera replikering för ytterligare datorer.
2. I den **källa** sidan > **källa**, Välj configuration server.
3. I **datortyp**väljer **virtuella datorer** eller **fysiska datorer**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen gäller inte om du replikerar fysiska datorer.
5. Välj den processerver som är namnet på configuration server om du inte skapat några ytterligare processervrar. Klicka sedan på **OK**.

    ![Aktivera replikeringskälla](./media/vmware-azure-enable-replication/enable-replication2.png)

6. I **Target**, Välj prenumerationen och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure för de redundansväxlade virtuella datorerna.

7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling. Nätverket måste finnas i samma region som Recovery Services-valvet. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du inte har ett nätverk kan behöva du skapa en. Om du vill skapa ett nätverk med hjälp av Resource Manager klickar du på **Skapa ny**. Välj ett undernät om det är tillämpligt, och klicka sedan på **OK**.

    ![Aktivera replikering Målinställningar](./media/vmware-azure-enable-replication/enable-rep3.png)
8. I **Virtual Machines** > **Välj virtuella datorer** väljer du de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**. Om du inte kan visa /välja en viss virtuell dator klickar du [här](https://aka.ms/doc-plugin-VM-not-showing) för att lösa problemet.

    ![Aktivera replikering väljer virtuella datorer](./media/vmware-azure-enable-replication/enable-replication5.png)
9. I **egenskaper** > **konfigurera egenskaper**, väljer du det konto som används av processervern för att automatiskt installera Mobilitetstjänsten på datorn.  
10. Som standard replikeras alla diskar. Om du vill undanta diskar från replikering klickar du på **alla diskar** och rensa alla diskar som du inte vill replikera.  Klicka sedan på **OK**. Du kan ange ytterligare egenskaper senare. [Läs mer](vmware-azure-exclude-disk.md) om att undanta diskar.

    ![Aktivera replikering konfigurera egenskaper](./media/vmware-azure-enable-replication/enable-replication6.png)

11. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts. Du kan ändra inställningarna för replikeringsprincipen i **inställningar** > **replikeringsprinciper** > (principnamn) > **redigera inställningar för**. Ändrar för en princip gäller även för replikering och nya datorer.
12. Aktivera **konsekvens** om du vill samla in datorer i en replikeringsgrupp. Ange ett namn för gruppen och klicka sedan på **OK**. 

    > [!NOTE]

    >    * Datorer i en replikeringsgrupp replikeras tillsammans och har delade kraschkonsekventa och appkonsekventa återställningspunkter när de växlar över.
    >    * Samla ihop virtuella datorer och fysiska servrar så att de speglar dina arbetsbelastningar. Aktivering av konsekvens för flera datorer kan påverka prestandan. Använd bara om datorerna kör samma arbetsbelastning och konsekvens.

    ![Aktivera replikering](./media/vmware-azure-enable-replication/enable-replication7.png)
13. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.



## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Nu ska kontrollera du egenskaperna för källdatorn. Kom ihåg att Azure VM-namnet måste överensstämma med [krav för Azure virtuella datorer](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Klicka på **inställningar** > **replikerade objekt** >, och välj sedan datorn. Den **Essentials** visar information om datorinställningar och status.
2. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.
3. I **beräkning och nätverk** > **beräkna egenskaper**, du kan ändra flera VM-propoerties:
    * Azure VM name - ändra namnet till uppfyller kraven för Azure vid behov
    * Storleken eller typ av virtuell dator – standard VM-storlek väljs baserat på käll-VM-storlek. Du kan välja en annan VM-storlek baserat på behov när som helst före redundans. Observera att VM-diskstorleken bygger också på källdiskens storlek och att den endast kan ändras efter redundans. Lär dig mer om diskstorlekar och IOPS i vår [skalbarhetsmål för diskar](../virtual-machines/windows/disk-scalability-targets.md) artikeln.

    ![Beräknings- och Nätverksegenskaper](./media/vmware-azure-enable-replication/vmproperties.png)

    *  Resursgrupp – du kan välja en [resursgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) från vilket en dator blir en del av en efter redundans. Du kan ändra den här inställningen när som helst före redundans. Efter redundans, om du migrerar datorn till en annan resursgrupp skyddsinställningarna för den datorn break.
    * Tillgänglighetsuppsättning – du kan välja en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) om din dator måste vara en del av en efter redundans. När du markerar en tillgänglighetsuppsättning, Tänk på att:

        * Endast tillgänglighetsuppsättningar som hör till den angivna resursgruppen listas.  
        * Datorer med olika virtuella nätverk kan inte ingå i samma tillgänglighetsuppsättning.
        * Endast virtuella datorer av samma storlek kan ingå i en tillgänglighetsuppsättning.
4. Du kan också visa och lägga till information om målnätverket, undernätet och IP-adress som tilldelas den virtuella Azure-datorn.
5. I **diskar**, du kan se det operativsystem och datadiskar på den virtuella datorn replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

- Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig under redundansväxlingen fungerar inte för växling vid fel. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.
- Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:
    - Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek, sedan har målet samma antal kort som källa.
    - Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
    Om en källdator har två nätverkskort och måldatorn stöder fyra så har kommer måldatorn två nätverkskort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett har bara ett kort med måldatorn.
    - Om den virtuella datorn har flera nätverkskort kan ansluta de till samma nätverk. Dessutom den första som visas i listan blir den *standard* nätverkskort i Azure-dator.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid-förmånen att spara på licenskostnaden för Windows Server-datorer som har migrerats till Azure eller att använda Azure för haveriberedskap. Om du är behörig att använda Azure Hybrid-förmånen kan du ange att den virtuella datorn som är tilldelade den här förmånen är den som skapar Azure Site Recovery om det finns en redundansväxling. Gör så här:
- Gå till avsnittet beräknings- och egenskaper för den replikerade virtuella datorn.
- Besvara frågan som frågar om du har en Windows Server-licens som gör att du är berättigad till Azure Hybrid-förmånen.
- Markera kryssrutan för att bekräfta att du har en berättigade Windows Server-licens med Software Assurance som du kan använda för att använda Azure Hybrid-förmånen på den dator som ska skapas vid redundansväxling.
- Spara inställningarna för den replikerade datorn.

Läs mer om [Azure Hybrid-förmånen](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Vanliga problem

* Varje disk ska vara mindre än 4 TB i storlek.
* OS-disken ska vara en standarddisk och inte en dynamisk disk.
* Operativsystemets familj måste vara Windows för generation 2/UEFI-aktiverade virtuella datorer och startdisken får vara högst 300 GB.

## <a name="next-steps"></a>Nästa steg

När skyddet har slutförts och datorn har nått ett skyddat läge, kan du prova en [redundans](site-recovery-failover.md) att kontrollera om ditt program visas i Azure eller inte.

* Lär dig hur du [Rensa inställningarna för registrering och skydd](site-recovery-manage-registration-and-protection.md) att inaktivera replikering.
* Lär dig hur du [automatisera replikering för virtuella datorer med hjälp av Powershell](vmware-azure-disaster-recovery-powershell.md)
