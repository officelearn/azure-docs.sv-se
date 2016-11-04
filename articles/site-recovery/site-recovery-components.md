---
title: Hur fungerar Site Recovery? | Microsoft Docs
description: Den här artikeln innehåller en översikt över arkitekturen i Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="how-does-azure-site-recovery-work?"></a>Hur fungerar Azure Site Recovery?
Den här artikeln beskriver arkitekturen i tjänsten Azure Site Recovery och de komponenter som gör att den fungerar.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Översikt
Du behöver ha en strategi för affärskontinuitet och haveriberedskap (BCDR) som ser till att arbetsbelastningar och data körs och är tillgängliga under planerade såväl som oplanerade avbrott och att återgången till normala driftsförhållanden sker så snart som möjligt. 

Site Recovery är en Azure-tjänst som bidrar till din haveriberedskapsstrategi. Den arrangerar replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. En översikt finns i [Vad är Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery på Azure-portalen
Azure har två olika [distributionsmodeller](../resource-manager-deployment-model.md) för att skapa och arbeta med resurser: Azure Resource Manager-modellen och den klassiska tjänsthanteringsmodellen. Azure har också två portaler – den [klassiska Azure-portalen](https://manage.windowsazure.com/) som stöder den klassiska distributionsmodellen och [Azure-portalen](https://portal.azure.com) som stöder båda distributionsmodellerna.

* Site Recovery är tillgängligt både på den klassiska portalen och på Azure-portalen.
* På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen.
* På Azure-portalen kan du använda den klassiska modellen eller Resource Manager-distributioner. 

Informationen i den här artikeln gäller både klassiska distributioner och distributioner via Azure-portalen. Tabellen sammanfattar eventuella skillnader.

| **Replikera** | **Azure-portal** | **Klassisk portal** |
| --- | --- | --- |
| **Replikering av VMware från virtuell dator till Azure** |Förenklad distributionsprocess.<br/><br/> Redundansväxla virtuella datorer till klassisk eller Resource Manager-baserad lagring.<br/><br/> Replikera till klassisk eller Resource Manager-baserad lagring.<br/><br/> Använd klassiska nätverk eller Resource Manager-nätverk för att ansluta till virtuella Azure-datorer efter redundansväxling.<br/><br/> Använd LRS- eller GRS-lagring. |Redundansväxla endast till klassiska lagring.<br/><br/> Använd endast klassiska nätverk för att ansluta till virtuella datorer efter redundansväxling.<br/><br/> Använd GRS-lagring. |
| **Replikera virtuella Hyper-V-datorer (utan VMM) till Azure** |Förenklad distributionsprocess.<br/><br/> Redundansväxla virtuella datorer till klassisk eller Resource Manager-baserad lagring.<br/><br/> Replikera till klassisk eller Resource Manager-baserad lagring.<br/><br/> Använd klassiska nätverk eller Resource Manager-nätverk för att ansluta till virtuella Azure-datorer efter redundansväxling. | |
| **Replikera virtuella Hyper-V-datorer (med VMM) till Azure** |Förenklad distributionsprocess.<br/><br/> Redundansväxla virtuella datorer till klassisk eller Resource Manager-baserad lagring.<br/><br/> Replikera till klassisk eller Resource Manager-baserad lagring.<br/><br/> Använd klassiska nätverk eller Resource Manager-nätverk för att ansluta till virtuella Azure-datorer efter redundansväxling.<br/><br/> Ställ in nätverksmappning |Redundansväxla endast till klassiska lagring.<br/><br/> Använd endast klassiska nätverk för att ansluta till virtuella datorer efter redundansväxling. |
| **Replikera virtuella Hyper-V-datorer (med VMM) till sekundär sida** |Förenklad distributionsprocess.<br/><br/> Redundansväxla virtuella datorer till klassisk eller Resource Manager-baserad lagring.<br/><br/> Replikera till klassisk eller Resource Manager-baserad lagring.<br/><br/> Använd klassiska nätverk eller Resource Manager-nätverk för att ansluta till virtuella Azure-datorer efter redundansväxling.<br/><br/> Ställ in nätverksmappning |Redundansväxla endast till klassiska lagring.<br/><br/> Använd endast klassiska nätverk för att ansluta till virtuella datorer efter redundansväxling.<br/><br/> Du kan ställa in lagringsmappning. <br/><br/> SAN-replikering stöds inte. |

## <a name="deployment-scenarios"></a>Distributionsscenarier
Site Recovery kan distribueras för att samordna replikeringen i ett antal scenarier:

* **Replikera virtuella VMware-datorer**: Du kan replikera lokala virtuella VMware-datorer till Azure-lagring eller till ett sekundärt datacenter.
* **Replikera fysiska datorer**: Du kan replikera fysiska datorer som kör Windows eller Linux till Azure-lagring eller till ett sekundärt datacenter. Fysiska datorer replikeras på nästan samma sätt som virtuella VMware-datorer.
* **Replikera virtuella Hyper-V-datorer**: Om dina Hyper-V-värdar hanteras i System Center VMM-moln, kan du replikera lokala virtuella datorer till Azure eller till ett sekundärt datacenter. Om värdar inte hanteras av VMM kan du endast replikera till Azure. Du kan replikera virtuella Hyper-V-datorer som inte hanteras av VMM till Azure.
* **Migrera virtuella datorer**: Du kan använda Site Recovery för att [migrera virtuella IaaS-datorer i Azure](site-recovery-migrate-azure-to-azure.md) mellan regioner eller för att [migrera Windows-instanser i AWS](site-recovery-migrate-aws-to-azure.md) till virtuella IaaS-datorer i Azure. Fullständig replikering stöds för närvarande inte. Du kan replikera för migrering (redundansväxling), men du kan inte återställa. 

Site Recovery kan replikera de flesta appar som körs på dessa virtuella datorer och fysiska servrar. En fullständig sammanfattning av de appar som stöds finns i [Vilka arbetsbelastningar kan Azure Site Recovery skydda?](site-recovery-workload.md)

## <a name="replicate-vmware-virtual-machines-to-azure"></a>Replikera virtuella VMware-datorer i Azure
![Optimerad](./media/site-recovery-components/arch-enhanced.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto.<br/><br/> **Lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Du kan använda ett klassiskt konto eller ett Resource Manager-lagringskonto. Kontot kan vara LRS eller GRS när du distribuerar på Azure-portalen. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.<br/><br/> **Nätverk**: Du behöver ett virtuellt Azure-nätverk som Azures virtuella datorer ska ansluta till när de skapas vid en redundansväxling. På Azure-portalen kan nätverk skapas i den klassiska tjänsthanteringsmodellen eller Resource Manager-modellen. |
| **Lokal konfigurationsserver** |Du behöver en lokal Windows Server 2012 R2-dator som kör konfigurationsservern och andra Site Recovery-komponenter.<br/><br/> Detta ska vara en virtuell VMware-dator med hög tillgänglighet.<br/><br/> Komponenterna på servern inkluderar:<br/><br/> **Konfigurationsserver**: Samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering och återställning.<br/><br/> **Processerver**: Fungerar som en replikerings-gateway. Den tar emot replikeringsdata från skyddade källdatorer, optimerar dem med cachelagring, komprimering och kryptering och skickar data till Azure-lagring. Den hanterar också push-installationen av mobilitetstjänsten till skyddade datorer och utför automatisk identifiering av virtuella VMware-datorer. Allt eftersom distributionen växer kan du lägga till ytterligare separata dedikerade servrar för att hantera allt större mängder replikeringstrafik.<br/><br/> **Huvudmålserver**: Hanterar replikeringsdata vid återställning från Azure. |
| **Lokala virtualiseringsservrar** |En eller flera vSphere-värdar. Vi rekommenderar även att en vCenter-server hanterar värdarna. |
| **Virtuella datorer för replikering** |Alla virtuella VMware-datorer som du vill replikera till Azure måste ha mobilitetstjänsten installerad. Den här tjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern. Den här komponenten kan installeras manuellt eller kan flyttas och installeras automatiskt av processervern när du aktiverar replikering för en dator. |

* [Lär dig mer](site-recovery-vmware-to-azure.md#azure-prerequisites) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-failback-azure-to-vmware.md) om redundans på Azure-portalen.

## <a name="replicate-physical-servers-to-azure"></a>Replikera fysiska servrar till Azure
![Optimerad](./media/site-recovery-components/arch-enhanced.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto.<br/><br/> **Lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Du kan använda ett klassiskt konto eller ett Resource Manager-lagringskonto. Kontot kan vara LRS eller GRS när du distribuerar på Azure-portalen. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.<br/><br/> **Nätverk**: Du behöver ett virtuellt Azure-nätverk som Azures virtuella datorer ska ansluta till när de skapas vid en redundansväxling. På Azure-portalen kan nätverk skapas i den klassiska tjänsthanteringsmodellen eller Resource Manager-modellen. |
| **Lokal konfigurationsserver** |Du behöver en lokal Windows Server 2012 R2-dator som kör konfigurationsservern och andra Site Recovery-komponenter.<br/><br/> Servern kan vara virtuell eller fysisk.<br/><br/> Komponenterna på servern inkluderar:<br/><br/> **Konfigurationsserver**: Samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering och återställning.<br/><br/> **Processerver**: Fungerar som en replikerings-gateway. Den tar emot replikeringsdata från skyddade källdatorer, optimerar dem med cachelagring, komprimering och kryptering och skickar data till Azure-lagring. Den hanterar också push-installationen av mobilitetstjänsten till skyddade datorer och utför automatisk identifiering av virtuella VMware-datorer. Allt eftersom distributionen växer kan du lägga till ytterligare separata dedikerade servrar för att hantera allt större mängder replikeringstrafik.<br/><br/> **Huvudmålserver**: Hanterar replikeringsdata vid återställning från Azure. |
| **Datorer för replikering** |Alla fysiska Windows/Linux-datorer som du vill replikera till Azure måste ha mobilitetstjänsten installerad. Den här tjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern. Den här komponenten kan installeras manuellt eller kan flyttas och installeras automatiskt av processervern när du aktiverar replikering för en dator. |
| **Återställning efter fel** |Redundansväxling från fysisk-till-fysisk stöds inte. Det betyder att om du växlar över fysiska servrar till Azure och sedan vill växla tillbaka dem så måste du växla tillbaka till en virtuell VMware-dator. Du kan inte växla tillbaka till en fysisk server. Du behöver en virtuell Azure-dator att växla över till, och om du inte distribuerade konfigurationsservern som en virtuell VMware-dator måste du konfigurera en separat huvudmålserver som en virtuell VMware-dator. Detta är nödvändigt eftersom huvudmålservern interagerar med och ansluter till VMware-lagring för att återställa diskarna till en virtuell VMware-dator. |

* [Lär dig mer](site-recovery-vmware-to-azure.md#azure-prerequisites) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-failback-azure-to-vmware.md) om redundans på Azure-portalen.

## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Replikera virtuella Hyper-V-datorer som inte hanteras av VMM till Azure
![Hyper-V-plats till Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto.<br/><br/> **Lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Du kan använda ett klassiskt konto eller ett Resource Manager-lagringskonto. Kontot måste vara GRS. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.<br/><br/> **Nätverk**: Du behöver ett virtuellt Azure-nätverk som Azures virtuella datorer ska ansluta till när de skapas vid en redundansväxling. |
| **Hyper-V-värdar/virtuella datorer** |En eller flera Hyper-V-värdar som kör en eller flera virtuella datorer.<br/><br/> Site Recovery-providern och Recovery Services-agenten är installerade på varje värd under distributionen. |

* [Lär dig mer](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) om kraven för distributioner på den klassiska portalen.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Replikera virtuella Hyper-V-datorer som hanteras av VMM till Azure
![VMM till Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto.<br/><br/> **Lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Du kan använda ett klassiskt konto eller ett Resource Manager-lagringskonto. Kontot måste vara GRS. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.<br/><br/> **Nätverk**: Du behöver ett virtuellt Azure-nätverk som Azures virtuella datorer ska ansluta till när de skapas vid en redundansväxling. |
| **VMM-server** |Du behöver en eller flera lokala VMM-servrar, med ett eller flera privata moln. Site Recovery-providern är installerad på varje server under distributionen. |
| **Hyper-V-värdar/virtuella datorer** |En eller flera Hyper-V-värdar som kör en eller flera virtuella datorer.<br/><br/> Recovery Services-agenten är installerad på varje värd under distributionen. |

* [Lär dig mer](site-recovery-vmm-to-azure.md#azure-requirements) om kraven för distributioner på Azure-portalen.
* [Lär dig mer](site-recovery-vmm-to-azure-classic.md#before-you-start) om kraven för distributioner på den klassiska portalen.

## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>Replikera virtuella VMware-datorer eller fysiska VMware-servrar till en sekundär plats
![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du distribuerar det här scenariot med InMage Scout. Du behöver ett Azure-konto för att få tillgång till detta.<br/><br/> När du har skapat ett Site Recovery-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen. |
| **Primär plats** |**Processerver**: Konfigurera processserverkomponenten på din primära plats så att den hanterar cachelagring, komprimering och dataoptimering. Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.<br/><br/> **VMware ESX/ESXi och vCenter**: Du behöver en VMware EXS/ESXi-hypervisor och eventuellt en VMware vCenter-server.<br/><br/> **Virtuella datorer** |
| **Sekundär plats** |**Konfigurationsserver**: Konfigurationsservern är den första komponenten som du installerar och installeras på den sekundära platsen för att hantera, konfigurera och övervaka distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen. Det finns bara en konfigurationsserver i en distribution och den måste installeras på en dator som kör Windows Server 2012 R2.<br/><br/> **vContinuum-server (sekundär plats)**: Den installeras på samma plats som konfigurationsservern. Den innehåller en konsol för att hantera och övervaka din skyddade miljö. I en standardinstallation är vContinuum-servern den första huvudmålservern och har Unified Agent installerad.<br/><br/> **Huvudmålserver**: Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna. Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar. Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. |

Om du vill replikera virtuella VMware-datorer eller fysiska servrar till en sekundär plats laddar du ned InMage Scout som ingår i Azure Site Recovery-prenumerationen. Du kan ladda ned verktyget från Azure-portalen eller från den klassiska Azure-portalen.

Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>Replikera virtuella Hyper-V- datorer som hanteras av VMM till en sekundär plats
![Lokal till lokal](./media/site-recovery-components/arch-onprem-onprem.png)

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto. |
| **VMM-server** |Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen. Varje server måste ha ett eller flera privata moln.<br/><br/> Under distributionen installerar du Azure Site Recovery-providern på VMM-servern. |
| **Hyper-V-värdar/virtuella datorer** |En eller flera Hyper-V-värdar som körs i VMM-molnen på den primära och den sekundära platsen<br/><br/> Varje värd ska ha en eller flera virtuella datorer att replikera.<br/><br/>. Recovery Services-agenten är installerad på varje värd under distributionen. |

* [Lär dig mer](site-recovery-vmm-to-vmm.md#azure-prerequisites) om distributionskraven på Azure-portalen.
* [Lär dig mer](site-recovery-vmm-to-vmm-classic.md#before-you-start) om distributionskraven på den klassiska Azure-portalen.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>Replikera virtuella Hyper-V-datorer som hanteras av VMM till en sekundär plats genom SAN-replikering
![SAN-replikering](./media/site-recovery-components/arch-onprem-onprem-san.png)

Du kan replikera virtuella Hyper-V-datorer som hanteras i VMM-moln till en sekundär plats med hjälp av SAN-replikering på den klassiska portalen. Det här scenariot stöds inte för närvarande på Azure-portalen.

| **Komponent** | **Detaljer** |
| --- | --- |
| **Azure** |**Konto**: Du behöver ett Azure-konto. |
| **VMM-server** |Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen. Varje server måste ha ett eller flera privata moln.<br/><br/> Under distributionen installerar du Azure Site Recovery-providern på VMM-servern. |
| **SAN** |En SAN-matris som stöds, som hanteras av den primära VMM-servern.<br/><br/> SAN-nätverket ska dela en nätverksinfrastruktur med en annan SAN-matris på den sekundära platsen. |
| **Hyper-V-värdar/virtuella datorer** |En eller flera Hyper-V-värdar som körs i VMM-molnen på den primära och den sekundära platsen<br/><br/> Varje värd ska ha en eller flera virtuella datorer att replikera.<br/><br/>. Recovery Services-agenten är installerad på varje värd under distributionen. |

I det här scenariot installerar du Azure Site Recovery-providern på VMM-servrarna under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Data replikeras mellan de primära och sekundära lagringsmatriserna med hjälp av synkron SAN-replikering.

[Lär dig mer](site-recovery-vmm-san.md#before-you-start) om distributionskraven.

## <a name="hyper-v-protection-lifecycle"></a>Livscykel för Hyper-V-skydd
Det här arbetsflödet visar processen för att skydda, replikera och redundansväxla virtuella Hyper-V-datorer.

1. **Aktivera skydd**: Du konfigurerar Site Recovery-valvet, konfigurerar replikeringsinställningar för ett VMM-moln eller en Hyper-V-plats och aktiverar skydd för virtuella datorer. Jobbet **Aktivera skydd** initieras och kan övervakas på fliken **Jobb**. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering till Azure med de inställningar som du har konfigurerat. Jobbet **Aktivera skydd** anropar också metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig replikering av virtuella datorer.
2. **Inledande replikering**: En ögonblicksbild av den virtuella datorn tas och de virtuella hårddiskarna replikeras en i taget tills alla har kopierats till Azure eller till det sekundära datacentret. Hur lång tid det tar att slutföra detta beror på storleken på de virtuella datorerna, nätverkets bandbredd och metoden för inledande replikering. Om diskändringar inträffar under en pågående inledande replikering spåras dessa ändringar av spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl) som finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till den sekundära lagringsplatsen. Observera att ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår. När den inledande replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och diskförändringarna (delta) i loggen synkroniseras och sammanfogas.
3. **Slutför skydd**: När replikeringen har slutförts konfigurerar jobbet **Slutför skydd** nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad. Om du replikerar till Azure kan du behöva justera inställningarna för den virtuella datorn så att den är redo för redundans. Nu kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.
4. **Replikering**: Efter den första replikeringen startar deltasynkroniseringen baserat på replikeringsinställningarna.
   * **Replikeringsfel**: Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så görs en omsynkronisering. Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering. Omsynkroniseringen minimerar mängden data som skickas genom att beräkna kontrollsummor för de virtuella käll- och måldatorerna och bara skicka förändringar (delta). När omsynkroniseringen är klar återupptas deltareplikeringen. Omsynkroniseringen schemaläggs som standard för automatisk körning utanför kontorstid, men du kan synkronisera om en virtuell dator manuellt.
   * **Replikeringsfel**: Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Om det är ett oåterkalleligt fel, till exempel ett autentiserings- eller auktoriseringsfel, eller en replikdator som har ett ogiltigt tillstånd, så görs inget nytt försök. Om det är ett korrigerbart fel, till exempel ett nätverksfel eller ont om ledigt utrymme/minne, så görs ett nytt försök med längre och längre intervall mellan försöken (varje minut, varannan minut, var 4:e, var 8:e, var 10:e och sedan var 30: e minut).
5. **Planerad/oplanerad redundans**: Du kan köra planerade eller oplanerade redundansväxlingar efter behov. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. När de virtuella replikdatorerna har skapats försätts de i ett tillstånd där de väntar på bekräftelse. Du måste bekräfta dem för att slutföra redundansväxlingen, såvida du inte replikerar med SAN då detta sker automatiskt. När den primära platsen är igång kan återställning efter fel göras. Om du har replikerat till Azure sker den omvända replikeringen automatiskt. Annars startar du den omvända replikeringen manuellt.

![arbetsflöde](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Nästa steg
[Förbereda för distribution](site-recovery-best-practices.md)

<!--HONumber=Oct16_HO3-->


