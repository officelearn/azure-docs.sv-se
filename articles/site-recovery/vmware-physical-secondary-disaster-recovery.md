---
title: Konfigurera haveriberedskap för virtuella VMware-datorer eller fysiska servrar till en sekundär plats med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för VMware-datorer eller Windows och Linux fysiska servrar till en sekundär plats med Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauarvd
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/01/2018
ms.author: raynew
ms.openlocfilehash: 94abdd30dc9cd279ab791541250787a111f80d30
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618996"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurera haveriberedskap för lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats

InMage Scout i [Azure Site Recovery](site-recovery-overview.md) ger i realtid replikering mellan lokala VMware-webbplatser. InMage Scout ingår i Azure Site Recovery service-prenumerationer.

## <a name="end-of-support-announcement"></a>Support upphör meddelande

Azure Site Recovery-scenario för replikering mellan den lokala VMware eller fysiska Datacenter når support upphör.

-   Från augusti 2018 scenariot inte kan konfigureras i Recovery Services-valvet och InMage Scout-programvaran kan inte hämtas från valvet. Befintliga distributioner kommer att stödjas. 
-   Scenariot från December 31 2020, kommer inte att stödas.
- Befintliga partners kan registrera nya kunder till scenariot tills support upphör.

Under 2018 och 2019 släpps två uppdateringar: 

-   Uppdatering 7: Korrigeringar nätverksproblem för konfiguration och efterlevnad och tillhandahåller stöd för TSL 1.2.
-   Uppdatera 8: Lägger till stöd för Linux-operativsystem RHEL/CentOS 7.3/7.4/7.5 och SUSE 12

Efter uppdatering 8, ingen ytterligare släpps uppdateringar. Det blir begränsad snabbkorrigering stöd för de operativsystem som har lagts till i uppdatering 8 och felkorrigeringar baserat på bästa prestanda.

Azure Site Recovery har fortfarande till innovationer genom att VMware och Hyper-V-kunder en sömlös och klassens bästa DRaaS-lösning med Azure som en plats för katastrofåterställning. Microsoft rekommenderar att befintliga InMage / ASR Scout kunder överväga att använda Azure Site Recovery-VMware till Azure-scenariot för kontinuitet för företag behöver. Azure Site Recovery-VMware till Azure-scenariot är en katastrofåterställningslösning i företagsklass för VMware-program, som erbjuder RPO och RTO minuter, stöd för flera virtuella datorer programmet replikering och återställning, sömlös integrering omfattande övervakning och stor TCO fördel.

### <a name="scenario-migration"></a>Scenario-migrering
Som ett alternativ rekommenderar vi att konfigurera haveriberedskap för lokala virtuella VMware-datorer och fysiska datorer genom att replikera dem till Azure. Gör detta på följande sätt:

