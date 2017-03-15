---
title: Hur fungerar Site Recovery? | Microsoft Docs
description: "Den här artikeln innehåller en översikt över arkitekturen i Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 22b50dd6242e8c10241b0626b48f8ef842b6b0fd
ms.openlocfilehash: c33ca9e5292096a0fd96d98da3e89d721463e903
ms.lasthandoff: 03/02/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Hur fungerar Azure Site Recovery?

Den här artikeln beskriver den underliggande arkitekturen i tjänsten [Azure Site Recovery](site-recovery-overview.md) och de komponenter som gör att den fungerar.

Skriv dina kommentarer längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="replication-to-azure"></a>Replikering till Azure

Du kan replikera följande till Azure:
- **VMware**: Lokala virtuella VMware-datorer som körs på en [värd som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Du kan replikera virtuella VMware-datorer som kör [operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
- **Hyper-V**: Lokala virtuella Hyper-V-datorer som körs på [värdar som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- **Fysiska datorer**: Lokala fysiska servrar som kör Windows eller Linux på [operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Du kan replikera virtuella Hyper-V-datorer som körs på ett gästoperativsystem [som stöds av Hyper-V och Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="vmware-replication-to-azure"></a>Replikering av VMware till Azure

Område | Komponent | Information
--- | --- | ---
**Azure** | I Azure behöver du ett Azure-konto, ett Azure-lagringskonto och ett Azure-nätverk. | Lagring och nätverk kan vara Resource Manager-konton eller klassiska konton.<br/><br/>  Replikerade data lagras i lagringskontot och virtuella Azure-datorer skapas med replikerad data när redundansväxling uppstår från din lokala plats. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurationsserver** | En enda hanteringsserver (virtuell VMWare-dator) kör alla lokala komponenter – konfigurationsserver, processerver, huvudmålserver | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
 **Processerver**:  | Installeras som standard på konfigurationsservern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern hanterar också push-installationen av mobilitetstjänsten till skyddade datorer och utför automatisk identifiering av virtuella VMware-datorer.<br/><br/> Allt eftersom distributionen växer kan du lägga till ytterligare separata dedikerade servrar för att hantera allt större mängder replikeringstrafik.
 **Huvudmålservern** | Installeras som standard på den lokala konfigurationsservern. | Hanterar replikeringsdata vid återställning efter fel från Azure.<br/><br/> Om trafikvolymer för återställning efter fel är stora, kan du distribuera en separat huvudmålserver för återställning efter fel.
**VMware-servrar** | Virtuella VMware-datorer finns på vSphere ESXi-servrar, och vi rekommenderar att en vCenter-server hanterar värdarna. | Du lägger till VMware-servrar till ditt Recovery Services-valv.<br/><br/> I
**Replikerade datorer** | Mobilitetstjänsten måste installeras på varje virtuell VMware-dator som du vill replikera. Den kan installeras manuellt på varje dator eller med en push-installation från processervern.

**Bild 1: VMware till Azure-komponenter**

![Komponenter](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in distributionen, inklusive Azure komponenter och Recovery Services-valvet. I valvet anger du replikeringskälla och mål, ställer in konfigurationsservern, lägger till VMware-servrar, skapar en replikeringsprincip, distribuerar Mobilitetstjänster, aktiverar replikering och kör ett redundanstest.
2.  Datorerna börjar replikera enligt replikeringsprincipen och en inledande datakopia replikeras till Azure Storage.
4. Replikering av deltaändringar till Azure börjar efter att den inledande replikeringen har slutförts. Spårade ändringar för en dator lagras i en .hrl-fil.
    - Replikerande datorer kommunicerar med konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.
    - Replikerande datorer skickar replikeringsdata till processervern på port HTTPS 9443 inkommande (kan konfigureras).
    - Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.
    - Processervern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure Storage över port 443 utgående.
    - Om du aktiverar multi-VM-konsekvens, kommunicerar datorer i replikeringsgruppen med varandra via port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Detta är användbart om datorerna kör samma arbetsbelastning och måste överensstämma.
5. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering). Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.

**Bild 2: VMware till Azure-replikering**

![Optimerad](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>Redundans och återställning efter fel

1. När du har kontrollerat att redundanstestningen fungerar som förväntat kan du köra oplanerade redundanstestningar till Azure efter behov. Planerad redundans stöds inte.
2. Du kan redundansväxla en enskild dator eller skapa [återställningsplaner](site-recovery-create-recovery-plans.md) för att samordna redundans för flera datorer.
3. När du kör en redundans skapas virtuella replikdatorer i Azure. Du etablerar en redundansväxling för att komma åt arbetsbelastningen från den virtuella Azure-replikdatorn.
4. När din primära lokala plats är tillgänglig igen, kan du återställa dit. Du ställer in en infrastruktur för återställning efter fel, startar replikering av datorn från den sekundära platsen till den primära och kör en oplanerad redundans från den sekundära platsen. När du etablerat den här redundansen, är dina data tillbaka lokalt och du måste aktivera replikering till Azure igen. [Läs mer](site-recovery-failback-azure-to-vmware.md)

Det finns några återställningskrav:


- **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure efter en redundansväxling måste du konfigurera en Azure-dator som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
- **VPN-anslutning**: För återställning efter fel behöver du en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
- **Separat lokal huvudmålserver**: Den lokala huvudmålservern hanterar återställning efter fel. Huvudmålservern installeras som standard på hanteringsservern, men om du växlar tillbaka större volymer trafik bör du konfigurera en separat lokal huvudmålserver för detta ändamål.
- **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Denna skapas automatiskt när du skapade din replikeringsprincip.

**Bild 3: VMware/fysisk återställning efter fel**

![Återställning efter fel](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-server-replication-to-azure"></a>Fysisk serverreplikering till Azure

Det här replikeringsscenariot använder även samma komponenter och samma process som vid [VMware till Azure](#vmware-replication-to-azure), men tänk på dessa skillnader:

- Du kan använda en fysisk server för konfigurationsservern istället för en virtuell VMware-dator
- Du behöver en lokal VMware-infrastruktur för återställning efter fel. Du kan inte återställa till en fysisk dator.

## <a name="hyper-v-replication-to-azure"></a>Hyper-V-replikering till Azure

**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | I Azure behöver du ett Microsoft Azure-konto, ett Azure-lagringskonto och ett Azure-nätverk. | Lagring och nätverk kan vara Resource Manager-baserade eller klassiska konton.<br/><br/> Replikerade data lagras i lagringskontot och virtuella Azure-datorer skapas med replikerad data när redundansväxling uppstår från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | Hyper-V-värdar i VMM-moln | Om Hyper-V-värdar hanteras i VMM-moln kan du registrera VMM-servern i Recovery Services-valvet.<br/><br/> På VMM-servern installerar du Site Recovery-providern för att samordna replikeringen med Azure.<br/><br/> Du behöver logiska nätverk och VM-nätverk för att konfigurera nätverksmappning. Ett nätverk för virtuella datorer bör vara kopplat till ett logiskt nätverk som är associerat med molnet.
**Hyper-V-värd** | Hyper-V-servrar kan distribueras med eller utan VMM-servern. | Om det inte finns någon VMM-server installeras Site Recovery-providern på värden för att dirigera replikering med Site Recovery via internet. Om det finns en VMM-server installeras providern på den och inte på värden.<br/><br/> Recovery Services-agenten installeras på värden för att hantera datareplikering.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Virtuella Hyper-V-datorer** | Du behöver en eller flera virtuella datorer på Hyper-V-värdservern. | Inget behöver uttryckligen installeras på virtuella datorer


### <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in Azure-komponenterna. Vi rekommenderar att du ställer in konton för lagring och nätverk innan du påbörjar Site Recovery-distributionen.
2. Du skapar ett Replication Services-valv för Site Recovery och konfigurerar valvinställningar, inklusive:
    - Inställningar för källa och mål. Om du inte hanterar Hyper-V-värdar i ett VMM-moln, skapar du en behållare för Hyper-V-platsen för målet och lägger till Hyper-V-värdar i den. Om dina Hyper-V-värdar hanteras i VMM, är källan VMM-molnet. Målet är Azure.
    - Installation av Azure Site Recovery-Provider och Microsoft Azure Recovery Services-agenten. Om du har VMM, kommer Providern installeras på den och agenten på varje Hyper-V-värd. Om du inte har VMM, installeras både Providern och agenten på varje värd.
    - Du skapar en replikeringsprincip för Hyper-V-platsen eller VMM-molnet. Principen tillämpas på alla virtuella datorer som befinner sig på värdar på platsen eller i molnet.
    - Du aktiverar replikering för virtuella Hyper-V-datorer. Inledande replikering sker i enlighet med inställningarna för replikeringsprincipen.
4. Dataändringar spåras och replikering av deltaändringar till Azure påbörjas efter att den inledande replikeringen har slutförts. Spårade ändringar för ett objekt lagras i en .hrl-fil.
5. Du kör ett redundanstest för att kontrollera att allt fungerar.

### <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad [redundansväxling](site-recovery-failover.md) från lokala Hyper-V virtuella datorer till Azure. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
2. Du kan redundansväxla en enskild dator eller skapa [återställningsplaner](site-recovery-create-recovery-plans.md) för att samordna redundans för flera datorer.
4. När du har kört redundansen, ska du kunna se den skapade virtuella replikdatorn i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
5. Du etablerar därefter redundansen att börja ha åtkomst till arbetsbelastningen från den virtuella Azure-replikdatorn.
6. När din primära lokala plats är tillgänglig igen, kan du [återställa dit](site-recovery-failback-from-azure-to-hyper-v.md). Du startar en planerad redundans från Azure till den primära platsen. För en planerad redundans kan du välja återställning efter fel till samma virtuella dator eller till en alternativ plats och synkronisera ändringarna mellan Azure och lokalt, så att ingen data går förlorad. När virtuella datorer skapas lokalt, etablerar du redundansen.

**Bild 4: Hyper-V-plats till Azure replikering**

![Komponenter](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Bild 5: Hyper-V i VMM-moln till Azure replikering**

![Komponenter](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-to-a-secondary-site"></a>Replikering till en sekundär plats

Du kan replikera följande till din sekundära plats:

- **VMware**: Lokala virtuella VMware-datorer som körs på en [värd som stöds](site-recovery-support-matrix-to-sec-site.md#on-premises-servers). Du kan replikera virtuella VMware-datorer som kör [operativsystem som stöds](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
- **Fysiska datorer**: Lokala fysiska servrar som kör Windows eller Linux på [operativsystem som stöds](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: Lokala virtuella Hyper-V-datorer som körs på [Hyper-V-värdar som stöds](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) och hanteras i VMM-moln. [värdar som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Du kan replikera virtuella Hyper-V-datorer som körs på ett gästoperativsystem [som stöds av Hyper-V och Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="vmware-vmphysical-server-replication-to-a-secondary-site"></a>Replikering av virtuella VMware-datorer/fysiska servrar till en sekundär plats

### <a name="components"></a>Komponenter

**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | Du kan distribuera det här scenariot med InMage Scout. | För att hämta InMage Scout behöver du en Azure-prenumeration.<br/><br/> När du har skapat ett Recovery Services-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Processervern** | Finns på primär plats | Du distribuerar processervern för att hantera cachelagring, komprimering och dataoptimering.<br/><br/> Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**Konfigurationsserver** | Finns på sekundär plats | Konfigurationsservern hanterar, konfigurerar och övervakar distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen.
**vContinuum-server** | Valfri. Installeras på samma plats som konfigurationsservern. | Den innehåller en konsol för att hantera och övervaka din skyddade miljö.
**Huvudmålservern** | Finns på den sekundära platsen | Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna.<br/><br/> Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar.<br/><br/> Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. Unified-agenten är installerad på den här servern.
**VMware ESX/ESXi och vCenter-server** |  Virtuella datorer finns på ESX-/ESXi-värdar. Värdar hanteras med en vCenter-server | Du behöver en VMware-infrastruktur för att replikera virtuella VMware-datorer.
**Virtuella datorer/fysiska servrar** |  Unified Agent installeras på virtuella VMware-datorer eller fysiska servrar som du vill replikera. | Agenten fungerar som en kommunikationsprovider mellan alla komponenter.


### <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera.
2. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern.
3. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

**Bild 6: VMware till VMware-replikering**

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-vm-replication-to-a-secondary-site"></a>Replikering av virtuella Hyper-V-datorer till sekundär sida


**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | Du behöver ett Microsoft Azure-konto. |
**VMM-server** | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen | Varje VMM-server ska vara ansluten till internet.<br/><br/> Varje server bör ha minst ett VMM-privat moln, med Hyper-V-funktionsprofilen inställld.<br/><br/> Du installerar Azure Site Recovery-providern på VMM-servern. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Kommunikationen mellan providern och Azure är säker och krypterad.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen.<br/><br/> Servrarna ska vara anslutna till internet.<br/><br/> Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | Finns på Hyper-V-värdgästservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

### <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in Azure-kontot.
2. Du skapar ett Replication Services-valv för Site Recovery och konfigurerar valvinställningar, inklusive:

    - Replikeringskällan och -målet (primära och sekundära platser).
    - Installation av Azure Site Recovery-Provider och Microsoft Azure Recovery Services-agenten. Providern är installerad på VMM-servrar och agenten på varje Hyper-V-värd.
    - Du skapar en replikeringsprincip för VMM-källmolnet. Principen tillämpas på alla virtuella datorer som befinner sig på värdar i molnet.
    - Du aktiverar replikering för virtuella Hyper-V-datorer. Inledande replikering sker i enlighet med inställningarna för replikeringsprincipen.
4. Dataändringar spåras och replikering av deltaändringar till Azure påbörjas efter att den inledande replikeringen har slutförts. Spårade ändringar för ett objekt lagras i en .hrl-fil.
5. Du kör ett redundanstest för att kontrollera att allt fungerar.

**Bild 7: VMM till VMM-replikering**

![Lokal till lokal](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>Redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad [redundansväxling](site-recovery-failover.md) mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
2. Du kan redundansväxla en enskild dator eller skapa [återställningsplaner](site-recovery-create-recovery-plans.md) för att samordna redundans för flera datorer.
4. Om du utför en oplanerad redundansväxling till en sekundär plats efter att redundansdatorerna på den sekundära platsen inte aktiverats för skydd eller replikering. Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
5. Sedan kan du etablera redundansen för att få åtkomst till arbetsbelastningen från den virtuella replikdatorn.
6. När din primära plats är tillgänglig igen, kan du initiera omvänd replikering för att replikera från den sekundära platsen till den primära. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
7. För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.


## <a name="hyper-v-replication-workflow"></a>Arbetsflöde för Hyper-V-replikering

**Arbetsflödesfas** | **Åtgärd**
--- | ---
1. **Aktivera skydd** | Efter att du aktiverar skydd för en virtuell Hyper-V-dator initieras jobbet **Aktivera skydd**, för att kontrollera att datorn överensstämmer med förhandskraven. Jobbet anropar två metoder:<br/><br/> [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) för att konfigurera replikeringen med de inställningar som du har konfigurerat.<br/><br/> [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), för att initiera en fullständig VM-replikering.
2. **Inledande replikering** |  En ögonblicksbild av den virtuella datorn tas och de virtuella hårddiskarna replikeras en i taget tills alla har kopierats till den sekundära platsen.<br/><br/> Hur lång tid det tar att slutföra detta beror på storleken på de virtuella datorerna, nätverkets bandbredd och metoden för inledande replikering.<br/><br/> Om diskändringar inträffar under en pågående inledande replikering spåras dessa ändringar av spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl) som finns i samma mapp som diskarna.<br/><br/> Varje disk har en associerad HRL-fil som skickas till den sekundära lagringsplatsen.<br/><br/> Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår. När den inledande replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och diskförändringarna (delta) i loggen synkroniseras och sammanfogas.
3. **Slutför skyddet** | När replikeringen har slutförts konfigurerar jobbet **Slutför skydd** nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad.<br/><br/> Om du replikerar till Azure kan du behöva justera inställningarna för den virtuella datorn så att den är redo för redundans.<br/><br/> Nu kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.
4. **Replikering** | Efter den första replikeringen startar deltasynkroniseringen baserat på replikeringsinställningarna.<br/><br/> **Replikeringsfel**: Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så görs en omsynkronisering. Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering. Vid en omsynkronisering minimeras mängden data som skickas genom att kontrollsummor beräknas för de virtuella käll- och måldatorerna varefter endast förändringarna (delta) skickas. När omsynkroniseringen är klar återupptas deltareplikeringen. Omsynkroniseringen schemaläggs som standard för automatisk körning utanför kontorstid, men du kan synkronisera om en virtuell dator manuellt.<br/><br/> **Replikeringsfel**: Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Om det är ett oåterkalleligt fel, till exempel ett autentiserings- eller auktoriseringsfel, eller en replikdator som har ett ogiltigt tillstånd, så görs inget nytt försök. Om det är ett korrigerbart fel, till exempel ett nätverksfel eller ont om ledigt utrymme/minne, så görs ett nytt försök med längre och längre intervall mellan försöken (varje minut, varannan minut, var 4:e, var 8:e, var 10:e och sedan var 30: e minut).
5. **Planerad/oplanerad redundans** | Du kan köra planerade eller oplanerade redundansväxlingar efter behov.<br/><br/> Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.<br/><br/> När de virtuella replikdatorerna har skapats försätts de i ett tillstånd där de väntar på bekräftelse. Du måste etablera dem för att slutföra redundansen.<br/><br/> När den primära platsen är igång kan du återgå till den när den är tillgänglig.


**Bild 8: Hyper-V-arbetsflöde**

![arbetsflöde](./media/site-recovery-components/arch-hyperv-azure-workflow.png)




## <a name="next-steps"></a>Nästa steg

[Kontrollera krav](site-recovery-prereq.md)

