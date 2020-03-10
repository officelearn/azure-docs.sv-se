---
title: Aktivera virtuella VMware-datorer för haveri beredskap med Azure Site Recovery
description: I den här artikeln beskrivs hur du aktiverar VMware VM-replikering för haveri beredskap med hjälp av tjänsten Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363272"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Azure för virtuella VMware-datorer

I den här artikeln beskrivs hur du aktiverar replikering av lokala virtuella VMware-datorer till Azure.

## <a name="resolve-common-issues"></a>Lös vanliga problem

* Varje disk ska vara mindre än 4 TB.
* OS-disken måste vara en standard disk, inte en dynamisk disk.
* För generation 2/UEFI-aktiverade virtuella datorer bör operativ system familjen vara Windows och start disken bör vara mindre än 300 GB.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har:

- [Konfigurera din lokala käll miljö](vmware-azure-set-up-source.md).
- [Konfigurera mål miljön i Azure](vmware-azure-set-up-target.md).
- [Kontrol lera krav och förutsättningar](vmware-physical-azure-support-matrix.md) innan du börjar. Viktiga saker att tänka på är:
    - [Operativ system som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) för replikerade datorer.
    - Stöd för [lagring/disk](vmware-physical-azure-support-matrix.md#storage) .
    - [Azure-krav](vmware-physical-azure-support-matrix.md#azure-vm-requirements) som lokala datorer ska uppfylla.


## <a name="before-you-start"></a>Innan du börjar
Tänk på följande när du replikerar virtuella VMware-datorer:

* Ditt Azure-användarkonto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) för att aktivera replikering av en ny virtuell dator till Azure.
* Virtuella VMware-datorer identifieras var 15: e minut. Det kan ta 15 minuter eller längre för virtuella datorer att visas i Azure Portal efter identifieringen. På samma sätt kan identifieringen ta 15 minuter eller längre när du lägger till en ny vCenter-Server eller vSphere-värd.
* Det kan ta 15 minuter eller längre innan miljö ändringar på den virtuella datorn (till exempel installation av VMware-verktyg) uppdateras i portalen.
* Du kan kontrol lera den senaste identifierade tiden för virtuella VMware-datorer: se den **senaste kontakt vid** fältet på sidan **konfigurations servrar** för vCenter-servern/vSphere-värden.
* Om du vill lägga till virtuella datorer för replikering utan att vänta på den schemalagda identifieringen markerar du konfigurations servern (men klickar inte på den) och väljer **Uppdatera**.
* När du aktiverar replikering, om den virtuella datorn är för beredd, installerar processervern automatiskt Azure Site Recovery mobilitets tjänsten på den.

## <a name="enable-replication"></a>Aktivera replikering

Observera följande information innan du följer stegen i det här avsnittet:
* Azure Site Recovery replikeras nu direkt till hanterade diskar för alla nya replikeringar. Processervern skriver replikeringsinställningarna till ett cache Storage-konto i mål regionen. De här loggarna används för att skapa återställnings punkter i replik Managed disks som har namngivnings konventionen asrseeddisk.
* PowerShell-stöd för replikering till hanterade diskar är tillgängligt från [AZ. RecoveryServices-modul version 2.0.0 och senare](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Vid redundansväxlingen används den återställnings punkt som du väljer för att skapa den mål hanterade disken.
* Virtuella datorer som tidigare har kon figurer ATS för replikering till mål lagrings konton påverkas inte.
* Replikering till lagrings konton för en ny virtuell dator är endast tillgängligt via ett REST-API (Representational State Transfer) och PowerShell. Använd Azure REST API version 2016-08-10 eller 2018-01-10 för replikering till lagrings konton.

Följ stegen nedan för att aktivera replikering:
1. Gå till **steg 2: replikera program** > **källa**. När du har aktiverat replikering för första gången väljer du **+ Replikera** i valvet för att aktivera replikering för ytterligare virtuella datorer.
2. På **käll** sidan > **källa**väljer du konfigurations servern.
3. För **dator typ**väljer du **Virtual Machines** eller **fysiska datorer**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen är inte relevant om du replikerar fysiska datorer.
5. Välj processervern. Om det inte finns några ytterligare process servrar som skapats, kommer den inbyggda processervern av konfigurations servern att vara tillgänglig i list rutan. Hälso status för varje processerver anges enligt rekommenderade gränser och andra parametrar. Välj en felfri processerver. Det går inte att välja en [kritisk](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver. Du kan antingen [Felsöka och lösa](vmware-physical-azure-troubleshoot-process-server.md) felen **eller** konfigurera en [skalbar processerver](vmware-azure-set-up-process-server-scale.md).
    ![Aktivera fönstret för replikeringskälla](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Från [9,24 versioner](service-updates-how-to.md#links-to-currently-supported-update-rollups)införs ytterligare aviseringar för att förbättra hälso aviseringarna i processervern. Uppgradera Site Recovery-komponenter till 9,24-versioner eller högre för att alla aviseringar ska genereras.

6. För **mål**väljer du den prenumeration och resurs grupp där du vill skapa de misslyckade virtuella datorerna. Välj den distributions modell som du vill använda i Azure för de virtuella datorer som har redundansväxlats.
2. Välj det Azure-nätverk och undernät som de virtuella Azure-datorerna ska ansluta till efter redundansväxlingen. Nätverket måste finnas i samma region som Site Recovery tjänst valvet.

   Välj **Konfigurera nu för valda datorer** om du vill tillämpa nätverks inställningen på alla virtuella datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket per virtuell dator. Om du inte har ett nätverk måste du skapa ett. Om du vill skapa ett nätverk med hjälp av Azure Resource Manager väljer du **Skapa nytt**. Välj ett undernät om det är tillämpligt och välj sedan **OK**.
   
   ![Aktivera fönstret Replication Target](./media/vmware-azure-enable-replication/enable-rep3.png)

1. För **virtuella datorer** > **Välj virtuella datorer**väljer du varje virtuell dator som du vill replikera. Du kan bara välja virtuella datorer för vilka replikering kan aktive ras. Välj sedan **OK**. Om du inte kan se eller välja en viss virtuell dator, se [käll datorn inte i Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) för att lösa problemet.

    ![Aktivera replikering Välj Virtual Machines-fönstret](./media/vmware-azure-enable-replication/enable-replication5.png)

1. För **egenskaper** > **Konfigurera egenskaper**väljer du det konto som processervern använder för att automatiskt installera Site Recovery mobilitets tjänsten på den virtuella datorn. Välj också den typ av mål som den hanterade disken ska replikeras till baserat på dina data omsättnings mönster.
10. Som standard replikeras alla diskar på den virtuella käll datorn. Om du vill utesluta diskar från replikering avmarkerar du kryss rutan **Inkludera** för diskar som du inte vill replikera. Välj sedan **OK**. Du kan ange ytterligare egenskaper senare. Läs mer om att [utesluta diskar](vmware-azure-exclude-disk.md).

    ![Aktivera fönstret Konfigurera egenskaper för replikering](./media/vmware-azure-enable-replication/enable-replication6.png)

1. I **replikeringsinställningar** > **konfigurerar du replikeringsinställningar**, verifierar att rätt replikeringsprincip är markerad. Du kan ändra inställningarna för replikeringsprincipen i **inställningar** > **replikeringsprinciper** > ***princip namn*** > **Redigera inställningar**. Ändringar som du tillämpar på en princip gäller även för replikering och nya virtuella datorer.
1. Aktivera **konsekvens för flera virtuella** datorer om du vill samla in virtuella datorer i en replikeringsgrupp. Ange ett namn för gruppen och välj sedan **OK**.

    > [!NOTE]
    >    * Virtuella datorer i en replikeringsgrupp replikeras tillsammans och har delade krasch-konsekventa och programkonsekventa återställnings punkter när de växlar över.
    >    * Samla in virtuella datorer och fysiska servrar så att de speglar dina arbets belastningar. Att aktivera konsekvens för flera virtuella datorer kan påverka arbets Belastningens prestanda. Gör bara detta om de virtuella datorerna kör samma arbets belastning och du behöver konsekvens.

    ![Fönstret aktivera replikering](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Välj **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** på **inställningar** > **jobb** > **Site Recovery jobb**. När jobbet **Slutför skydd** har körts är den virtuella datorn klar för redundans.

## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Kontrol lera sedan egenskaperna för den virtuella käll datorn. Kom ihåg att namnet på den virtuella Azure-datorn måste följa kraven för den [virtuella Azure-datorn](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Gå till **inställningar** > **replikerade objekt**och välj sedan den virtuella datorn. Sidan **Essentials** visar information om den virtuella datorns inställningar och status.
1. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.
1. I **beräknings-och nätverks** > **beräknings egenskaper**kan du ändra flera egenskaper för virtuella datorer. 

    ![Fönstret Egenskaper för beräkning och nätverk](./media/vmware-azure-enable-replication/vmproperties.png)

    * Namn på virtuell Azure-dator: ändra namnet så att det uppfyller Azure-kraven, om det behövs.
    * Storlek på virtuell måldator eller VM-typ: standard storleken på virtuella datorer väljs baserat på några parametrar som inkluderar disk antal, antal nätverkskort, antal processor kärnor, minne och tillgängliga storlekar för virtuella dator roller i Azure-regionen. Azure Site Recovery väljer den första tillgängliga virtuella dator storleken som uppfyller alla kriterier. Du kan välja en annan VM-storlek baserat på dina behov när som helst före redundansväxlingen. Observera att storleken på den virtuella dator disken också baseras på storleken på käll disken och bara kan ändras efter redundansväxlingen. Lär dig mer om disk storlekar och IOPS-priser vid [skalbarhet och prestanda mål för VM-diskar i Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Resurs grupp: du kan välja en [resurs grupp](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)där en virtuell dator blir en del av en post växling vid fel. Du kan ändra den här inställningen när som helst före redundansväxlingen. Efter redundansväxlingen, om du migrerar den virtuella datorn till en annan resurs grupp, avbryts skydds inställningarna för den virtuella datorn.
    * Tillgänglighets uppsättning: du kan välja en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) om den virtuella datorn måste ingå i en post-redundansväxling. Tänk på följande när du väljer en tillgänglighets uppsättning:

        * Endast tillgänglighets uppsättningar som tillhör den angivna resurs gruppen visas.  
        * Virtuella datorer som finns i olika virtuella nätverk kan inte ingå i samma tillgänglighets uppsättning.
        * Endast virtuella datorer av samma storlek kan ingå i en tillgänglighets uppsättning.
1. Du kan också lägga till information om mål nätverket, under nätet och IP-adressen som har tilldelats till den virtuella Azure-datorn.
2. I **diskar**kan du se operativ systemet och data diskarna på den virtuella datorn som kommer att replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

Du kan ange IP-måladressen. Om du inte anger en adress, använder den virtuella datorn som inte är på den virtuella datorn DHCP. Om du anger en adress som inte är tillgänglig vid redundans fungerar inte redundansväxlingen. Om adressen är tillgänglig i nätverket för redundanstest kan du använda samma mål-IP-adress för redundanstest.

Antalet nätverkskort styrs av den storlek som du anger för den virtuella mål datorn enligt följande:

- Om antalet nätverkskort på den virtuella käll datorn är mindre än eller lika med antalet nätverkskort som tillåts för den virtuella mål datorns storlek, har målet samma antal kort som källan.
- Om antalet nätverkskort för den virtuella käll datorn överskrider det antal som tillåts för den virtuella mål datorns storlek, används Max storleken för målet. Om till exempel en virtuell käll dator har två nätverkskort och storleken på den virtuella mål datorn stöder fyra, har den virtuella mål datorn två kort. Om den virtuella käll datorn har två nätverkskort men mål storleken endast stöder ett, har den virtuella mål datorn bara ett kort.
- Om den virtuella datorn har flera nätverkskort ansluter alla till samma nätverk. Det första kortet som visas i listan blir också *standard* nätverkskortet på den virtuella Azure-datorn. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid-förmån för att spara licens kostnader för Windows Server-datorer som migreras till Azure. Förmånen gäller även för Azure Disaster Recovery. Om du är berättigad kan du tilldela förmånen för den virtuella datorn som Site Recovery skapar om det finns en redundansväxling. Det gör du genom att följa dessa steg:
1. Gå till **dator-och nätverks egenskaperna** för den replikerade virtuella datorn.
2. Svara när du tillfrågas om du har en Windows Server-licens som gör att du är berättigad till Azure Hybrid-förmån.
3. Bekräfta att du har en giltig Windows Server-licens med Software Assurance som du kan använda för att tillämpa förmånen på den virtuella datorn som ska skapas vid en redundansväxling.
4. Spara inställningarna för den replikerade virtuella datorn.

Läs mer om [Azure Hybrid-förmån](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har nått ett skyddat tillstånd kan du prova med en [redundansväxling](site-recovery-failover.md) för att kontrol lera om programmet visas i Azure.

* Lär dig hur du [rensar registrerings-och skydds inställningarna](site-recovery-manage-registration-and-protection.md) för att inaktivera replikering.
* Lär dig hur du [automatiserar replikering för dina virtuella datorer med hjälp av PowerShell](vmware-azure-disaster-recovery-powershell.md).