1.  Granska snabb jämförelsen nedan. Innan du kan replikera lokala datorer, behöver du kontrollera att de uppfyller [krav](./vmware-physical-azure-support-matrix.md#replicated-machines) för replikering till Azure. Om du replikerar virtuella VMware-datorer, rekommenderar vi att du läser igenom [riktlinjerna för kapacitetsplanering](./site-recovery-plan-capacity-vmware.md), och kör den [planeringsverktyget](./site-recovery-deployment-planner.md) till kapacitetskrav för identitet, och kontrollera efterlevnad.
2.  När du har kört Kapacitetsplaneraren, du kan konfigurera replikering: o för virtuella VMware-datorer, följer du de här självstudierna att [förbereda Azure](./tutorial-prepare-azure.md), [förbereda lokala VMware-miljö](./vmware-azure-tutorial-prepare-on-premises.md), och [konfigurera haveriberedskap](./vmware-azure-tutorial-prepare-on-premises.md).
o för fysiska datorer, följ den här [självstudien](./physical-azure-disaster-recovery.md).
3.  När datorer replikeras till Azure, kan du köra en [programåterställningstest](./site-recovery-test-failover-to-azure.md) att kontrollera att allt fungerar som förväntat.

### <a name="quick-comparison"></a>Snabb jämförelse

**Funktion** | **Replikering till Azure** |**Replikering mellan datacenter i VMware**
--|--|--
**Nödvändiga komponenter** |Mobilitetstjänsten på replikerade datorer. Den lokala konfigurationsservern, processervern och huvudmålservern. Tillfällig processerver i Azure för återställning efter fel.|Mobilitetstjänsten Processervern, konfigurationsservern och Huvudmålservern
**Konfiguration och dirigering** |Recovery Services-valv i Azure portal | Med hjälp av vContinuum 
**Replikerade**|Disk (Windows och Linux) |Volym-Windows<br> Disk-Linux
**Delad klusterdisk**|Stöds inte|Stöds
**Dataomsättning begränsningar (genomsnitt)** |10 MB/s data per disk<br> Data för 25MB/s per virtuell dator<br> [Läs mer](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | Data > 10 MB/s per disk  <br> Data > 25 MB/s per virtuell dator
**Övervakning** |Från Azure-portalen|Från CX (konfigurationsserver)
**Stödmatris**| [Klicka här för information](./vmware-physical-azure-support-matrix.md)|[Ladda ned ASR Scout kompatibel matris](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien behöver du:

- [Granska](vmware-physical-secondary-support-matrix.md) kraven för stöd för alla komponenter.
- Se till att de datorer som du vill replikera följer [replikerade datorn support](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="create-a-vault"></a>Skapa ett valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad som ska replikera och var du vill replikera den till.

1. Klicka på **Site Recovery** > **förbereda infrastrukturen** > **skyddsmål**.
2. Välj **till återställningsplats** > **Ja, med VMware vSphere Hypervisor**. Klicka sedan på **OK**.
3. I **Scout installationsprogrammet**, ladda ned InMage Scout 8.0.1 GA-programvara och nyckel för tjänstregistrering. Filer för installationsprogrammet för alla komponenter som ingår i den hämtade ZIP-fil.

## <a name="download-and-install-component-updates"></a>Hämta och installera Komponentuppdateringar

 Granska och installera senast [uppdateringar](#updates). Uppdateringar ska installeras på servrar i följande ordning:

1. RX-server (om tillämpligt)
2. Konfigurationsservrar
3. Processervrar
4. Master Target-servrar
5. vContinuum-servrar
6. Källservern (både Windows och Linux-servrar)

Installera uppdateringar på följande sätt:

> [!NOTE]
>Alla Scout-komponenterna filversion uppdatering kanske inte är samma i update .zip-filen. Den äldre versionen betyda att det finns ingen ändring i komponenten sedan tidigare uppdateringen till den här uppdateringen.

Ladda ned den [uppdatera](https://aka.ms/asr-scout-update6) .zip-filen. Filen innehåller följande komponenter: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bitar för RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extrahera .zip-filer.
2. **RX server**: kopiera **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** till RX-servern och extrahera den. I den extrahera mappen kör **/Install**.
3. **Konfigurationsservern och processervern**: kopiera **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** till konfigurationsservern och processervern. Dubbelklicka för att köra den.<br>
4. **Windows-huvudmålservern**: Om du vill uppdatera enhetlig agenten, kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till servern. Dubbelklicka på den för att köra den. Samma enhetlig agentuppdatering gäller även för källservern. Om källan inte har uppdaterats till uppdatering 4, bör du uppdatera enhetlig agenten.
  Uppdateringen behöver inte tillämpa på Huvudmålservern mål förbereds med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det här är den nya GA installer med de senaste ändringarna.
5. **vContinuum-server**: kopiera **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** till servern.  Kontrollera att du har stängt guiden vContinuum. Dubbelklicka på filen för att köra den.
    Uppdateringen behöver inte tillämpa på Huvudmålet förberedd med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det här är den nya GA installer med de senaste ändringarna.
6. **Linux huvudmålserver**: Om du vill uppdatera enhetlig agenten, kopiera **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till huvudservern målserver och extrahera den. I den extrahera mappen kör **/Install**.
7. **Windows-källservern**: Om du vill uppdatera enhetlig agenten, kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till källservern. Dubbelklicka på filen för att köra den. 
    Du behöver inte installera uppdatering 5-agenten på källservern om den redan har uppdaterats till uppdatering 4 eller källagent installeras med installationsprogrammet för senaste grundläggande **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Linux-källservern**: kopiera motsvarande version av agentfilen enhetlig till Linux-servern för att uppdatera enhetlig agenten och extrahera den. I den extrahera mappen kör **/Install**.  Exempel: För RHEL 6.7 64-bitars server, kopiera **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till servern, och extrahera den. I den extrahera mappen kör **/Install**.

## <a name="enable-replication"></a>Aktivera replikering

1. Konfigurera replikering mellan källa och mål VMware-webbplatser.
2. Se följande dokument för mer information om installation, skydd och återställning:

   * [Viktig information](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitetsöversikten](https://aka.ms/asr-scout-cm)
   * [Användarhandbok](https://aka.ms/asr-scout-user-guide)
   * [Användarhandbok för RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Snabb installationsguide](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Uppdateringar

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 uppdatering 6 
Uppdaterad: Oktober 12 2017

Ladda ned [Scout uppdatering 6](https://aka.ms/asr-scout-update6).

Scout uppdatering 6 är en ackumulerad uppdatering. Den innehåller alla korrigeringar från uppdatering 1 för uppdatering 5 plus de nya korrigeringar och förbättringar som beskrivs nedan. 

#### <a name="new-platform-support"></a>Stöd för nya plattformar
* Stöd har lagts till för källa Windows Server 2016
* Stöd har lagts till för följande Linux-operativsystem:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Stöd har lagts till för VMware Center 6.5

> [!NOTE]
> * Grundläggande Unified Agent(UA) installationsprogrammet för Windows har uppdaterats till support för Windows Server 2016. Det nya installationsprogrammet **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Samma installationsprogrammet ska användas för alla Windows-version som stöds. 
> * Basera Windows vContinuum & Huvudmålet installer har uppdaterats till support för Windows Server 2016. Det nya installationsprogrammet **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Samma installationsprogrammet används för att distribuera Windows 2016 Master Target och Huvudmålserver för Windows 2012 R2.
> * Ladda ned GA-paketet från portalen, enligt beskrivningen i [skapa ett valv](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar
- Återställning efter fel skydd misslyckas för Linux VM med listan över diskar som ska replikeras är tom i slutet av konfiguration.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 uppdatering 5
Scout uppdatering 5 är en ackumulerad uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 4 och de nya ändringarna som beskrivs nedan.
- Korrigeringar från Site Recovery Scout uppdatering 4 för uppdatering 5 är avsedda för mål- och vContinuum huvudkomponenter.
- Om källservrar, huvudmålservern, konfiguration, process och RX servrar redan kör uppdatering 4, därefter tillämpa det endast på huvudmålservern. 

#### <a name="new-platform-support"></a>Stöd för nya plattformar
* SUSE Linux Enterprise Server 11 Service Pack-4(SP4)
* SLES 11 SP4 64 bitars **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA.zip**). Ladda ned GA-paketet från portalen, enligt beskrivningen i [skapa ett valv](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar

* Korrigeringar för ökad Windows-kluster stöder tillförlitlighet:
    * Fast – vissa av P2V-MSCS klusterdiskar blir RAW efter återställningen.
    * Fixed-P2V MSCS-kluster återställningen misslyckas på grund av ett matchningsfel för disk-beställning.
    * Fast - The MSCS-kluster att lägga till diskar misslyckas med ett diskfel storlek matchningsfel.
    * Fast tjänsten beredskapskontrollen för källan MSCS-kluster med RDM-LUN-mappningen utförs inte i storlek verifiering.
    * Fixed-enkel nod kluster skyddet fungerar inte på grund av ett matchningsfel SCSI-problem. 
    * Det går inte att fixed-nytt skydd av P2V-Windows-klusterserver om mål-klusterdiskar finns. 
    
* Åtgärdat: Vid återställning efter fel skydd om den markerade mallen målserver inte på samma ESXi-server som den skyddade källa datorn (under vanlig skydd), och sedan vContinuum hämtar fel huvudmålservern under återställning efter fel återställning och återställning åtgärden misslyckas.

> [!NOTE]
> * Korrigeringar för P2V-kluster gäller endast fysiska MSCS-kluster som nyligen skyddats med Site Recovery Scout uppdatering 5. Installera kluster-korrigeringar på skyddade P2V MSCS-kluster med äldre uppdateringar enligt uppgradera stegen som beskrivs i avsnittet 12 i den [viktig för Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Om samma uppsättning diskar är aktiva på alla noder i klustret vid tidpunkten för nytt skydd som de visades när skyddade från början, återanvänder befintliga måldiskarna endast i nytt skydd för fysiska MSCS-kluster. Om inte, använder du de manuella stegen i avsnittet 12 i [viktig för Site Recovery Scout](https://aka.ms/asr-scout-release-notes), för att flytta sida måldiskarna till rätt datastore-sökväg till återanvändning vid nytt skydd. Om du skyddar MSCS-kluster i P2V läge utan att följa Uppgraderingsstegen skapas en ny disk på ESXi-målservern. Du måste ta bort de gamla diskarna manuellt från databasen.
> * När en SLES11 eller SLES11 (med alla servicepack) källserver startas utan problem, sedan manuellt vill markera det **rot** disk replikering par för omsynkronisering. Det finns inget meddelande i CX-gränssnittet. Om du inte markerar disken rot för omsynkronisering, märker du problem med dataintegriteten.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 uppdatering 4
Scout uppdatering 4 är en ackumulerad uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 3 och de nya ändringarna som beskrivs nedan.

#### <a name="new-platform-support"></a>Stöd för nya plattformar

* Stöd har lagts till för vCenter-/ vSphere 6.0, 6.1 och 6.2
* Stöd har lagts till för de här Linux-operativsystem:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 och 7.2
  * CentOS 7.0, 7.1 och 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bitars **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** paketeras med grundläggande Scout GA-paketet **InMage_Scout_Standard_8.0.1 GA.zip**. Ladda ned Scout GA-paketet från portalen enligt beskrivningen i [skapa ett valv](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar

* Förbättrad avstängning hantering för följande Linux-operativsystem och klonar att förhindra oönskade omsynkroniseringen problem:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* För Linux är nu åtkomstbehörigheter för alla mappar i installationskatalogen för enhetlig agenten begränsade till den lokala användaren.
* På Windows, en korrigering av en tidsgräns uppnås problem som uppstod vid utfärdande av den vanliga distribuerade konsekvens bokmärken överbelastad på distribuerade program, till exempel SQL Server och SharePoint-kluster.
* En logg relaterade korrigering i grundläggande installationsprogrammet för configuration server.
* En nedladdningslänk till VMware vCLI 6.0 lades till i Windows huvudmålservern grundläggande installationsprogrammet.
* Ytterligare kontroller och loggar har lagts till, för nätverket konfigurationsändringar under redundans och disaster recovery-test.
* Lösa ett problem som orsakade bevarandeinformationen inte ska rapporteras till konfigurationsservern.  
* Fysiska kluster kan lösa ett problem som orsakade volymstorleksändring om du vill återställa i guiden vContinuum när minska storleken på källvolymen.
* Lösa ett problem för klustret skydd som misslyckades med felet: ”Det gick inte att hitta disksignaturen”, när klusterdisken är en PRDM-disk.
* En korrigering av en cxps transport server kraschar eller på grund av ett utanför det tillåtna intervallet-undantag.
* Servernamn och IP-adresskolumner är nu ändras i den **Push-Installation av** i guiden vContinuum.
* Förbättringar av RX-API:
  * De fem senaste tillgängliga vanliga konsekvensen pekar nu tillgängliga (endast garanterad taggar).
  * Kapacitet och ledigt utrymme informationen visas för alla skyddade enheter.
  * Tillstånd för Scout-drivrutinen på källservern är tillgänglig.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** grundläggande paketet innehåller:
    * Ett grundläggande installationsprogram för uppdaterade konfigurationen-server (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Ett grundläggande installationsprogram för Windows-huvudmålservern (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Använd nya konfigurationsservern och Windows huvudmålservern GA bits för alla nya installationer.
> * Uppdatering 4 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 uppdatering 3

Alla Site Recovery-uppdateringar är kumulativa. Update 3 innehåller alla korrigeringar från uppdatering 1 och uppdatering 2. Uppdatering 3 kan tillämpas direkt på 8.0.1 GA. Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats.

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar
Uppdatering 3 åtgärdar följande problem:

* Konfigurationsservern och RX inte är registrerad i valvet när de är bakom proxyn.
* Antalet timmar som mål för återställningspunkt (RPO) inte har nått uppdateras inte i hälsorapporten.
* Konfigurationsservern är inte synkroniseras med RX när information om ESX-maskinvara eller nätverksinformation, innehålla UTF-8-tecken.
* Windows Server 2008 R2-domänkontrollanter starta inte efter återställningen.
* Offlinesynkronisering fungerar inte som förväntat.
* Efter redundansväxling av virtuella datorer framsteg inte replikering par borttagning inom configuration server-konsolen under en längre tid. Användare kan inte slutföra återställningen eller återuppta åtgärder.
* Övergripande ögonblicksbild-aktiviteter med konsekvensjobbet har optimerats, minska programmet kopplas från, till exempel SQL Server-klienter.
* Konsekvens verktyget (VACP.exe) prestanda har förbättrats. Användning av minne krävs för att skapa ögonblicksbilder på Windows har minskats.
* Push-meddelandet installera kraschar när lösenordet är större än 16 tecken.
* vContinuum inte kontrollera och ange nya inloggningsuppgifter för vCenter, om autentiseringsuppgifterna ändras.
* Huvudmålservern Cachehanteraren (cachemgr) är inte på Linux, ladda ned filer från processervern. Detta resulterar i replikering par begränsning.
* När fysiska failover-kluster (MSCS) diskbeställning inte på samma sätt på alla noder, replikering har inte angetts för några av volymerna som klustret. Klustret måste att återaktivera skyddet för att dra nytta av den här snabbkorrigeringen.  
* SMTP-funktioner fungerar inte som förväntat när RX har uppgraderats från Scout 7.1 till Scout 8.0.1.
* Mer statistik har lagts till i loggen för återställningsåtgärd att spåra den tid det tar att slutföra den.
* Stöd har lagts till för Linux-operativsystem på källservern:
  * Red Hat Enterprise Linux (RHEL) 6 uppdatering 7
  * CentOS 6 uppdatering 7
* Konfigurationsservern och RX konsoler nu visa aviseringar för paret som gäller bitmappsläge.
* Följande säkerhetskorrigeringar har lagts till i RX:
    * Auktorisering kringgå via parametern manipulation: begränsad åtkomst till användare som inte är tillämpliga.
    * Förfalskning av begäran mellan webbplatser: sidan token konceptet implementerades och genererar slumpmässigt för varje sida. Det innebär att det finns en enda inloggning instans för samma användare och sidan uppdatering fungerar inte. I stället omdirigerar till instrumentpanelen.
    * Ladda upp skadliga filer: filerna är begränsade till specifika tillägg: z, aiff, asf, avi, bmp, csv, dokument, docx, FLA-, flv, gif, gz, gzip, jpeg, jpg, log, mid mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, RAM-minne, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml och zip.
    * Beständiga cross site scripting: indata verifieringar har lagts till.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 uppdatering 2 (uppdatering 03 15 december)

Korrigeringar i uppdatering 2 omfattar:

* **Konfigurationsservern**: problem som hindrade den 31 dagars kostnadsfri Avläsning av funktionen inte fungerar som förväntat när konfigurationsservern registrerades till Azure Site Recovery-valv.
* **Enhetlig agenten**: åtgärda ett problem i uppdatering 1 som resulterade i uppdateringen inte installeras på huvudmålservern, vid uppgradering från version 8.0 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 uppdatering 1
Uppdatering 1 innehåller följande felkorrigeringar och nya funktioner:

* kostnadsfria skydd per serverinstans 31 dagar. På så sätt kan du testa funktionen eller ställa in proof-of-concept.
* Alla åtgärder på servern, inklusive redundans och återställning efter fel, är kostnadsfria under de första 31 dagarna. Tiden startar när en server först skyddas med Site Recovery Scout. Från den 32: a dagen debiteras varje skyddad server enligt priset för standard-instans för Site Recovery-skydd till en plats som ägs av kunden.
* När som helst, antalet skyddade servrar för närvarande debiteras är tillgänglig på den **instrumentpanelen** i valvet.
* Stöd har lagts till för vSphere-kommandoradsgränssnittet (vCLI) 5.5 uppdatering 2.
* Stöd har lagts till för de här Linux-operativsystem på källservern:
    * RHEL 6 uppdatering 6
    * RHEL 5 uppdatering 11
    * CentOS 6 uppdatering 6
    * CentOS 5 uppdatering 11
* Felkorrigeringar för att åtgärda följande problem:
  * Valvet registreringen misslyckas för konfigurationsservern eller RX-server.
  * Klustervolymer visas inte som förväntat när klustrade virtuella datorerna åter skyddats som de återupptas.
  * Det går inte att återställning efter fel när huvudmålservern finns på en annan ESXi-server från produktionen för lokala virtuella datorer.
  * Filen konfigurationsbehörighet ändras när du uppgraderar till 8.0.1. Den här ändringen påverkar skydd och åtgärder.
  * Tröskelvärde för omsynkronisering är inte aktiv som förväntat, orsakar inkonsekvent replikering beteende.
  * RPO-inställningar visas inte korrekt i konsolen för configuration server. Värdet för okomprimerade data visar felaktigt det komprimerade värdet.
  * Borttagningsåtgärden ta bort inte som förväntat i guiden vContinuum och replikering tas bort inte från konsolen configuration server.
  * I guiden vContinuum disken är omarkerade automatiskt när du klickar på **information** i vyn disken vid skydd av MSCS virtuella datorer.
  * Fysisk till virtuell (P2V) för scenariot flyttas nödvändiga HP-tjänster (till exempel CIMnotify och CqMgHost) inte till manuell i VM-återställning. Det här problemet resulterar i ytterligare starttiden.
  * Linux VM-skyddet fungerar inte när det finns fler än 26 diskar på huvudmålservern.

