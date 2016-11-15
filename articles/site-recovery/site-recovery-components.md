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
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94250becb238adad7983d4b45939eb988411fca3


---
# <a name="how-does-azure-site-recovery-work"></a>Hur fungerar Azure Site Recovery?
Den här artikeln beskriver den underliggande arkitekturen i tjänsten Azure Site Recovery och de komponenter som gör att den fungerar.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Översikt
Organisationer behöver en BCDR-strategi som beskriver hur appar, arbetsbelastningar och data fungerar och är tillgängliga under planerade och oplanerade driftavbrott och som ser till att systemets normala drifttillstånd återställs så fort som möjligt. Avsikten med en BCDR-strategi är att skydda affärsdata och se till att de kan återställas samt att säkerställa att arbetsbelastningar förblir tillgängliga i händelse av allvarliga fel.

Site Recovery är en Azure-tjänst som stödjer din BCDR-strategi genom att dirigera replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. Läs mer i [Vad är Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery på Azure Portal
Azure har två olika [distributionsmodeller](../resource-manager-deployment-model.md) för att skapa och arbeta med resurser: Azure Resource Manager-modellen och den klassiska tjänsthanteringsmodellen. Azure har också två portaler – den [klassiska Azure-portalen](https://manage.windowsazure.com/) som stöder den klassiska distributionsmodellen och [Azure-portalen](https://portal.azure.com) som stöder båda distributionsmodellerna.

Site Recovery är tillgängligt både på den klassiska portalen och på Azure Portal. På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen. På Azure-portalen kan du använda den klassiska modellen eller Resource Manager-distributioner. [Läs mer](site-recovery-overview.md#site-recovery-in-the-azure-portal) om hur du distribuerar med Azure-portalen.

Informationen i den här artikeln gäller både klassiska distributioner och distributioner via Azure Portal. Eventuella skillnader beskrivs i förekommande fall.

## <a name="deployment-scenarios"></a>Distributionsscenarier
Site Recovery kan distribueras för att samordna replikeringen i ett antal scenarier:

* **Replikera virtuella VMware-datorer**: Du kan replikera lokala virtuella VMware-datorer till Azure eller till ett sekundärt datacenter.
* * **Replikera fysiska datorer**: Du kan replikera fysiska datorer som kör Windows eller Linux till Azure eller till ett sekundärt datacenter. Fysiska datorer replikeras på nästan samma sätt som virtuella VMware-datorer
* **Replikera virtuella Hyper-V-datorer (utan VMM)**: Du kan replikera virtuella Hyper-V-datorer som inte hanteras av VMM till Azure.
* **Replikera virtuella Hyper-V-datorer som hanteras i System Center VMM-moln**: Du kan replikera lokala virtuella Hyper-V-datorer som körs på Hyper-V-värdservrar i VMM-moln till Azure eller till ett sekundärt datacenter. Du kan replikera med Hyper-V-standardreplikering eller SAN-replikering.
* **Migrera virtuella datorer**: Du kan använda Site Recovery för att [migrera virtuella IaaS-datorer i Azure](site-recovery-migrate-azure-to-azure.md) mellan regioner eller för att [migrera Windows-instanser i AWS](site-recovery-migrate-aws-to-azure.md) till virtuella IaaS-datorer i Azure. För närvarande stöds endast migrering vilket innebär att du kan redundansväxla dessa virtuella datorer, men inte växla tillbaka (återställa) dem.

Site Recovery kan replikera de flesta appar som körs på dessa virtuella datorer och fysiska servrar. En fullständig sammanfattning av de appar som stöds finns i [Vilka arbetsbelastningar kan Azure Site Recovery skydda?](site-recovery-workload.md)

## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replikera till Azure: Virtuella VMware-datorer eller fysiska Windows-/Linux-servrar
Du kan replikera virtuella VMware-datorer med Site Recovery på ett par olika sätt.

* **Med hjälp av Azure-portalen**: När du distribuerar Site Recovery på Azure-portal kan du växla över virtuella datorer till klassisk tjänsthanteringslagring eller till Resource Manager. Replikeringen av virtuella VMware-datorer på Azure-portalen medför flera fördelar, däribland möjligheten att replikera både till klassisk lagring och Resource Manager-lagring i Azure. [Läs mer](site-recovery-vmware-to-azure.md).
* **Med hjälp av den klassiska portalen**: Du kan distribuera Site Recovery på den klassiska portalen med en optimerad miljö. Detta rekommenderas för alla nya distributioner på den klassiska portalen. I den här typen av distribution kan du bara växla över virtuella datorer till klassisk lagring i Azure, inte till Resource Manager-lagring. [Läs mer](site-recovery-vmware-to-azure-classic.md). Det finns också en [äldre miljö](site-recovery-vmware-to-azure-classic-legacy.md) för att konfigurera VMware-replikering på den klassiska portalen. Den bör inte användas för nya distributioner.  Om du redan har distribuerat med den äldre miljön rekommenderar vi att du [lär dig hur du migrerar](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) till den optimerade distributionen.

Kraven på arkitektur för Site Recovery-distributioner när du replikerar VM/fysiska VMware-servrar är i princip samma för Azure-portalen och den klassiska Azure-portalen (optimerad), med bara några få skillnader:

* Om du distribuerar på Azure-portalen kan du replikera till Resource Manager-baserad lagring och använda Resource Manager-nätverk för att ansluta till virtuella datorer i Azure efter en redundansväxling.
* När du distribuerar på Azure-portalen stöds både LRS- och GRS-lagring. GRS krävs på den klassiska portalen.
* Distributionen är enklare och mer användarvänlig på Azure-portalen.

Du behöver det här:

* **Azure-konto**: Du behöver ett Microsoft Azure-konto.
* **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Du kan använda ett klassiskt konto eller ett Resource Manager-lagringskonto. Kontot kan vara LRS eller GRS när du distribuerar på Azure-portalen. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.
* **Azure-nätverk**: Du behöver ett virtuellt Azure-nätverk som virtuella Azure-datorer ska ansluta till när de skapas under en redundansväxling. På Azure-portalen kan de vara nätverk som skapats i den klassiska tjänsthanteringsmodellen eller i Resource Manager-modellen.
* **Lokal konfigurationsserver**: Du behöver en lokal Windows Server 2012 R2-dator som kör konfigurationsservern och andra Site Recovery-komponenter. Om du replikerar virtuella VMware-datorer bör detta vara en virtuell VMware-dator med hög tillgänglighet. Om du vill replikera fysiska servrar kan datorn vara fysisk. Följande Site Recovery-komponenter installeras på datorn:
  * **Konfigurationsserver**: Samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering och återställning.
  * **Processerver**: Fungerar som en replikerings-gateway. Den tar emot replikeringsdata från skyddade källdatorer, optimerar dem med cachelagring, komprimering och kryptering och skickar data till Azure-lagring. Den hanterar också push-installationen av mobilitetstjänsten till skyddade datorer och utför automatisk identifiering av virtuella VMware-datorer. Allt eftersom distributionen växer kan du lägga till ytterligare separata dedikerade servrar för att hantera allt större mängder replikeringstrafik.
  * **Huvudmålserver**: Hanterar replikeringsdata vid återställning från Azure.
* **Virtuella VMware-datorer eller fysiska servrar som ska replikeras**: Mobility-tjänstkomponenten måste vara installerad på alla datorer som du vill replikera till Azure. Den här tjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till processervern. Den här komponenten kan installeras manuellt eller kan flyttas och installerats automatiskt av processervern när du aktiverar replikering för en dator.
* **vSphere-värdar/vCenter-server**: Du behöver en eller flera vSphere-värdservrar som kör virtuella VMware-datorer. Vi rekommenderar att du distribuerar en vCenter-server för att hantera dessa värdar.
* **Redundans**: Det här behöver du:
  * **Redundansväxling från fysisk-till-fysisk stöds inte**: Det betyder att om du växlar över fysiska servrar till Azure och sedan vill växla tillbaka dem så måste du växla tillbaka till en virtuell VMware-dator. Du kan inte växla tillbaka till en fysisk server. Du behöver en virtuell Azure-dator att växla över till, och om du inte distribuerade konfigurationsservern som en virtuell VMware-dator måste du konfigurera en separat huvudmålserver som en virtuell VMware-dator. Detta är nödvändigt eftersom huvudmålservern interagerar med och ansluter till VMware-lagring för att återställa diskarna till en virtuell VMware-dator.
  * * **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure efter en redundansväxling måste du konfigurera en Azure-dator som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
  * **VPN-anslutning**: För återställning efter fel behöver du en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
  * **Separat lokal huvudmålserver**: Den lokala huvudmålservern hanterar återställning efter fel. Huvudmålservern installeras som standard på hanteringsservern, men om du växlar tillbaka större volymer trafik bör du konfigurera en separat lokal huvudmålserver för detta ändamål.

**Allmän arkitektur**

![Optimerad](./media/site-recovery-components/arch-enhanced.png)

**Distributionskomponenter**

![Optimerad](./media/site-recovery-components/arch-enhanced2.png)

**Återställning efter fel**

![Förbättrad återställning efter fel](./media/site-recovery-components/enhanced-failback.png)

* [Lär dig mer](site-recovery-vmware-to-azure.md#azure-prerequisites) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) om kraven för optimerad distribution på den klassiska portalen.
* [Lär dig mer](site-recovery-failback-azure-to-vmware.md) om redundans på Azure-portalen.
* [Lär dig mer](site-recovery-failback-azure-to-vmware-classic.md) om redundans på den klassiska portalen.

## <a name="replicate-to-azure-hyperv-vms-not-managed-by-vmm"></a>Replikera till Azure: Virtuella Hyper-V-datorer som inte hanteras av VMM
Du kan replikera virtuella Hyper-V-datorer som inte hanteras av System Center VMM till Azure med Site Recovery på följande sätt:

* **Med hjälp av Azure-portalen**: När du distribuerar Site Recovery på Azure-portalen kan du växla över virtuella datorer till klassisk lagring eller till Resource Manager. [Läs mer](site-recovery-hyper-v-site-to-azure.md).
* **Med hjälp av den klassiska portalen**: Du kan distribuera Site Recovery på den klassiska portalen. I den här typen av distribution kan du bara växla över virtuella datorer till klassisk lagring i Azure, inte till Resource Manager-lagring. [Läs mer](site-recovery-hyper-v-site-to-azure-classic.md).

Arkitekturen för båda distributionerna påminner om varandra, förutom att:

* Om du distribuerar på Azure-portalen kan du replikera till Resource Manager-lagring och använda Resource Manager-nätverk för att ansluta till virtuella datorer i Azure efter en redundansväxling.
* Distributionen är enklare och mer användarvänlig på Azure-portalen.

Du behöver det här:

* **Azure-konto**: Du behöver ett Microsoft Azure-konto.
* **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. På Azure-portalen kan du använda ett klassiskt konto eller ett Resource Manager-konto. På den klassiska portalen kan du bara använda ett klassiskt konto. Replikerade data lagras i Azure och virtuella Azure-datorer skapas när redundansväxlingen sker.
* **Azure-nätverk**: Du behöver ett Azure-nätverk som virtuella Azure-datorer ska ansluta till när de skapas efter en redundansväxling.
* **Hyper-v-värd**: Du behöver en eller flera Windows Server 2012 R2 Hyper-V-värdservrar. Installera Azure Site Recovery-providern och Microsoft Azure Recovery Services-agenten på värden under Site Recovery-distributionen.
* **Virtuella Hyper-V-datorer**: Du behöver en eller flera virtuella datorer på Hyper-V-värdservern. Azure Site Recovery-providern och Azure Recovery Services-agenten på Hyper-V-värden under Site Recovery-distributionen. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Agenten hanterar replikeringsdata över HTTPS-port 443. Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.

**Allmän arkitektur**

![Hyper-V-plats till Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

* [Lär dig mer](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) om kraven för distributioner på den klassiska portalen.

## <a name="replicate-to-azure-hyperv-vms-managed-by-vmm"></a>Replikera till Azure: Virtuella Hyper-V-datorer som hanteras av VMM
Du kan replikera virtuella Hyper-V-datorer i VMM-moln till Azure med Site Recovery på följande sätt:

* **Med hjälp av Azure-portalen**: När du distribuerar Site Recovery på Azure-portalen kan du växla över virtuella datorer till klassisk lagring eller till Resource Manager. [Läs mer](site-recovery-vmm-to-azure.md).
* **Med hjälp av den klassiska portalen**: Du kan distribuera Site Recovery på den klassiska portalen. I den här typen av distribution kan du bara växla över virtuella datorer till klassisk lagring i Azure, inte till Resource Manager-lagring. [Läs mer](site-recovery-vmm-to-azure-classic.md).

Arkitekturen för båda distributionerna påminner om varandra, förutom att:

* Om du distribuerar på Azure-portalen kan du replikera till Resource Manager-baserad lagring och använda Resource Manager-nätverk för att ansluta till virtuella datorer i Azure efter en redundansväxling.
* Distributionen är enklare och mer användarvänlig på Azure-portalen.

Du behöver det här:

* **Azure-konto**: Du behöver ett Microsoft Azure-konto.
* **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. På Azure-portalen kan du använda ett klassiskt konto eller ett Resource Manager-konto. På den klassiska portalen kan du bara använda ett klassiskt konto. Replikerade data lagras i Azure och virtuella Azure-datorer skapas när redundansväxlingen sker.
* **Azure-nätverk**: Du måste konfigurera nätverksmappning så att virtuella datorer i Azure ansluts till rätt nätverk när de skapas efter en redundansväxling.
* **VMM-server**: Du behöver en eller flera lokala VMM-servrar som körs på System Center 2012 R2 och som är konfigurerade med ett eller flera privata moln. Om du distribuerar på Azure-portalen behöver du logiska nätverk och nätverk för virtuella datorer så att du kan konfigurera nätverksmappning. På den klassiska portalen är detta valfritt.  Ett nätverk för virtuella datorer bör vara kopplat till ett logiskt nätverk som är associerat med molnet.
* **Hyper-V-värd**: Du behöver en eller flera Windows Server 2012 R2 Hyper-V-värdservrar i VMM-molnet.
* **Virtuella Hyper-V-datorer**: Du behöver en eller flera virtuella datorer på Hyper-V-värdservern.

**Allmän arkitektur**

![VMM till Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

* [Lär dig mer](site-recovery-vmm-to-azure.md#azure-requirements) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-vmm-to-azure-classic.md#before-you-start) om kraven för distributioner på den klassiska portalen.

## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replikera till en sekundär plats: Virtuella VMware-datorer eller fysiska servrar
Om du vill replikera virtuella VMware-datorer eller fysiska servrar till en sekundär plats laddar du ned InMage Scout som ingår i Azure Site Recovery-prenumerationen. Du kan ladda ned verktyget från Azure-portalen eller från den klassiska Azure-portalen.

Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera. Efter den första replikeringen skickar agenten på varje dator replikeringsförändringar (delta) till processervern. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

Du behöver det här:

**Azure-konto**: Du distribuerar det här scenariot med InMage Scout. InMage Scout ingår i Azure-prenumerationer. När du har skapat ett Site Recovery-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Processerver (primär plats)**: Konfigurera processerverkomponenten på din primära plats för hantering av cachelagring, komprimering och dataoptimering. Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**VMware-baserad ESX/ESXi och vCenter-server (primär plats)**: Om du skyddar virtuella datorer i VMware behöver du en VMware-baserad EXS-/ESXi-hypervisor och eventuellt en VMware-baserad vCenter-server för att hantera hypervisorer.

* **Virtuella datorer/fysiska servrar (primär plats)**: Unified Agent måste vara installerat på de virtuella VMware-datorer eller Windows-/Linux-baserade fysiska servrar som du vill skydda. Unified Agent installeras även på de datorer som fungerar som huvudmålservrar. Agenten fungerar som en kommunikationsprovider mellan alla komponenter.
* * **Konfigurationsserver (sekundär plats)**: Konfigurationsservern är den första komponenten som du installerar. Den installeras på den sekundära platsen för att hantera, konfigurera och övervaka distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen. Det finns bara en konfigurationsserver i en distribution och den måste installeras på en dator som kör Windows Server 2012 R2.
* **vContinuum-server (sekundär plats)**: Den installeras på samma plats (den sekundära platsen) som konfigurationsservern. Den innehåller en konsol för att hantera och övervaka din skyddade miljö. I en standardinstallation är vContinuum-servern den första huvudmålservern och har Unified Agent installerad.
* **Huvudmålserver (sekundär plats)**: Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna. Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar. Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där.

**Allmän arkitektur**

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)

## <a name="replicate-to-a-secondary-site-hyperv-vms-managed-by-vmm"></a>Replikera till en sekundär plats: Virtuella Hyper-V- datorer som hanteras av VMM
Du kan replikera virtuella Hyper-V-datorer som hanteras av System Center VMM till ett sekundärt datacenter med Site Recovery på följande sätt:

* **Med hjälp av Azure-portalen**: När du distribuerar Site Recovery på Azure-portalen. [Läs mer](site-recovery-hyper-v-site-to-azure.md).
* **Med hjälp av den klassiska portalen**: Du kan distribuera Site Recovery på den klassiska portalen. [Läs mer](site-recovery-hyper-v-site-to-azure-classic.md).

Arkitekturen för båda distributionerna påminner om varandra, förutom att:

* Om du distribuerar på Azure-portalen måste du konfigurera nätverksmappning. På den klassiska portalen är detta valfritt.
* Distributionen är enklare och mer användarvänlig på Azure-portalen.
* * Om du distribuerar på den klassiska Azure-portalen är [lagringsmappning](site-recovery-storage-mapping.md) tillgängligt.

Du behöver det här:

* **Azure-konto**: Du behöver ett Microsoft Azure-konto.
* **VMM-server**: Vi rekommenderar en VMM-server på den primära platsen och en på den sekundära platsen, som innehåller minst ett privat VMM-moln var. Servern måste köra minst System Center 2012 SP1 med de senaste uppdateringarna och vara ansluten till Internet. Moln ska vara konfigurerade med Hyper-V-kapacitetsprofilen. Du ska installera Azure Site Recovery-providern på VMM-servern. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Kommunikationen mellan providern och Azure är säker och krypterad.
* **Hyper-V-server**: Hyper-V-värdservrar ska finnas i de primära och sekundära VMM-molnen. Värdservrarna ska köra minst Windows Server 2012 med de senaste uppdateringarna installerade och vara anslutna till Internet. Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
* **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.

**Allmän arkitektur**

![Lokal till lokal](./media/site-recovery-components/arch-onprem-onprem.png)

* [Lär dig mer](site-recovery-vmm-to-vmm.md#azure-prerequisites) om distributionskraven på Azure-portalen.
* * [Lär dig mer](site-recovery-vmm-to-vmm-classic.md#before-you-start) om distributionskraven på den klassiska Azure-portalen.

## <a name="replicate-to-a-secondary-site-with-san-replication-hyperv-vms-managed-by-vmm"></a>Replikera till en sekundär plats med SAN-replikering: Virtuella Hyper-V-datorer som hanteras av VMM
Du kan replikera virtuella Hyper-V-datorer som hanteras i VMM-moln till en sekundär plats med hjälp av SAN-replikering på den klassiska Azure-portalen. Det här scenariot stöds inte för närvarande på den nya Azure-portalen.

I det här scenariot installerar du Azure Site Recovery-providern på VMM-servrarna under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Data replikeras mellan de primära och sekundära lagringsmatriserna med hjälp av synkron SAN-replikering.

Du behöver det här:

**Azure-konto**: Du behöver en Azure-prenumeration

* **SAN-matris**: En [SAN-matris som stöds](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) som hanteras av den primära VMM-servern. SAN-nätverket delar en nätverksinfrastruktur med en annan SAN-matris på den sekundära platsen.
* **VMM-server**: Vi rekommenderar en VMM-server på den primära platsen och en på den sekundära platsen, som innehåller minst ett privat VMM-moln var. Servern måste köra minst System Center 2012 SP1 med de senaste uppdateringarna och vara ansluten till Internet. Moln ska vara konfigurerade med Hyper-V-kapacitetsprofilen.
* **Hyper-V-server**: Hyper-V-värdservrar som finns i de primära och sekundära VMM-molnen. Värdservrarna ska köra minst Windows Server 2012 med de senaste uppdateringarna installerade och vara anslutna till Internet.
* **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.

**Arkitektur för SAN-replikering**

![SAN-replikering](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Lär dig mer](site-recovery-vmm-san.md#before-you-start) om distributionskraven.

### <a name="onpremises"></a>Lokal
## <a name="hyperv-protection-lifecycle"></a>Livscykel för Hyper-V-skydd
Det här arbetsflödet visar processen för att skydda, replikera och redundansväxla virtuella Hyper-V-datorer.

1. **Aktivera skydd**: Du konfigurerar Site Recovery-valvet, konfigurerar replikeringsinställningar för ett VMM-moln eller en Hyper-V-plats och aktiverar skydd för virtuella datorer. Jobbet **Aktivera skydd** initieras och kan övervakas på fliken **Jobb**. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering till Azure med de inställningar som du har konfigurerat. Jobbet **Aktivera skydd** anropar också metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig replikering av virtuella datorer.
2. **Inledande replikering**: En ögonblicksbild av den virtuella datorn tas och de virtuella hårddiskarna replikeras en i taget tills alla har kopierats till Azure eller till det sekundära datacentret. Hur lång tid det tar att slutföra detta beror på storleken på de virtuella datorerna, nätverkets bandbredd och metoden för inledande replikering. Om diskändringar inträffar under en pågående inledande replikering spåras dessa ändringar av spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl) som finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till den sekundära lagringsplatsen. Observera att ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår. När den inledande replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och diskförändringarna (delta) i loggen synkroniseras och sammanfogas.
3. **Slutför skydd**: När replikeringen har slutförts konfigurerar jobbet **Slutför skydd** nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad. Om du replikerar till Azure kan du behöva justera inställningarna för den virtuella datorn så att den är redo för redundans. Nu kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.
4. **Replikering**: Efter den första replikeringen startar deltasynkroniseringen baserat på replikeringsinställningarna.
   * **Replikeringsfel**: Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så görs en omsynkronisering. Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering. Omsynkroniseringen minimerar mängden data som skickas genom att beräkna kontrollsummor för de virtuella käll- och måldatorerna och bara skicka förändringar (delta). När omsynkroniseringen är klar återupptas deltareplikeringen. Omsynkroniseringen schemaläggs som standard för automatisk körning utanför kontorstid, men du kan synkronisera om en virtuell dator manuellt.
   * **Replikeringsfel**: Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Om det är ett oåterkalleligt fel, till exempel ett autentiserings- eller auktoriseringsfel, eller en replikdator som har ett ogiltigt tillstånd, så görs inget nytt försök. Om det är ett återkalleligt fel, till exempel ett nätverksfel eller ont om ledigt utrymme/minne, så görs ett nytt försök med längre och längre intervall mellan försöken (varje minut, varannan minut, var 4:e, var 8:e, var 10:e och sedan var 30: e minut).
5. **Planerad/oplanerad redundans**: Du kan köra planerade eller oplanerade redundansväxlingar efter behov. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. När de virtuella replikdatorerna har skapats försätts de i ett tillstånd där de väntar på bekräftelse. Du måste bekräfta dem för att slutföra redundansväxlingen, såvida du inte replikerar med SAN då detta sker automatiskt. När den primära platsen är igång kan återställning efter fel göras. Om du har replikerat till Azure sker den omvända replikeringen automatiskt. Annars startar du den omvända replikeringen manuellt.

![arbetsflöde](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Nästa steg
[Förbereda för distribution](site-recovery-best-practices.md)




<!--HONumber=Nov16_HO2-->


