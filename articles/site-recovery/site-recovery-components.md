<properties
    pageTitle="Hur fungerar Site Recovery? | Microsoft Azure"
    description="Den här artikeln innehåller en översikt över arkitekturen i Site Recovery"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/27/2016"
    ms.author="raynew"/>

# Hur fungerar Azure Site Recovery?

Den här artikeln beskriver den underliggande arkitekturen i tjänsten Azure Site Recovery och de komponenter som gör att den fungerar. 

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Översikt

Organisationer behöver en BCDR-strategi för affärskontinuitet och haveriberedskap som definierar hur appar, arbetsbelastningar och data är tillgängliga under planerade och oplanerade avbrott och hur de kan återställas till normalt drifttillstånd så fort som möjligt.

Site Recovery är en Azure-tjänst som stödjer din BCDR-strategi genom att samordna replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till en sekundär plats. Om det uppstår driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den återgår till normal drift.

Site Recovery kan distribueras för att samordna replikeringen i ett antal scenarier:

- **Replikera virtuella VMware-datorer **: Du kan replikera lokala virtuella VMware-datorer till [Azure](site-recovery-vmware-to-azure-classic.md) eller till ett [sekundärt datacenter](site-recovery-vmware-to-vmware.md).
- **Replikera fysiska datorer**: Du kan replikera fysiska datorer som kör Windows eller Linux till [Azure](site-recovery-vmware-to-azure-classic.md) eller till ett [sekundärt datacenter](site-recovery-vmware-to-vmware.md).
- **Replikera virtuella Hyper-V-datorer som hanteras i System Center VMM-moln**: Du kan replikera lokala virtuella Hyper-V-datorer i VMM-moln till [Azure](site-recovery-vmm-to-azure.md) eller till ett [sekundärt datacenter](site-recovery-vmm-to-vmm.md). 
- **Replikera virtuella Hyper-V-datorer (utan VMM)**: Du kan replikera virtuella Hyper-V-datorer som inte hanteras av VMM till [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Migrera virtuella datorer**: Du kan använda Site Recovery för att [migrera virtuella IaaS-datorer i Azure](site-recovery-migrate-azure-to-azure.md) mellan regioner eller för att [migrera Windows-instanser i AWS](site-recovery-migrate-aws-to-azure.md) till virtuella IaaS-datorer i Azure. För närvarande stöds endast migrering vilket innebär att du kan redundansväxla dessa virtuella datorer, men inte växla tillbaka (återställa) dem.

Site Recovery kan replikera de flesta appar som körs på dessa virtuella datorer och fysiska servrar. En fullständig sammanfattning av de appar som stöds finns i [Vilka arbetsbelastningar kan Azure Site Recovery skydda?](site-recovery-workload.md)

## Replikera lokala virtuella VMware-datorer/fysiska VMware-servrar till Azure

Det finns för närvarande två olika arkitekturer för replikering av virtuella VMware-datorer eller fysiska Windows-/Linux-baserade VMware-servrar till Azure:

- [Äldre arkitektur](site-recovery-vmware-to-azure-classic-legacy.md): Den här arkitekturen bör inte användas för nya distributioner. 
- [Förbättrad arkitektur](site-recovery-vmware-to-azure-classic.md): Det här är den senaste arkitekturen och bör användas för alla nya distributioner. Om du redan har distribuerat det här scenariot med den äldre arkitekturen rekommenderar vi att du [lär dig mer om migrering](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) till den förbättrade distributionen.

I den utökade distributionen måste du konfigurera en lokal hanteringsserver med alla Site Recovery-komponenter. Du distribuerar mobilitetstjänsten automatiskt (eller installerar den manuellt) på varje dator som du vill skydda. Efter den första replikeringen skickar mobilitetstjänsten på datorn förändringsdata (delta) för replikeringen till processervern, som optimerar dem innan de skickas till Azure-lagring.

![Förbättrad](./media/site-recovery-components/arch-enhanced.png)
![Förbättrad](./media/site-recovery-components/arch-enhanced2.png)

### Lokal
Det här behöver du lokalt:

- **Hanteringsserver**: Du behöver en Windows Server 2012 R2-dator som fungerar som hanteringsservern. På den här servern installerar du alla dessa Site Recovery-komponenter med en enda installationsfil:

    - **Konfigurationsserverkomponent**: Samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering och återställning.
    - **Processerverkomponent**: Fungerar som en replikerings-gateway. Den tar emot replikeringsdata från skyddade källdatorer, optimerar dem med cachelagring, komprimering och kryptering och skickar data till Azure-lagring. Den hanterar också push-installationen av mobilitetstjänsten till skyddade datorer och utför automatisk identifiering av virtuella VMware-datorer. Allt eftersom distributionen växer kan du lägga till ytterligare separata dedikerade servrar för att hantera allt större mängder replikeringstrafik.
    - **Huvudmålserverkomponent**: Hanterar replikeringsdata vid återställning från Azure. 
- **ESX-/ESXi-värdar och vCenter-server för VMware**: Du behöver en eller flera ESX-/ESXi-värdservrar som kör virtuella VMware-datorer. Vi rekommenderar att du distribuerar en vCenter-server för att hantera dessa värdar. **Obs!** **Även om du replikerar fysiska servrar måste du växla tillbaka dem till VMware**. När du replikerar en fysisk server körs den som en virtuell Azure-dator när du redundansväxlar till Azure. Den växlar tillbaka lokalt som en lokal virtuell VMware-dator. 
    
- **Virtuella datorer/fysiska servrar**: Mobilitetstjänsten måste vara installerad på alla datorer som du vill replikera till Azure. Den här tjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till processervern. Den här komponenten kan installeras manuellt eller kan flyttas och installerats automatiskt av processervern när du aktiverar replikering för en dator.

### Azure

Det här behöver du i Azure-infrastrukturen:     - **Azure-konto**: Du behöver ett Microsoft Azure-konto.
    - **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen. 
    - **Azure-nätverk**: Du behöver ett virtuellt Azure-nätverk som de virtuella Azure-datorer som skapas ska ansluta till efter redundansväxlingen. 
    
    
### Återställning efter fel

Återställning till det lokala systemet sker alltid till virtuella VMware-datorer, även om du redundansväxlade en fysisk server. Du behöver det här:

- **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure efter en redundansväxling måste du konfigurera en Azure-dator som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
- **VPN-anslutning**: För återställning efter fel behöver du en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
- **Separat lokal huvudmålserver**: Den lokala huvudmålservern hanterar återställning efter fel. Huvudmålservern installeras som standard på hanteringsservern, men om du växlar tillbaka större volymer trafik bör du konfigurera en separat lokal huvudmålserver för detta ändamål. 

![Förbättrad återställning efter fel](./media/site-recovery-components/enhanced-failback.png)

[Läs mer](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) om kraven för förbättrad distribution.
[Läs mer](site-recovery-failback-azure-to-vmware-classic.md) om återställning efter fel för en förbättrad distribution.




## Replikera virtuella Hyper-V-datorer i VMM-moln till Azure

Om du vill distribuera det här scenariot installerar du Azure Site Recovery-providern på VMM-servern under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Azure Recovery Services-agenten installeras under distributionen av Site Recovery på Hyper-V-värdservern och data replikeras mellan agenten och Azure-lagring över HTTPS-port 443. Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.

- Lokalt: 
    - **VMM-server**: Minst en VMM-server med minst ett privat VMM-moln. Servern ska köras på System Center 2012 R2 och måste ha en Internetanslutning. Om du vill vara säker på att virtuella datorer i Azure ansluter till ett nätverk efter en redundansväxling måste du konfigurera nätverksmappning. Det gör du genom att ansluta dina virtuella källdatorer till ett virtuellt datornätverk för VMM. Det virtuella datornätverket bör vara kopplat till ett logiskt nätverk som är associerat med molnet.
    - **Hyper-V-server**: Minst en Hyper-V-värdserver i VMM-molnet. Hyper-V-värdar måste köra Windows Server 2012 R2.
    - **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.
    
- Azure: 
    - **Azure-konto**: Du behöver ett Microsoft Azure-konto.
    - **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.
    - **Azure-nätverk**: Om du vill konfigurera nätverksmappning så att virtuella datorer i Azure ansluter till nätverk efter en redundansväxling måste du konfigurera ett Azure-nätverk.

    ![VMM till Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Mer information om exakta [distributionskrav](site-recovery-vmm-to-azure.md#before-you-start).

## Replikera virtuella VMware-datorer eller fysiska VMware-servrar till en sekundär plats

Om du vill replikera virtuella VMware-datorer eller Windows-/Linux-baserade fysiska VMware-servrar till en sekundär plats laddar du ned InMage Scout som ingår i Azure Site Recovery-prenumerationen. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera. Efter den första replikeringen skickar agenten på varje dator replikeringsförändringar (delta) till processervern. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)

### Lokal primär plats

- **Processerver**: Konfigurera processserverkomponenten på din primära plats så att den hanterar cachelagring, komprimering och dataoptimering. Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda. 
- **VMware-baserad ESX/ESXi och vCenter-server**: Om du skyddar virtuella datorer i VMware behöver du en VMware-baserad EXS-/ESXi-hypervisor och eventuellt en VMware-baserad vCenter-server för att hantera hypervisorer.
- **Virtuella datorer/fysiska servrar**: Unified Agent måste vara installerat på virtuella VMwares-datorer eller Windows-/Linux-baserade fysiska servrar som du vill skydda. Unified Agent installeras även på de datorer som fungerar som huvudmålservrar. Agenten fungerar som en kommunikationsprovider mellan alla komponenter. 
    
### Lokal sekundär plats
 
- **Konfigurationsserver**: Konfigurationsservern är den första komponenten som du installerar och installeras på den sekundära platsen för att hantera, konfigurera och övervaka distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen. Det finns bara en konfigurationsserver i en distribution och den måste installeras på en dator som kör Windows Server 2012 R2.
- **vContinuum-server**: Den installeras på samma plats (den sekundära platsen) som konfigurationsservern. Den innehåller en konsol för att hantera och övervaka din skyddade miljö. I en standardinstallation är vContinuum-servern den första huvudmålservern och har Unified Agent installerad.
- **Huvudmålserver**: Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna. Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar. Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. 

### Azure

Du kan distribuera det här scenariot med InMage Scout. InMage Scout ingår i Azure-prenumerationer. När du har skapat ett Site Recovery-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.


## Replikera virtuella Hyper-V-datorer till Azure (utan VMM)

Om du vill replikera virtuella Hyper-V-datorer som inte hanteras i VMM-moln till Azure kan du installera Azure Site Recovery-providern och Azure Recovery Services-agenten på Hyper-V-värden under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Agenten hanterar replikeringsdata över HTTPS-port 443. Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.

![Hyper-V-plats till Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### Lokal

- **Hyper-V-server**: Minst en Hyper-V-värdservern. Hyper-V-värdar måste köra Windows Server 2012 R2.
- **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.
    
### Azure

- **Azure-konto**: Du behöver ett Microsoft Azure-konto.
- **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.

[Lär dig mer](site-recovery-hyper-v-site-to-azure.md#before-you-start) om distributionskraven.


## Replikera virtuella Hyper-V-datorer i VMM-moln till Azure

Du distribuerar det här scenariot genom att installera Azure Site Recovery-providern på VMM-servern och Azure Recovery Services-agenten på Hyper-V-värden under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Agenten hanterar replikeringsdata över HTTPS-port 443. Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring (vilande) är också krypterade.

![VMM till Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### Lokal

- **VMM-server**: Minst en VMM-server med minst ett privat VMM-moln. Servern ska köras på System Center 2012 R2 och måste ha en Internetanslutning. Om du vill vara säker på att virtuella datorer i Azure ansluter till ett nätverk efter en redundansväxling måste du konfigurera nätverksmappning. Det gör du genom att ansluta virtuella källdatorer till ett VMM-baserat virtuellt datornätverk. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
- **Hyper-V-server**: Minst en Hyper-V-värdserver i VMM-molnet. Hyper-V-värdar måste köra Windows Server 2012 R2.
- **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.
    
### Azure

- **Azure-konto**: Du behöver ett Microsoft Azure-konto.
- **Azure-lagring**: Du behöver ett Azure-lagringskonto för att lagra replikerade data. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen.
- **Azure-nätverk**: Om du vill vara säker på att virtuella datorer i Azure ansluter till nätverk efter en redundansväxling måste du konfigurera nätverksmappning. För att åstadkomma detta behöver du ett Azure-nätverk.

[Lär dig mer](site-recovery-vmm-to-azure.md#before-you-start) om distributionskraven.

## Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter

Om du vill distribuera det här scenariot installerar du Azure Site Recovery-providern på VMM-servern under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering. Kommunikationen både från providern och mellan Hyper-V-värdservrar är säker och krypterad. 

![Lokal till lokal](./media/site-recovery-components/arch-onprem-onprem.png)

### Lokal

- **VMM-server**: Vi rekommenderar en VMM-server på den primära platsen och en på den sekundära platsen, som innehåller minst ett privat VMM-moln var. Servern måste köra minst System Center 2012 SP1 med de senaste uppdateringarna och vara ansluten till Internet. Moln ska vara konfigurerade med Hyper-V-kapacitetsprofilen.
- **Hyper-V-server**: Hyper-V-värdservrar ska finnas i de primära och sekundära VMM-molnen. Värdservrarna ska köra minst Windows Server 2012 med de senaste uppdateringarna installerade och vara anslutna till Internet.
- **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.
    
### Azure

Du behöver en Azure-prenumeration.

[Lär dig mer](site-recovery-vmm-to-vmm.md#before-you-start) om distributionskraven.


## Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter med SAN-replikering

I det här scenariot installerar du Azure Site Recovery-providern på VMM-servrarna under distributionen av Site Recovery. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Data replikeras mellan de primära och sekundära lagringsmatriserna med hjälp av synkron SAN-replikering.

![SAN-replikering](./media/site-recovery-components/arch-onprem-onprem-san.png)

### Lokal

- **SAN-matris**: En [SAN-matris som stöds](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) som hanteras av den primära VMM-servern. SAN-nätverket delar en nätverksinfrastruktur med en annan SAN-matris på den sekundära platsen.
- **VMM-server**: Vi rekommenderar en VMM-server på den primära platsen och en på den sekundära platsen, som innehåller minst ett privat VMM-moln var. Servern måste köra minst System Center 2012 SP1 med de senaste uppdateringarna och vara ansluten till Internet. Moln ska vara konfigurerade med Hyper-V-kapacitetsprofilen.
- **Hyper-V-server**: Hyper-V-värdservrar som finns i de primära och sekundära VMM-molnen. Värdservrarna ska köra minst Windows Server 2012 med de senaste uppdateringarna installerade och vara anslutna till Internet.
- **Skyddade datorer**: Hyper-V-källservern måste ha minst en virtuell dator som du vill skydda.
    
### Azure

Du behöver en Azure-prenumeration.  

[Lär dig mer](site-recovery-vmm-san.md#before-you-start) om distributionskraven.


## Livscykel för Hyper-V-skydd

Det här arbetsflödet visar processen för att skydda, replikera och redundansväxla virtuella Hyper-V-datorer. 

1. **Aktivera skydd**: Du konfigurerar Site Recovery-valvet, konfigurerar replikeringsinställningar för ett VMM-moln eller en Hyper-V-plats och aktiverar skydd för virtuella datorer. Jobbet **Aktivera skydd** initieras och kan övervakas på fliken **Jobb**. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering till Azure med de inställningar som du har konfigurerat. Jobbet **Aktivera skydd** anropar också metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig replikering av virtuella datorer.
2. **Inledande replikering**: En ögonblicksbild av den virtuella datorn tas och de virtuella hårddiskarna replikeras en i taget tills alla har kopierats till Azure eller till det sekundära datacentret. Hur lång tid det tar att slutföra detta beror på storleken på de virtuella datorerna, nätverkets bandbredd och metoden för inledande replikering. Om diskändringar inträffar under en pågående inledande replikering spåras dessa ändringar av spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl) som finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till den sekundära lagringsplatsen. Observera att ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår. När den inledande replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och diskförändringarna (delta) i loggen synkroniseras och sammanfogas.
3. **Slutför skydd**: När replikeringen har slutförts konfigurerar jobbet **Slutför skydd** nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad. Om du replikerar till Azure kan du behöva justera inställningarna för den virtuella datorn så att den är redo för redundans. Nu kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.
4. **Replikering**: Efter den första replikeringen startar deltasynkroniseringen baserat på replikeringsinställningarna. 
    - **Replikeringsfel**: Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så görs en omsynkronisering. Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering. Omsynkroniseringen minimerar mängden data som skickas genom att beräkna kontrollsummor för de virtuella käll- och måldatorerna och bara skicka förändringar (delta). När omsynkroniseringen är klar återupptas deltareplikeringen. Omsynkroniseringen schemaläggs som standard för automatisk körning utanför kontorstid, men du kan synkronisera om en virtuell dator manuellt.
    - **Replikeringsfel**: Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Om det är ett oåterkalleligt fel, till exempel ett autentiserings- eller auktoriseringsfel, eller en replikdator som har ett ogiltigt tillstånd, så görs inget nytt försök. Om det är ett återkalleligt fel, till exempel ett nätverksfel eller ont om ledigt utrymme/minne, så görs ett nytt försök med längre och längre intervall mellan försöken (varje minut, varannan minut, var 4:e, var 8:e, var 10:e och sedan var 30: e minut).
4. **Planerad/oplanerad redundans**: Du kan köra planerade eller oplanerade redundansväxlingar efter behov. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. När de virtuella replikdatorerna har skapats försätts de i ett tillstånd där de väntar på bekräftelse. Du måste bekräfta dem för att slutföra redundansväxlingen, såvida du inte replikerar med SAN då detta sker automatiskt. När den primära platsen är igång kan återställning efter fel göras. Om du har replikerat till Azure sker den omvända replikeringen automatiskt. Annars startar du den omvända replikeringen manuellt.
 

![arbetsflöde](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Nästa steg

[Förbereda för distribution](site-recovery-best-practices.md)



<!--HONumber=Jun16_HO2-->


