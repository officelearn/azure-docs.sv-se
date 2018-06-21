---
title: Aktivera replikering för VMware VM till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in replikering av virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: asgang
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: asgang
ms.openlocfilehash: 5a4f184d0edf42732f1671d123f885749ae188d9
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287396"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Azure för virtuella VMware-datorer


Den här artikeln beskriver hur du aktiverar replikering av lokala virtuella VMware-datorer till Azure.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har:

1.  [Konfigurera lokala källmiljön](vmware-azure-set-up-source.md).
2.  [Konfigurera målmiljön i Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Innan du börjar
När du replikerar virtuella VMware-datorer:

* Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en ny virtuell dator till Azure.
* Virtuella VMware-datorer identifieras var 15: e minut. Det kan ta 15 minuter eller längre att visas i Azure portal efter identifiering. På samma sätt kan identifieringen ta 15 minuter eller mer när du lägger till en ny vCenter-servern eller vSphere-värd.
* Ändringar i miljön på den virtuella datorn (till exempel VMware verktyg installation) kan ta 15 minuter eller mer att uppdateras i portalen.
* Du kan kontrollera senast identifierade för VMwares virtuella datorer i den **senaste kontakt på** fältet för vCenter server/vSphere-värd, på den **Konfigurationsservrar** sidan.
* Markera om du vill lägga till datorer för replikering utan att vänta på den schemalagda identifieringen konfigurationsservern (inte på den), och klicka på den **uppdatera** knappen.
* När du aktiverar replikering, om datorn är förberedd installerar processervern automatiskt Mobilitetstjänsten på den.


## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Steg 2: Replikera program** > **Källa**. När du har aktiverat replikering för första gången klickar du på **+Replikera** i valvet för att aktivera replikering för ytterligare datorer.
2. I den **källa** sidan > **källa**, Välj konfigurationsservern.
3. I **datorn typen**väljer **virtuella datorer** eller **fysiska datorer**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen är inte relevant om du replikerar fysiska datorer.
5. Välj processervern, som kommer att namnet på konfigurationsservern om du inte har skapat några ytterligare servrar. Klicka sedan på **OK**.

    ![Aktivera källa för replikering](./media/vmware-azure-enable-replication/enable-replication2.png)

6. I **mål**, Välj prenumerationen och resursgrupp som du vill skapa misslyckades över virtuella datorer. Välj distributionsmodell som du vill använda i Azure för misslyckades över virtuella datorer.

7. Välj Azure Storage-konto som du vill använda för att replikera data. 

    > [!NOTE]

    >   * Du kan välja en premium eller standardlagringskonto. Om du väljer ett premium-konto, måste du ange ett ytterligare ett standardlagringskonto för pågående replikeringsloggar. Konton måste vara i samma region som Recovery Services-valvet.
    >   * Om du vill använda ett annat lagringskonto kan du [skapar du en](../storage/common/storage-create-storage-account.md). Klicka för att skapa ett lagringskonto med hjälp av hanteraren för filserverresurser **Skapa nytt**. 

8. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling. Nätverket måste finnas i samma region som Recovery Services-valvet. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du inte har ett nätverk kan du behöva [skapar du en](#set-up-an-azure-network). Klicka för att skapa ett nätverk med hjälp av hanteraren för filserverresurser **Skapa nytt**. Välj ett undernät, om tillämpligt, och klicka sedan på **OK**.

    ![Aktivera replikering Målinställningar](./media/vmware-azure-enable-replication/enable-rep3.png)
9. I **Virtual Machines** > **Välj virtuella datorer** väljer du de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering väljer virtuella datorer](./media/vmware-azure-enable-replication/enable-replication5.png)
10. I **egenskaper** > **konfigurera egenskaper för**, väljer du det konto som används av processervern för att automatiskt installera Mobilitetstjänsten på datorn.  
11. Alla diskar replikeras som standard. Om du vill exkludera diskar från replikeringen, klickar du på **alla diskar** och avmarkera alla diskar som du inte vill replikera.  Klicka sedan på **OK**. Du kan ange ytterligare egenskaper senare. [Lär dig mer](vmware-azure-exclude-disk.md) om Exkludera diskar.

    ![Aktivera replikering konfigurerar du egenskaper](./media/vmware-azure-enable-replication/enable-replication6.png)

12. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts. Du kan ändra inställningar för replikering i **inställningar** > **replikeringsprinciper** > (principnamn) > **redigera inställningar för**. Du ändrar en princip gäller även för replikering och nya datorer.
13. Aktivera **konsekvens för flera** om du vill samla datorer i en replikeringsgrupp. Ange ett namn för gruppen och klicka sedan på **OK**. 

    > [!NOTE]

    >    * Datorer i en replikeringsgrupp replikeras tillsammans och har delat kraschkonsekvent och programkonsekventa återställningspunkter när de växlar över.
    >    * Samla in virtuella datorer och fysiska servrar så att de speglar dina arbetsbelastningar. Aktivera konsekvens för flera kan påverka arbetsbelastningens prestanda. Använd bara om datorer kör samma arbetsbelastning och du behöver enhetlighet.

    ![Aktivera replikering](./media/vmware-azure-enable-replication/enable-replication7.png)
14. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

> [!NOTE]
> Om datorn är förberedd för push-installation, installeras komponenten Mobilitetstjänsten när skydd är aktiverat. När komponenten är installerad på datorn, startar ett jobb för att skydda och misslyckas. Efter fel måste du manuellt starta om varje dator. Efter omstarten skyddsjobbet börjar igen och inledande replikering.
>
>

## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Därefter måste kontrollera du egenskaperna för källdatorn. Kom ihåg att det Virtuella Azure-namnet måste överensstämma med [Azure virtuella datorns krav](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Klicka på **inställningar** > **replikerade objekt** >, och välj sedan datorn. Den **Essentials** visar information om inställningarna för datorn och status.
2. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.
3. I **Beräkning och nätverk** > **Beräkna egenskaper** kan du ange namnet och storleken på den virtuella Azure-datorn. Ändra namnet som ska uppfylla kraven för Azure vid behov.

    ![Beräknings- och egenskaper för nätverk](./media/vmware-azure-enable-replication/vmproperties.png)

4.  Du kan välja en [resursgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) från som en dator blir en del av en post växling vid fel. Du kan ändra den här inställningen före redundans. Efter växling vid fel, om du migrerar datorn till en annan resursgrupp skyddsinställningarna för den datorn break.
5. Du kan välja en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) om datorn måste vara en del av en post växling vid fel. När du markerar en tillgänglighetsuppsättning, Tänk på att:

    * Endast tillgänglighetsuppsättningar som hör till den angivna resursgruppen visas.  
    * Datorer med olika virtuella nätverk kan inte ingå i samma tillgänglighetsuppsättning.
    * Endast virtuella datorer av samma storlek kan ingå i en tillgänglighetsuppsättning.
