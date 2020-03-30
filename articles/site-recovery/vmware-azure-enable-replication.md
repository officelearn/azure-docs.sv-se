---
title: Aktivera virtuella virtuella virtuella VMware-datorer för haveriberedskap med hjälp av Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs hur du aktiverar VMware VM-replikering för haveribeã¤ndegning av Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257321"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Virtuella datorer för Azure för virtuella datorer med VMware

I den här artikeln beskrivs hur du aktiverar replikering av lokala virtuella datorer med VMware till Azure.

## <a name="resolve-common-issues"></a>Lösa vanliga problem

* Varje disk bör vara mindre än 4 TB.
* OS-disken ska vara en enkel disk, inte en dynamisk disk.
* För generation 2/UEFI-aktiverade virtuella datorer bör operativsystemfamiljen vara Windows och startdisken ska vara mindre än 300 GB.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har:

- [Konfigurera den lokala källmiljön](vmware-azure-set-up-source.md).
- [Konfigurera din målmiljö i Azure](vmware-azure-set-up-target.md).
- [Verifiera krav och förutsättningar](vmware-physical-azure-support-matrix.md) innan du börjar. Viktiga saker att notera är:
    - [Operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) för replikerade datorer.
    - Stöd för [lagring/disk.](vmware-physical-azure-support-matrix.md#storage)
    - [Azure-krav](vmware-physical-azure-support-matrix.md#azure-vm-requirements) som lokala datorer ska uppfylla.


## <a name="before-you-start"></a>Innan du börjar
När du replikerar virtuella VMware-datorer bör du tänka på den här informationen:

* Ditt Azure-användarkonto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) för att aktivera replikering av en ny virtuell dator till Azure.
* Virtuella datorer identifieras var 15:e minut. Det kan ta 15 minuter eller längre för virtuella datorer att visas i Azure-portalen efter identifiering. På samma sätt kan identifieringen ta 15 minuter eller längre när du lägger till en ny vCenter-server eller vSphere-värd.
* Det kan ta 15 minuter eller längre för miljöändringar på den virtuella datorn (till exempel installation av VMware-verktyg) att uppdateras i portalen.
* Du kan kontrollera den senast identifierade tiden för virtuella datorer med VMware: Se fältet **Sista kontakt vid** på sidan **Konfigurationsservrar** för vCenter-server/vSphere-värden.
* Om du vill lägga till virtuella datorer för replikering utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (men klickar inte på den) och väljer **Uppdatera**.
* När du aktiverar replikering, om den virtuella datorn är förberedd, installerar processservern automatiskt azure site recovery mobility-tjänsten på den.

## <a name="enable-replication"></a>Aktivera replikering

Innan du följer stegen i det här avsnittet bör du tänka på följande:
* Azure Site Recovery replikerar nu direkt till hanterade diskar för alla nya replikeringar. Processservern skriver replikeringsloggar till ett cachelagringskonto i målregionen. Dessa loggar används för att skapa återställningspunkter i replikhanterade diskar som har namngivningskonvention för asrseeddisk.
* Powershell-stöd för replikera till hanterade diskar är tillgängligt från [Az.RecoveryServices modul version 2.0.0 och framåt](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Vid tidpunkten för redundans används återställningspunkten som du väljer för att skapa den målhanterade disken.
* Virtuella datorer som tidigare har konfigurerats för att replikeras till mållagringskonton påverkas inte.
* Replikering till lagringskonton för en ny virtuell dator är endast tillgänglig via ett REST-API (Representational State Transfer) och Powershell. Använd Azure REST API version 2016-08-10 eller 2018-01-10 för att replikera till lagringskonton.

Följ nedanstående steg för att aktivera replikering:
1. Gå till **steg 2: Replikera** > **programkälla**. När du har aktiverat replikering för första gången väljer du **+Replikera** i valvet för att aktivera replikering för ytterligare virtuella datorer.
2. Välj **konfigurationsservern** på sidan Källa **> Källa.**
3. För **maskintyp**väljer du **Virtuella datorer** eller fysiska **datorer**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen är inte relevant om du replikerar fysiska datorer.
5. Välj processservern. Om inga ytterligare processservrar skapas är inbyggda processserver för konfigurationsserver tillgänglig i listrutan. Hälsostatus för varje processserver anges enligt rekommenderade gränser och andra parametrar. Välj en felfri processserver. Det går inte att välja en [kritisk](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processserver. Du kan antingen [felsöka och lösa](vmware-physical-azure-troubleshoot-process-server.md) felen **eller** konfigurera en [utskalningsprocessserver](vmware-azure-set-up-process-server-scale.md).
    ![Aktivera fönstret replikeringskälla](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Från [9,24 versioner](service-updates-how-to.md#links-to-currently-supported-update-rollups)introduceras ytterligare aviseringar för att förbättra hälsovarningarna för processservern. Uppgradera site recovery-komponenter till 9,24 versioner eller högre för alla aviseringar som ska genereras.

6. För **Target**väljer du den prenumerations- och resursgrupp där du vill skapa de virtuella datorerna som inte har överskridas. Välj den distributionsmodell som du vill använda i Azure för de misslyckade virtuella datorerna.
2. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter redundans. Nätverket måste finnas i samma region som tjänstvalvet för platsåterställning.

   Välj **Konfigurera nu för valda datorer om** du vill använda nätverksinställningen på alla virtuella datorer som du väljer för skydd. Välj **Konfigurera senare** om du vill välja Azure-nätverket per virtuell dator. Om du inte har ett nätverk måste du skapa ett. Om du vill skapa ett nätverk med Hjälp av Azure Resource Manager väljer du **Skapa nya**. Välj ett undernät om tillämpligt och välj sedan **OK**.
   
   ![Aktivera fönstret replikeringsmål](./media/vmware-azure-enable-replication/enable-rep3.png)

1. För **virtuella datorer** > **Välj virtuella datorer**väljer du varje virtuell dator som du vill replikera. Du kan bara välja virtuella datorer för vilka replikering kan aktiveras. Välj sedan **OK**. Om du inte kan se eller välja en viss virtuell dator läser du [Källdatorn finns inte med i Azure-portalen](https://aka.ms/doc-plugin-VM-not-showing) för att lösa problemet.

    ![Aktivera replikering Välj fönstret virtuella datorer](./media/vmware-azure-enable-replication/enable-replication5.png)

1. För **egenskaper** > **Konfigurera egenskaper**väljer du det konto som processservern använder för att automatiskt installera tjänsten Site Recovery Mobility på den virtuella datorn. Välj också vilken typ av målhanterad disk som ska replikeras till baserat på dina dataomsättningsmönster.
10. Som standard replikeras alla diskar på en virtuell källdator. Om du vill utesluta diskar från replikering **avmarkerar** du kryssrutan Inkludera för alla diskar som du inte vill replikera. Välj sedan **OK**. Du kan ange ytterligare egenskaper senare. Läs mer om [att utesluta diskar](vmware-azure-exclude-disk.md).

    ![Fönstret Konfigurera egenskaper för replikeringskonfigurera](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Kontrollera att rätt replikeringsprincip är markerad vid **replikeringsinställningar** > Konfigurera**replikeringsinställningar.** Du kan ändra replikeringsprincipinställningarna vid **principnamn för principer för** > **inställningars** > ***princip.*** > **Edit Settings** Ändringar som du tillämpar på en princip gäller även för replikering och nya virtuella datorer.
1. Aktivera **konsekvens för flera virtuella datorer** om du vill samla in virtuella datorer i en replikeringsgrupp. Ange ett namn för gruppen och välj sedan **OK**.

    > [!NOTE]
    >    * Virtuella datorer i en replikeringsgrupp replikeras tillsammans och har delade kraschkonsekventa och appkonsekventa återställningspunkter när de växlar över.
    >    * Samla virtuella datorer och fysiska servrar tillsammans så att de speglar dina arbetsbelastningar. Om du aktiverar konsekvens med flera virtuella datorer kan arbetsbelastningsprestanda påverkas. Gör detta bara om de virtuella datorerna kör samma arbetsbelastning och du behöver konsekvens.

    ![Aktivera replikeringsfönster](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Välj **Aktivera replikering**. Du kan spåra förloppet**Jobs** > för jobbet **Aktivera skydd** vid**Jobbplatsåterställningsjobb**för **inställningar** > . När jobbet **Slutför skydd** har körts är den virtuella datorn klar för redundans.

## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Kontrollera sedan egenskaperna för den virtuella datorn för källan. Kom ihåg att Azure VM-namnet måste uppfylla [kraven för virtuella Azure-datorer](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Gå till **Inställningar** > **replikerade objekt**och välj sedan den virtuella datorn. Sidan **Essentials** visar information om den virtuella datorns inställningar och status.
1. I **Egenskaper**kan du visa replikerings- och redundansinformation för den virtuella datorn.
1. I egenskaper för beräkning och**nätverksberäkning** **Compute and Network** > kan du ändra flera vm-egenskaper. 

    ![Fönstret Beräknings- och nätverksegenskaper](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM-namn: Ändra namnet för att uppfylla Azure-krav om det behövs.
    * Mål-VM-storlek eller VM-typ: Standardstorleken för virtuella datorer väljs baserat på några parametrar som inkluderar diskantal, antal nätverkskort, CPU-kärnantal, Minne och tillgängliga VM-rollstorlekar i mål Azure-regionen. Azure Site Recovery väljer den första tillgängliga VM-storleken som uppfyller alla villkor. Du kan välja en annan vm-storlek baserat på dina behov när som helst före redundans. Observera att VM-diskstorleken också baseras på källdiskstorlek och att den bara kan ändras efter redundans. Läs mer om diskstorlekar och IOPS-priser vid [skalbarhets- och prestandamål för VM-diskar i Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Resursgrupp: Du kan välja en [resursgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), från vilken en virtuell dator blir en del av en efter redundans. Du kan ändra den här inställningen när som helst före redundans. Om du efter redundans migrerar den virtuella datorn till en annan resursgrupp, skyddsinställningarna för den virtuella datorns avbrott.
    * Tillgänglighetsuppsättning: Du kan välja en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) om den virtuella datorn behöver vara en del av en efterväxling. När du väljer en tillgänglighetsuppsättning bör du tänka på följande:

        * Endast tillgänglighetsuppsättningar som tillhör den angivna resursgruppen visas.  
        * Virtuella datorer som finns i olika virtuella nätverk kan inte vara en del av samma tillgänglighetsuppsättning.
        * Endast virtuella datorer av samma storlek kan vara en del av en tillgänglighetsuppsättning.
1. Du kan också lägga till information om målnätverket, undernätet och IP-adressen som har tilldelats Azure VM.
2. I **Diskar**kan du se operativsystemet och datadiskarna på den virtuella datorn som ska replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

Du kan ange IP-måladressen. Om du inte anger en adress använder den misslyckade virtuella datorn DHCP. Om du anger en adress som inte är tillgänglig vid redundans fungerar inte redundansen. Om adressen är tillgänglig i test failover-nätverket kan du använda samma mål-IP-adress för test redundans.

Antalet nätverkskort styrs av den storlek som du anger för den virtuella måldatorn enligt följande:

- Om antalet nätverkskort på den virtuella datorn för källan är mindre än eller lika med antalet kort som tillåts för måldass storlek, har målet samma antal kort som källan.
- Om antalet kort för den virtuella källdatorn överskrider det antal som tillåts för måldass storlek används den maximala målstorleken. Om en virtuell dator har två nätverkskort och måldatorns storlek stöder fyra, har målvirtualen två kort. Om källdatorn har två kort men målstorleken bara stöder ett, har måldatorn bara ett kort.
- Om den virtuella datorn har flera nätverkskort ansluter de alla till samma nätverk. Det första kortet som visas i listan *default* blir också standardnätverkskortet på den virtuella Azure-datorn. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid Benefit för att spara på licenskostnader för Windows Server-datorer som migreras till Azure. Förmånen gäller även för Azure-haveriberedskap. Om du är kvalificerad kan du tilldela förmånen till den virtuella datorn som Site Recovery skapar om det finns en redundansväxling. Det gör du genom att följa dessa steg:
1. Gå till **egenskaperna dator och nätverk** för den replikerade virtuella datorn.
2. Svara på frågan om du har en Windows Server-licens som gör dig kvalificerad för Azure Hybrid Benefit.
3. Bekräfta att du har en berättigad Windows Server-licens med Software Assurance som du kan använda för att tillämpa förmånen på den virtuella datorn som skapas vid redundans.
4. Spara inställningarna för den replikerade virtuella datorn.

Läs mer om [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har nått ett skyddat tillstånd kan du prova en [redundans för](site-recovery-failover.md) att kontrollera om ditt program visas i Azure.

* Lär dig hur du [rengör registrerings- och skyddsinställningar för](site-recovery-manage-registration-and-protection.md) att inaktivera replikering.
* Lär dig hur du [automatiserar replikering för dina virtuella datorer med powershell](vmware-azure-disaster-recovery-powershell.md).
