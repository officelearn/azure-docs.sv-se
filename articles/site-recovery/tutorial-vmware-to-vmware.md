---
title: "Ställ in återställning av virtuella VMware-datorer eller fysiska servrar till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in återställning av virtuella VMware-datorer, eller Windows och Linux fysiska servrar till en sekundär plats med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauarvd
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: raynew
ms.openlocfilehash: 17bd7f424117842fd0687ba8a5fcf4d83c96a0bb
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Ställ in återställning av lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats

InMage Scout i [Azure Site Recovery](site-recovery-overview.md) erbjuder realtid replikering mellan lokala platser för VMware. InMage Scout ingår i Azure Site Recovery-tjänsten prenumerationer. 


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- [Granska](site-recovery-support-matrix-to-sec-site.md) kraven för stöd för alla komponenter.
- Kontrollera att de datorer som du vill replikera uppfyller [replikerade datorn support](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Skapa ett valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad som ska replikera och var du vill replikera den till.

1. Klicka på **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
2. Välj **till återställningsplatsen** > **Ja, med VMware vSphere-Hypervisor**. Klicka sedan på **OK**.
3. I **Scout installationsprogrammet**, ladda ned InMage Scout 8.0.1 GA programvara och Registreringsnyckeln. Installationsfilerna för alla komponenter ingår i den hämta ZIP-filen.

## <a name="install-component-updates"></a>Installera Komponentuppdateringar

 Granska och installera senaste [uppdateringar](#updates). Uppdateringar ska installeras på servrar i följande ordning:

1. RX server (om tillämpligt)
2. Konfigurationsservrar
3. Process-servrar
4. Huvudmålet servrar
5. vContinuum-servrar
6. Källservern (både Windows och Linux-servrar)

Installera uppdateringar på följande sätt:

> [!NOTE]
>Uppdatering av alla Scout komponenterna filversion får inte vara samma i uppdateringen ZIP-filen. Den äldre versionen betyda att det finns ingen ändring i komponenten sedan tidigare uppdateringen till den här uppdateringen.

Hämta den [uppdatera](https://aka.ms/asr-scout-update6) ZIP-filen. Filen innehåller följande komponenter: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bitar för RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extrahera ZIP-filer.
2. **RX server**: kopiera **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** till RX-servern och extrahera den. I den extraherade mappen kör **/Install**.
3. **Konfigurationsservern och processervern**: kopiera **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** till konfigurationsservern och processervern. Dubbelklicka om du vill köra den.<br>
4. **Windows-huvudmålservern**: Om du vill uppdatera enhetlig agenten kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till servern. Dubbelklicka på den att köra den. Samma enhetlig agentuppdatering gäller även för källservern. Om källa inte har uppdaterats till uppdatering 4, bör du uppdatera enhetlig agenten.
  Uppdateringen behöver inte tillämpa i bakgrunden mål förbereds med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det är den nya GA installer med de senaste ändringarna.
5. **vContinuum-server**: kopiera **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** till servern.  Kontrollera att du har stängt guiden vContinuum. Dubbelklicka på filen för att köra den.
    Uppdateringen behöver inte tillämpa på Huvudmålet förbereds med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det är den nya GA installer med de senaste ändringarna.
6. **Linux huvudmålservern**: Om du vill uppdatera enhetlig agenten kopiera **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till huvudservern målet server och extrahera den. I den extraherade mappen kör **/Install**.
7. **Windows-källservern**: Om du vill uppdatera enhetlig agenten kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till källservern. Dubbelklicka på filen för att köra den. 
    Du behöver inte installera uppdatering 5-agenten på källservern om den redan har uppdaterats till Update 4 eller källagent installeras med installationsprogrammet för senaste grundläggande **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Linux-källservern**: kopiera motsvarande version av filen unified agent till Linux-servern om du vill uppdatera enhetlig agenten och extrahera den. I den extraherade mappen kör **/Install**.  Exempel: För RHEL 6,7 64 bitarsserver kopierar **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till servern, och extrahera den. I den extraherade mappen kör **/Install**.

## <a name="enable-replication"></a>Aktivera replikering

1. Konfigurera replikering mellan käll- och mål-VMware-platser.
2. Använd InMage Scout dokumentationen som hämtas med produkten vägledning. Du kan också du kan komma åt dokumentationen på följande sätt:

   * [Viktig information](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitetsmatrix](https://aka.ms/asr-scout-cm)
   * [Användarhandboken](https://aka.ms/asr-scout-user-guide)
   * [RX användarhandboken](https://aka.ms/asr-scout-rx-user-guide)
   * [Snabb installationsguiden](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Uppdateringar

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 uppdatering 6 
Uppdaterad: Oktober 12 2017

Scout uppdatering 6 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 för uppdatering 5 samt nya korrigeringar och förbättringar som beskrivs nedan. 

#### <a name="new-platform-support"></a>Stöd för nya plattformar
* Stöd har lagts till för källa Windows Server 2016
* Stöd har för följande Linux-operativsystem lagts till:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Stöd har lagts till för VMware Center 6.5

> [!NOTE]
> * Grundläggande Agent(UA) Unified installer för Windows har uppdaterats till stöd för Windows Server 2016. Nya installationsprogrammet **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Med samma installationsprogram ska användas för alla Windows-version som stöds. 
> * Grundläggande Windows vContinuum & Huvudmålet installer har uppdaterats till stöd för Windows Server 2016. Nya installationsprogrammet **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Med samma installationsprogram används för att distribuera Windows 2016 Huvudmålet och Huvudmålservern för Windows 2012 R2.
> * Hämta GA från portalen, enligt beskrivningen i [skapa ett valv](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar
- Återställning av skydd misslyckas för Linux VM med listan över diskar som ska replikeras är tom i slutet av konfig.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 uppdatering 5
Scout uppdatering 5 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 för Update 4 och nya korrigeringar som beskrivs nedan.
- Korrigeringar från Site Recovery Scout uppdatering 4 för uppdatering 5 är speciellt avsedda för mål- och vContinuum huvudkomponenter.
- Om källservrar, huvudmålservern, konfiguration, process och RX servrar redan kör uppdatering 4, tillämpas den endast på huvudmålservern. 

#### <a name="new-platform-support"></a>Stöd för nya plattformar
* SUSE Linux Enterprise Server 11 Service Pack-4(SP4)
* SLES 11 SP4 64 bitars **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** paketeras med grundläggande Scout GA-paketet (**InMage_Scout_Standard_8.0.1 GA.zip**). Hämta GA från portalen, enligt beskrivningen i [skapa ett valv](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar

* Korrigeringar för ökad Windows-kluster stöder tillförlitlighet:
    * Fast – vissa av P2V-MSCS klusterdiskar blir RAW efter återställningen.
    * Fixed-P2V MSCS-kluster återställningen misslyckas på grund av en disk ordning.
    * Fast det MSCS kluster åtgärden för att lägga till diskar misslyckas med ett diskfel storlek matchningsfel.
    * Fast i beredskapskontrollen för källan MSCS-kluster med mappning av RDM LUN misslyckas storlek verifieringen.
    * Fixed-enskild nod klustret skyddet fungerar inte på grund av ett matchningsfel SCSI-problem. 
    * Fixed-återaktivera skyddet av P2V-Windows-klusterserver misslyckas om mål-klusterdiskar finns. 
    
* Fast: Under återställning efter fel skyddet om den markerade mallen riktar server finns inte på ESXi samma server som skyddade källdatorn (under vidarebefordra skydd), och sedan vContinuum hämtar fel huvudmålservern under återställning efter fel återställning och återställningen åtgärden misslyckas.

> [!NOTE]
> * Korrigeringar för P2V-kluster gäller endast fysiska MSCS-kluster som nyligen skyddats med Site Recovery Scout uppdatering 5. Installera kluster-korrigeringar på skyddade P2V MSCS-kluster med äldre uppdateringar, så uppgradera anvisningarna i avsnittet 12 i den [Site Recovery Scout viktig information](https://aka.ms/asr-scout-release-notes).
> * Om samma uppsättning med diskar är aktiva på alla klusternoder vid tidpunkten för återaktivera skyddet som de var när ursprungligen skyddad, kan återaktivera skyddet på ett fysiskt MSCS-kluster endast att återanvända befintliga mål-diskar. Om inte, använder de manuella stegen i avsnittet 12 i [Site Recovery Scout viktig information](https://aka.ms/asr-scout-release-notes), flytta mål på serversidan diskar till rätt datastore-sökvägen för användning under återaktivera skyddet. Om du skyddar MSCS-kluster i P2V läge utan att uppgradera följande skapas en ny disk på ESXi målservern. Du måste manuellt ta bort gamla diskar från databasen.
> * När en SLES11 eller SLES11 (med alla servicepack) källserver startas utan problem, sedan manuellt vill markera det **rot** disk replikering par för omsynkronisering. Det finns inget meddelande i CX-gränssnittet. Om du inte markerar disken roten för omsynkronisering märker du problem med dataintegriteten.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 uppdatering 4
Scout Update 4 är en kumulativ uppdatering. Det innehåller alla korrigeringar från uppdatering 1 för uppdatering 3 och nya korrigeringar som beskrivs nedan.

#### <a name="new-platform-support"></a>Stöd för nya plattformar

* Stöd har lagts till för vCenter/vSphere 6.0, 6.1 och 6.2
* Stöd har lagts till för dessa Linux-operativsystem:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 och 7.2
  * CentOS 7.0, 7.1 och 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bitars **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** paketeras med grundläggande Scout GA paketet **InMage_Scout_Standard_8.0.1 GA.zip**. Hämta Scout GA-paketet från portalen, enligt beskrivningen i [skapa ett valv](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar

* Förbättrad avstängning hantering för följande Linux-operativsystem och klonar att förhindra oönskade omsynkronisering problem:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* För Linux begränsade åtkomstbehörigheter för alla mappar i installationskatalogen unified agent nu till den lokala användaren.
* I Windows, en korrigering för en timeout för problem som uppstod vid utfärdande av vanliga distribuerade konsekvenskontroll bokmärken överbelastad på distribuerade program som SQL Server och Share Point-kluster.
* En logg relaterade korrigeringsfilen i grundläggande installationsprogrammet för configuration-servern.
* En nedladdningslänk för VMware vCLI 6.0 har lagts till Windows huvudmålservern grundläggande installer.
* Loggar och ytterligare kontroller har lagts till för ändringarna i nätverkskonfigurationen under växling vid fel och disaster recovery-övningar.
* En korrigering för ett problem som orsakade kvarhållning information inte ska rapporteras till konfigurationsservern.  
* Fysiska kluster, en korrigering för ett problem som orsakade volym ändrar storlek så att den inte i vContinuum-guiden när du minska storleken på källvolymen.
* En korrigering för ett kluster skydd problem som misslyckades med felet: ”Det gick inte att hitta disksignaturen”, när klustret disken är en PRDM.
* En korrigering för ett cxps transport server kraschar, på grund av ett undantag intervall.
* Servernamn och IP-adresskolumner är nu ändras i den **Push-Installation av** vContinuum-guiden.
* Förbättringar av RX API:
  * De fem senaste tillgängliga vanliga konsekvenskontrollen poäng nu tillgängliga (endast garanteras taggar).
  * Kapacitet och ledigt utrymme informationen visas för alla skyddade enheter.
  * Scout drivrutinen tillstånd på källservern är tillgänglig.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** grundläggande paketet:
    * En uppdaterad konfiguration server grundläggande installer (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * En Windows huvudmålservern grundläggande installer (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Använd nya konfigurationsservern och Windows huvudmålservern GA bits för alla nya installationer.
> * Update 4 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 uppdatering 3

Alla Site Recovery-uppdateringar är kumulativa. Uppdatering 3 innehåller alla korrigeringar från uppdatering 1 och uppdatering 2. Uppdatering 3 kan tillämpas direkt på 8.0.1 GA. Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats.

#### <a name="bug-fixes-and-enhancements"></a>Felkorrigeringar och förbättringar
3 åtgärdas på följande:

* Konfigurationsservern och RX är inte registrerad i valvet när de är bakom proxyn.
* Antalet timmar som återställningspunktmål (RPO) inte nått uppdateras inte i hälsorapporten.
* Konfigurationsservern är inte synkroniserar med RX när information om ESX maskinvara eller nätverksinformation, innehåller UTF-8 tecken.
* Windows Server 2008 R2-domänkontrollanter starta inte efter återställningen.
* Offlinesynkronisering fungerar inte som förväntat.
* Efter redundansväxling av Virtuella datorer, replikering par borttagning inte gå vidare i configuration server-konsolen under lång tid. Användare kan inte slutföra återställningen eller återuppta åtgärder.
* Övergripande ögonblicksbild aktiviteter med konsekvenskontroll jobbet har optimerats, för att minska program kopplas från, till exempel SQL Server-klienter.
* Prestanda för konsekvens-verktyget (VACP.exe) har förbättrats. Användning av minne krävs för att skapa ögonblicksbilder i Windows har minskats.
* Push-installera kraschar när lösenordet är större än 16 tecken.
* vContinuum inte kontrollera och fråga efter nya vCenter autentiseringsuppgifter när autentiseringsuppgifterna har ändrats.
* Huvudmålservern Cachehanteraren (cachemgr) är inte på Linux, ladda ned filer från processervern. Detta resulterar i replikering par begränsning.
* När fysiska failover cluster (MSCS) disk ordning är inte på alla noder, replikering har inte angetts för några av volymerna. Klustret måste att återaktivera skyddet för att dra nytta av den här korrigeringen.  
* SMTP-funktioner fungerar inte som förväntat, när RX har uppgraderats från Scout 7.1 till Scout 8.0.1.
* Flera statistik har lagts till i loggen för återställningsåtgärd att spåra den tid det tar att slutföra den.
* Stöd har lagts till för Linux-operativsystem på källservern:
  * Red Hat Enterprise Linux (RHEL) 6 uppdatering 7
  * CentOS 6 uppdatering 7
* Konfigurationsservern och RX konsoler nu visa meddelanden om ett par övergår i bitmappsläget.
* Följande säkerhetskorrigeringar har lagts till i RX:
    * Auktorisering kringgå via parametern manipulation: begränsad åtkomst till användare som inte är tillämpliga.
    * Begäran förfalskning: sidan token konceptet implementerades och genererar slumpmässigt för varje sida. Det innebär att det finns bara en inloggning instans för samma användare och uppdatering av sidan inte fungerar. I stället omdirigerar på instrumentpanelen.
    * Skadliga filöverföringen: filerna är begränsad till specifika tillägg: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log mid mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, RAM-minne, rar, rm, rmi, rmvb, RTF- , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml och zip.
    * Beständiga globala webbplatsskript: indata verifieringar har lagts till.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 uppdatering 2 (uppdatering 03 Dec 15)

Korrigeringar i uppdatering 2 är:

* **Konfigurationsservern**: problem som hindrar den 31 dagars kostnadsfri avläsning funktion inte fungerar som förväntat, när konfigurationsservern har registrerats i Site Recovery.
* **Enhetlig agent**: åtgärda ett problem i uppdatering 1 som resulterade i uppdateringen inte installeras på huvudmålservern, vid uppgradering från version 8.0 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 uppdatering 1
Uppdatering 1 innehåller följande felkorrigeringar och nya funktioner:

* ledigt skydd per serverinstans 31 dagar. På så sätt kan du testa funktioner eller ställa in en--konceptbevis.
* Alla åtgärder på servern, inklusive redundans och återställning efter fel, kan de första 31 dagar. Tid startar när en server först är skyddat med Site Recovery Scout. Från och med 32 dagen debiteras varje skyddad server med standard instans hastighet för Site Recovery-skydd till en plats som ägs av kunden.
* När som helst, antalet skyddade servrar som debiteras är tillgängligt på den **instrumentpanelen** i valvet.
* Stöd har lagts till för vSphere kommandoradsgränssnittet (vCLI) 5.5 uppdatering 2.
* Stöd har lagts till för dessa Linux-operativsystem på källservern:
    * RHEL 6 uppdatering 6
    * RHEL 5 uppdatera 11
    * CentOS 6 uppdatering 6
    * CentOS 5 Update 11
* Felkorrigeringar för tänka på följande:
  * Valvet registreringen misslyckas för konfigurationsservern eller RX server.
  * Klustervolymer visas inte som förväntat när klustrade virtuella datorer är att återaktivera skyddet eftersom de återupptas.
  * Det går inte att återställning efter fel när huvudmålservern finns på en annan server ESXi från lokala produktion virtuella datorer.
  * Filen konfigurationsbehörighet ändras när du uppgraderar till 8.0.1. Den här ändringen påverkar skydd och åtgärder.
  * Tröskelvärdet för omsynkronisering är inte tillämpas som förväntat, orsakas inkonsekvent replikering.
  * Inställningarna för Återställningspunktmål visas inte korrekt i configuration server-konsolen. Okomprimerad datavärdet visar felaktigt komprimerade värdet.
  * Åtgärden ta bort ta bort inte som förväntat i guiden vContinuum och replikering tas inte bort från configuration server-konsolen.
  * I guiden vContinuum disken är avmarkerat automatiskt när du klickar på **information** i vyn disk under skydd av MSCS virtuella datorer.
  * I scenariot för fysisk till virtuell (P2V) har flyttas inte nödvändiga HP-tjänster (till exempel CIMnotify och CqMgHost) till manuell i VM-återställning. Det här problemet resulterar i ytterligare starten.
  * Linux VM skyddet fungerar inte när det finns fler än 26 diskar på huvudmålservern.