5. Du kan också visa och lägga till information om målnätverket, undernätet och IP-adressen till den virtuella Azure-datorn.
6. I **diskar**, du kan se operativsystemet och datadiskar på den virtuella datorn replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

- Du kan ange IP-måladressen. Om du inte anger någon adress använder misslyckades över datorn DHCP. Om du anger en adress som inte är tillgänglig under växling vid fel, fungerar inte för växling vid fel. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket.
- Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:
    - Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek, har målservern samma antal kort som källan.
    - Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
    Om en källdator har två nätverkskort och måldatorn stöder fyra har måldatorn två nätverkskort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett har bara en kort med måldatorn.
    - Om den virtuella datorn har flera nätverkskort, ansluta alla till samma nätverk. Dessutom den första som visas i listan blir den *standard* nätverkskort i den virtuella Azure-datorn.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid fördelen att spara på licensieringskostnader för Windows Server-datorer som har migrerats till Azure eller använda Azure för katastrofåterställning. Om du är berättigad att använda Azure-Hybrid förmån kan ange du att den virtuella datorn som tilldelats den här funktionen är en Azure Site Recovery skapas om det finns en växling vid fel. Gör så här:
- Gå till avsnittet beräknings- och egenskaper för den replikerade virtuella datorn.
- Besvara frågan som frågar om du har en Windows Server-licens som gör att du är berättigad till Azure Hybrid-förmånen.
- Markera kryssrutan för att bekräfta att du har en berättigad Windows Server-licens med Software Assurance, som du kan använda för att tillämpa förmån för Azure-Hybrid på den dator som kommer att skapas på växling vid fel.
- Spara inställningarna för den replikerade datorn.

Lär dig mer om [Azure Hybrid förmån](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Vanliga problem

* Varje disk måste vara mindre än 1 TB i storlek.
* OS-disk ska vara en standarddisk och inte en dynamisk disk.
* Operativsystemsfamiljen bör vara Windows för generation 2/UEFI-aktiverade virtuella datorer och startdisken ska vara mindre än 300 GB.

## <a name="next-steps"></a>Nästa steg

När skyddet har slutförts och datorn har uppnått ett skyddat läge kan du prova en [redundans](site-recovery-failover.md) att kontrollera om ditt program kommer i Azure eller inte.

Om du vill inaktivera skyddet, lär du dig hur du [Rensa inställningar för registrering och skydd](site-recovery-manage-registration-and-protection.md).
