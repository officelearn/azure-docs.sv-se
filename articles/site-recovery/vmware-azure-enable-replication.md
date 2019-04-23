---
title: Aktivera replikering av virtuella VMware-datorer på haveriberedskap till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar virtuella VMware-datorer för replikering till Azure för haveriberedskap med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004745"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Azure för virtuella VMware-datorer

Den här artikeln beskriver hur du aktiverar replikering av lokala virtuella VMware-datorer till Azure.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du har:

- [Konfigurera din lokala källmiljö](vmware-azure-set-up-source.md).
- [Konfigurera målmiljön i Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Innan du börjar
När du replikerar virtuella VMware-datorer, Tänk på den här informationen:

* Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en ny virtuell dator till Azure.
* Virtuella VMware-datorer identifieras var 15: e minut. Det kan ta 15 minuter eller längre för virtuella datorer visas i Azure portal efter identifieringen. På samma sätt identifieringen kan ta 15 minuter eller längre när du lägger till en ny vCenter-servern eller vSphere-värd.
* Det kan ta 15 minuter eller längre för miljöändringar på den virtuella datorn (till exempel VMware-verktyg-installation) uppdateras i portalen.
* Du kan kontrollera den senaste identifierade tid för virtuella VMware-datorer: Se den **senaste kontakt på** på den **Konfigurationsservrar** för vCenter-servern/vSphere-värden.
* Om du vill lägga till virtuella datorer för replikering utan att vänta på den schemalagda identifieringen, markerar du konfigurationsservern (men inte på den) och välj **uppdatera**.
* När du aktiverar replikering, om den virtuella datorn är förberedd installerar processervern automatiskt Azure Site Recovery-mobilitetstjänsten på den.

## <a name="enable-replication"></a>Aktivera replikering

Observera följande innan du följer stegen i det här avsnittet:
* Azure Site Recovery replikerar nu direkt till hanterade diskar för alla nya replikeringar. Processervern skriver replikeringsloggar till ett cachelagringskonto i målregionen. De här loggarna används för att skapa återställningspunkter i hanterade replikeringsdiskar.
* Vid tidpunkten för växling vid fel används den återställningspunkt som du väljer för att skapa mål-hanterad disk.
* Virtuella datorer som konfigurerats tidigare för att replikera till mållagringskonton påverkas inte.
* Replikering till storage-konton för en ny virtuell dator är bara tillgängligt via en REST Representational State Transfer () API och Powershell. Använd Azure REST-API version 2016-08-10 eller 2018-01-10 för att replikera till storage-konton.

1. Gå till **steg 2: Replikera program** > **källa**. När du aktiverar replikering för första gången väljer **+ replikera** i valvet för att aktivera replikering för ytterligare virtuella datorer.
1. I den **källa** sidan > **källa**, Välj configuration server.
1. För **datortyp**väljer **virtuella datorer** eller **fysiska datorer**.
1. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen gäller inte om du replikerar fysiska datorer.
1. Välj den processerver som kommer att konfigurationsservern om du inte har skapat några ytterligare processervrar. Välj sedan **OK**.

    ![Aktivera replikering fönstret för datakälla](./media/vmware-azure-enable-replication/enable-replication2.png)

1. För **Target**, Välj gruppen prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure för de redundansväxlade virtuella datorerna.

1. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter redundansväxlingen. Nätverket måste finnas i samma region som Site Recovery service-valv.

   Välj **konfigurera nu för valda datorer** att tillämpa nätverksinställningen på alla virtuella datorer som du väljer för skydd. Välj **konfigurera senare** att välja Azure-nätverket per virtuell dator. Om du inte har ett nätverk kan behöva du skapa en. Om du vill skapa ett nätverk med hjälp av Azure Resource Manager, Välj **Skapa ny**. Välj ett undernät om det är tillämpligt, och välj sedan **OK**.
   
   ![Aktivera replikering målfönstret](./media/vmware-azure-enable-replication/enable-rep3.png)

1. För **virtuella datorer** > **Välj virtuella datorer**, Välj varje virtuell dator som du vill replikera. Du kan bara välja virtuella datorer som du kan aktivera replikering. Välj sedan **OK**. Om du inte kan se eller väljer en viss virtuell dator, se [källdatorn inte visas i Azure-portalen](https://aka.ms/doc-plugin-VM-not-showing) att lösa problemet.

    ![Aktivera replikering väljer virtuella datorer fönster](./media/vmware-azure-enable-replication/enable-replication5.png)

1. För **egenskaper** > **konfigurera egenskaper**, väljer du det konto som processervern använder för att automatiskt installera Site Recovery-mobilitetstjänsten på den virtuella datorn. Välj även typ av mål hanterad disk att replikera till utifrån dina data omsättning mönster.
10. Som standard replikeras alla diskar på en virtuell källdator. Om du vill undanta diskar från replikering, avmarkera de **inkludera** markerar du kryssrutan för alla diskar som du inte vill replikera. Välj sedan **OK**. Du kan ange ytterligare egenskaper senare. Läs mer om [diskar undantas](vmware-azure-exclude-disk.md).

    ![Aktivera replikering konfigurera egenskapsfönstret](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Vid **replikeringsinställningar** > **konfigurera replikeringsinställningar**, kontrollera att rätt replikeringsprincip har valts. Du kan ändra inställningarna för replikeringsprincipen på **inställningar** > **replikeringsprinciper** > ***principnamn***  >  **Redigera inställningar för**. Det gäller även dessa ändringar till en princip för replikering och nya virtuella datorer.
1. Aktivera **konsekvens** om du vill samla in virtuella datorer i en replikeringsgrupp. Ange ett namn för gruppen och välj sedan **OK**.

    > [!NOTE]
    >    * Virtuella datorer i en replikeringsgrupp replikeras tillsammans och har delade kraschkonsekventa och appkonsekventa återställningspunkter när de växlar över.
    >    * Samla ihop virtuella datorer och fysiska servrar så att de speglar dina arbetsbelastningar. Aktivering av konsekvens för flera datorer kan påverka arbetsbelastningens prestanda. Gör detta endast om de virtuella datorerna kör samma arbetsbelastning och konsekvens.

    ![Aktivera replikering fönster](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Välj **Aktivera replikering**. Du kan följa förloppet för den **Aktivera skydd** jobb på **inställningar** > **jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet körs, den virtuella datorn är redo för redundans.

## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Kontrollera egenskaperna för den virtuella källdatorn. Kom ihåg att Azure VM-namnet måste överensstämma med [krav för Azure virtuella datorer](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Gå till **inställningar** > **replikerade objekt**, och välj sedan den virtuella datorn. Den **Essentials** visar information om Virtuellt datorns inställningar och status.
1. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.
1. I **beräkning och nätverk** > **beräkna egenskaper**, du kan ändra egenskaper för flera virtuella datorer. 

    ![Egenskapsfönstret för beräkning och nätverk](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM-namn: Ändra namnet att uppfylla kraven för Azure, om det behövs.
    * Storleken eller VM-typ: Standardstorleken för virtuella datorer är valt baserat på ett par parametrar som innehåller antalet diskar, antal NIC, processor antal kärnor, minne och tillgängliga storlekar för VM-roll i Azure-målregion. Azure Site Recovery hämtar den första tillgängliga VM-storlek som uppfyller alla villkor. Du kan välja en annan VM-storlek baserat på dina behov när som helst före redundans. Observera att VM-diskstorleken bygger också på källdiskens storlek och kan bara ändras efter redundans. Läs mer om diskstorlekar och IOPS priserna vid [mål för skalbarhet och prestanda för Virtuella diskar på Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Resursgrupp: Du kan välja en [resursgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), från som en virtuell dator blir en del av en efter redundans. Du kan ändra den här inställningen när som helst före redundans. Om du migrerar den virtuella datorn till en annan resursgrupp efter en redundansväxling dela skyddsinställningarna för den virtuella datorn.
    * Tillgänglighetsuppsättning: Du kan välja en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) om den virtuella datorn måste vara en del av en efter redundans. När du väljer en tillgänglighetsuppsättning måste ha följande i åtanke:

        * Endast tillgänglighetsuppsättningar som hör till den angivna resursgruppen listas.  
        * Virtuella datorer som finns på olika virtuella nätverk kan inte ingå i samma tillgänglighetsuppsättning.
        * Endast virtuella datorer av samma storlek kan ingå i en tillgänglighetsuppsättning.
1. Du kan också lägga till information om målnätverket, undernätet och IP-adress som tilldelas till den virtuella Azure-datorn.
2. I **diskar**, du kan se det operativsystem och datadiskar på den virtuella datorn som ska replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

Du kan ange IP-måladressen. Om du inte anger någon adress använder DHCP för den redundansväxlade virtuella datorn. Om du anger en adress som inte är tillgänglig under redundansväxlingen fungerar inte för växling vid fel. Om adressen är tillgänglig i nätverket för redundanstestet, kan du använda samma mål-IP för att testa redundans.

Antalet nätverkskort beror på storleken som du anger den virtuella måldatorn, enligt följande:

- Om antalet nätverkskort på den virtuella källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för den Virtuella måldatorns storlek, har målet samma antal kort som källa.
- Om antalet nätverkskort för den virtuella källdatorn överskrider det antal som tillåts för den Virtuella måldatorns storlek, används högsta. Om en virtuell källdator har två nätverkskort och den Virtuella måldatorns storlek stöder fyra, har den virtuella måldatorn två nätverkskort. Om den Virtuella källdatorn har två nätverkskort men målstorleken endast stöder ett, har den Virtuella måldatorn endast ett kort.
- Om den virtuella datorn har flera nätverkskort kan ansluta de till samma nätverk. Dessutom det första kortet som visas i listan blir den *standard* nätverkskort i Azure-dator. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid-förmånen för att spara på kostnader för programvarulicenser för Windows Server-datorer som migreras till Azure. Förmånen gäller även för Azure-haveriberedskap. Om du är behörig, kan du tilldela fördelen att den virtuella datorn som skapar Site Recovery om det finns en redundansväxling. Det gör du genom att följa dessa steg:
1. Gå till den **datorns och nätverkets egenskaper** för den replikerade virtuella datorn.
2. Svara när du tillfrågas om du har en Windows Server-licens som gör att du är berättigad till Azure Hybrid-förmånen.
3. Bekräfta att du har en berättigade Windows Server-licens med Software Assurance som du kan använda för att använda förmånen med den virtuella datorn som ska skapas vid en redundansväxling.
4. Spara inställningarna för den replikerade virtuella datorn.

Läs mer om [Azure Hybrid-förmånen](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Lösa vanliga problem

* Varje disk ska vara mindre än 4 TB.
* OS-disken ska vara en standarddisk, inte en dynamisk disk.
* Operativsystemets familj måste vara Windows för generation 2/UEFI-aktiverade virtuella datorer och startdisken får inte vara mindre än 300 GB.

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn når ett skyddat läge kan du prova en [redundans](site-recovery-failover.md) att kontrollera om ditt program ska visas i Azure.

* Lär dig hur du [Rensa inställningarna för registrering och skydd](site-recovery-manage-registration-and-protection.md) att inaktivera replikering.
* Lär dig hur du [automatisera replikering för virtuella datorer med hjälp av Powershell](vmware-azure-disaster-recovery-powershell.md).
