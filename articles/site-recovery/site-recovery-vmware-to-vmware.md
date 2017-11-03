---
title: Replikera virtuella VMware-datorer eller fysiska servrar till en annan plats (klassiska Azure-portalen) | Microsoft Docs
description: "Använd den här artikeln för att replikera virtuella VMware-datorer eller Windows-/ Linux fysiska servrar till en sekundär plats med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 01a6f35fe61290f8c7275c34273d66956a53d3f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>Replikera lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats i den klassiska Azure-portalen

## <a name="overview"></a>Översikt
InMage Scout i Azure Site Recovery tillhandahåller realtid replikering mellan lokala platser för VMware. InMage Scout ingår i Azure Site Recovery-tjänsten prenumerationer. 

## <a name="prerequisites"></a>Krav
**Azure-konto**: du behöver en [Microsoft Azure](https://azure.microsoft.com/) konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery.

## <a name="step-1-create-a-vault"></a>Steg 1: Skapa ett valv
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på Ny > Management > säkerhetskopiering och återställning (OMS). Du kan också klicka på Bläddra > Återställningstjänstvalvet > Lägg till.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du en av dem.
4. I **resursgruppen** skapa en ny resursgrupp eller välj en befintlig. Ange en Azure-region för att slutföra obligatoriska fält.
5. I **plats**, väljer du ett geografiskt område för valvet. Regioner som stöds finns i [priser för Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Om du vill komma åt valvet från instrumentpanelen på Fäst på instrumentpanelen och klicka på Skapa.
7. Det nya valvet visas på instrumentpanelen > alla resurser, och på den huvudsakliga Recovery Services-valv bladet.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Steg 2: Konfigurera valvet och ladda ned InMage Scout komponenter
1. Välj ditt valv och klicka på inställningar i bladet Recovery Services-valv.
2. I **inställningar** > **komma igång** klickar du på **Site Recovery** > steg 1: **Förbered infrastrukturen**  >  **Skyddsmål**.
3. I **skyddsmål** välja till återställningsplatsen och välj Ja, med VMware vSphere-hypervisor-programmet. Klicka sedan på OK.
4. I **Scout installationsprogrammet**, klicka på hämta ladda ned InMage Scout 8.0.1 GA programvara och registrering nyckel. Installationsfilerna för alla nödvändiga komponenter finns i den hämtade ZIP-fil.

## <a name="step-3-install-component-updates"></a>Steg 3: Installera Komponentuppdateringar
Läs mer om senast [uppdateringar](#updates). Du måste installera uppdateringsfiler på servrar i följande ordning:

1. Om det finns en RX-server
2. Konfigurationsservrar
3. Process-servrar
4. Huvudmålservern servrar
5. vContinuum-servrar
6. Källservern (både Windows och Linux-Server)

Installera uppdateringar på följande sätt:

1. Hämta den [uppdatera](https://aka.ms/asr-scout-update5) ZIP-filen. Den här ZIP-filen innehåller följande filer:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * UA update4 bitar för RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Extrahera ZIP-filer.<br>
3. **För RX servern**: kopiera **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** till servern RX och extrahera den. I den extraherade mappen kör **/Install**.<br>
4. **För server-processen konfigurationsservern**: kopiera **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** till konfigurationsservern och processervern. Dubbelklicka om du vill köra den.<br>
5. **För Windows-huvudmålservern**: Om du vill uppdatera enhetlig agenten kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till huvudmålservern. Dubbelklicka på den att köra den. Observera att enhetlig agenten gäller även för källservern om datakällan inte uppdateras till Update4. Du bör installera den på källservern och, som tidigare nämnts senare i den här listan.<br>
6. **För vContinuum-servern**: kopiera **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** till vContinuum-servern.  Kontrollera att du har stängt guiden vContinuum. Dubbelklicka på filen för att köra den.<br>
7. **För Linux-huvudmålservern**: Om du vill uppdatera enhetlig agenten kopiera **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till huvudservern målet server och extrahera den. I den extraherade mappen kör **/Install**.<br>
8. **För Windows-källservern**: du behöver inte installera uppdatering 5-agenten på källan om soruce finns redan på update4. Om det är mindre än update4 gäller uppdatering 5-agenten.
Om du vill uppdatera enhetlig agenten kopiera **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till källservern. Dubbelklicka på den att köra den. <br>
9. **För Linux-källservern**: Om du vill uppdatera enhetlig agenten kopiera motsvarande version av filen UA till Linux-servern och extrahera den. I den extraherade mappen kör **/Install**.  Exempel: För RHEL 6,7 64 bitarsserver kopierar **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till servern och extrahera den. I den extraherade mappen kör **/Install**.

## <a name="step-4-set-up-replication"></a>Steg 4: Konfigurera replikering
1. Konfigurera replikering mellan käll- och mål-VMware-platser.
2. Använd InMage Scout dokumentationen som hämtas med produkten vägledning. Du kan också du kan komma åt dokumentationen på följande sätt:

   * [Viktig information](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitetsmatrix](https://aka.ms/asr-scout-cm)
   * [Användarhandboken](https://aka.ms/asr-scout-user-guide)
   * [RX användarhandboken](https://aka.ms/asr-scout-rx-user-guide)
   * [Snabb installationsguiden](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Uppdateringar
### <a name="azure-site-recovery-scout-801-update-5"></a>Azure Site Recovery Scout 8.0.1 uppdatering 5
Scout uppdatering 5 är en kumulativ uppdatering. Den innehåller alla korrigeringsfiler för update1 till update4 och följande nya felkorrigeringar och förbättringar.
Korrigeringar som har lagts till från ASR Scout update4 till update5 är specifika för huvudserver och vContinuum-komponenter. Om alla dina källservrar, Huvudmålet, konfigurationsservern, Processervern och RX redan finns på ASR Scout update4 måste du installera uppdateringen 5 endast på huvudmålservern. 

**Stöd för nya plattformar**
* SUSE Linux Enterprise Server 11 Service Pack-4(SP4)

> [!NOTE]
> SLES 11 SP4 64 bitars **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** paketeras med grundläggande Scout GA paket **InMage_Scout_Standard_8.0.1 GA.zip**. Hämta Scout GA paketet från portalen som anges i [steg 1](#step-1-create-a-vault).
>

**Felkorrigeringar och förbättringar**

* Ökad tillförlitlighet för stöd av Windows-kluster
    * Fast tid några av P2V-MSCS klusterdiskar blir RAW efter återställning
    * Fixed-P2V MSCS-kluster återställningen misslyckas på grund av disk ordning
    * Fixed-MSCS-kluster lägga till diskar misslyckas med storleken felaktig matchning av disk
    * Fixed-källa MSCS-kluster med RDM LUN mappning beredskapskontroll misslyckas storlek verifieringen
    * Fixed-enskild nod klustret skyddet fungerar inte på grund av SCSI-matchningsfel problem 
    * Fixed-skydda igen av P2V-Windows-klusterserver misslyckas om mål-klusterdiskar finns. 
    
* Under återställning efter fel skyddet, om valda Huvudmålservern är inte på samma ESXi-servern som skyddad källdatorn (under vidarebefordra skydd), vContinuum hämtar fel Huvudmålservern under återställning efter fel återställningen och därefter återställningen misslyckas.

> [!NOTE]
> 
> * Korrigeringar är tillämpliga på endast de fysiska MSCS-kluster som nyligen skyddade med ASR Scout update5 över P2V-klustret. Att använda klustret åtgärdas på redan skyddade P2V MSCS-kluster med äldre uppdateringar, måste du följa Uppgraderingsstegen som nämns i avsnittet 12, uppgradering skyddade P2V MSCS-kluster till Scout Update5 av [ASR Scout viktig information](https://aka.ms/asr-scout-release-notes).
> 
> * Skydda igen av fysiskt MSCS-kluster kan återanvända befintliga måldiskarna endast om vid tidpunkten för återaktivera skyddet, samma uppsättning med diskar är aktiva på alla klusternoder som de var när skyddat från början. Om inte, finns manuella åtgärder som anges i avsnitt 12 i [ASR Scout viktig information](https://aka.ms/asr-scout-release-notes) att flytta sidan måldiskarna till rätt datastore-sökvägen för att använda dem igen under återaktivera skyddet. Om du skyddar MSCS-kluster i P2V läge utan att uppgradera följande kommer den Skapa ny disk på ESXi målservern. Du måste manuellt ta bort gamla diskar från databasen.
> 
> * När datakällan SLES11 eller SLES11 med alla service pack-servern startas utan problem och sedan markera en manuellt i **rot** disk replikering par för synkroniserar som inte ska meddelas i CX UI. Om du inte ' Markera disken roten för omsynkronisering kan det uppstå problem med dataintegriteten (DI).
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 uppdatering 4
Scout Update 4 är en kumulativ uppdatering. Den innehåller alla korrigeringsfiler för update1 till update3 och följande nya felkorrigeringar och förbättringar.

**Stöd för nya plattformar**

* Stöd har lagts till för vCenter/vSphere 6.0, 6.1 och 6.2
* Stöd har lagts till för följande Linux-operativsystem
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 och 7.2
  * CentOS 7.0, 7.1 och 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bitars **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** paketeras med grundläggande Scout GA paket **InMage_Scout_Standard_8.0.1 GA.zip**. Hämta Scout GA paketet från portalen som anges i [steg 1](#step-1-create-a-vault).
>
>

**Felkorrigeringar och förbättringar**

* Förbättrad avstängning hantering för följande Linux OSes och kloner att förhindra oönskade synkroniserar problem.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* Slutför behörigheter i enhetlig katalog för agentinstallation nu är begränsad endast till den lokala användaren åtkomst för Linux.
* Timeout för problem vid utfärdande vanliga distribuerade konsekvenskontroll bok märke på kraftigt läsa in distribuerade program som SQL- och Share Point-kluster i Windows.
* Tillagda loggen relaterade korrigera i CX grundläggande installer.
* VMware vCLI 6.0 länken läggs till grundläggande Huvudmålet för Windows-installationsprogrammet.
* Lägga till fler kontroller och loggar för nätverket konfigurationer ändringar under växling vid fel och DR övningar.
* Tid kvarhållning information har inte rapporterats till CX.  
* För fysiska klustret misslyckas volym ändra storlek igen guiden vContinuum när datakällan volym förminskas inträffade.
* Klustret skydd misslyckades med felet ”Det gick inte att hitta disksignaturen” när klustret är PRDM disk.
* cxps transport server kraschar på grund av undantaget intervall.
* Servernamn och IP-kolumner är nu ändra storlek på sidan för push-installera vContinuum-guiden.
* RX API-förbättringar
  * Innehåller fem senaste tillgängliga vanliga konsekvenspunkter (endast garanteras taggar).
  * Ger kapacitet och information om ledigt utrymme för de skydda enheterna.
  * Ger Scout drivrutinen tillstånd på källservern.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** grundläggande paketet har uppdaterats CX grundläggande installer **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** och Windows Huvudmålet grundläggande installer **InMage_ Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Använd nya CX och Windows Huvudmålet GA-bitar för alla nya installationen.
> * Update 4 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats på systemet.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 uppdatering 3
Uppdatering 3 innehåller följande felkorrigeringar och förbättringar:

* Det gick inte att registrera i Site Recovery-valvet när de är bakom proxyn konfigurationsservern och RX.
* Antalet timmar som återställningspunktmål (RPO) inte är uppfyllt uppdateras inte i hälsorapporten.
* Konfigurationsservern inte synkroniserar med RX när information om ESX maskinvara eller nätverksinformation innehåller UTF-8 tecken.
* Det gick inte att starta efter återställning av Windows Server 2008 R2-domänkontrollanter.
* Offlinesynkronisering fungerar inte som förväntat.
* Efter redundans för virtuell dator (VM), replikering par borttagning hämtar fastnat i CX UI under lång tid och användarna kan inte slutföra återställningen eller åtgärden återuppta.
* Övergripande kopplar ögonblicksbild åtgärder som utförs av konsekvensjobbet har optimerats för att minska program som SQL-klienter.
* Prestanda för verktyget konsekvenskontroll (VACP.exe) har förbättrats genom att minska den mängden minne som krävs för att skapa ögonblicksbilder i Windows.
* Push-installera kraschar när lösenordet är större än 16 tecken.
* vContinuum kontrollerar inte och fråga efter nya vCenter autentiseringsuppgifter när autentiseringsuppgifterna har ändrats.
* På Linux, Cachehanteraren huvudmålservern (cachemgr) inte ladda ned filer från processervern, vilket resulterar i replikering par begränsning.
* När den fysiska kluster (MSCS) disk redundansordning som inte är samma på alla noder har replikering inte angetts för några av volymerna.
  <br/>Observera att klustret behöver att återaktivera skyddet för att dra nytta av den här korrigeringen.  
* SMTP-funktioner fungerar inte som förväntat när RX har uppgraderats från Scout 7.1 till Scout 8.0.1.
* Flera statistik har lagts till i loggen för återställningsåtgärd att spåra den tid det tog för att slutföra den.
* Stöd har lagts till för Linux-operativsystem på källservern:
  * Red Hat Enterprise Linux (RHEL) 6 uppdatering 7
  * CentOS 6 uppdatering 7
* CX och RX UI kan du nu visa meddelandet för par, som blir bitmappsläge.
* Följande säkerhetskorrigeringar har lagts till i RX:

| **Problembeskrivning** | **Procedurer för implementering** |
| --- | --- |
| Auktorisering kringgå via parametern manipulation |Begränsad åtkomst till användare som inte är tillämpliga. |
| Förfalskning av begäran |Implementerad konceptet sida-token som genererar slumpmässigt för varje sida. <br/>Med den här visas: <li> Det finns bara en inloggning instans för samma användare.</li><li>Sidan uppdatera fungerar inte--dirigeras till instrumentpanelen.</li> |
| Skadliga filöverföring |Begränsad filer till vissa tillägg. Tillåtna tillägg är: 7z aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log mid mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, RAM-minne, rar, rm, rmi, rmvb, RTF-, sdc, sitd, swf, sxc, sxw, tar , tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml och zip. |
| Beständiga globala webbplatsskript |Lägga till indata verifieringar. |

> [!NOTE]
> * Alla Site Recovery-uppdateringar är kumulativa. Update 3 har alla korrigeringar av uppdatering 1 och uppdatering 2. Uppdatering 3 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurationsservern och RX uppdateringar kan inte återställas när de har tillämpats på systemet.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 uppdatering 2 (uppdatering 03 Dec 15)
Korrigeringar i uppdatering 2 är:

* **Konfigurationsservern**: åtgärda ett problem som gjorde funktionen 31 dagars kostnadsfri avläsning inte fungerar som förväntat när konfigurationsservern har registrerats i Site Recovery.
* **Enhetlig agent**: åtgärda ett problem i uppdatering 1 som resulterade i uppdateringen inte installeras på huvudmålservern när den har uppgraderats från version 8.0 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 uppdatering 1
Uppdatering 1 innehåller följande felkorrigeringar och nya funktioner:

* ledigt skydd per serverinstans 31 dagar. På så sätt kan du testa funktioner eller ställa in en--konceptbevis.
  * Alla åtgärder på servern, inklusive redundans och återställning efter fel, kan de första 31 dagar från den tidpunkt som en server först är skyddat med Site Recovery Scout.
  * Från och med den 32 dag debiteras varje skyddad server med standard instans hastighet för Azure Site Recovery-skydd på en plats som ägs av kunden.
  * När som helst är antalet skyddade servrar som debiteras för närvarande tillgänglig på sidan instrumentpanelen i Azure Site Recovery-valvet.
* Stöd lagts till för för vSphere kommandoradsgränssnittet (vCLI) 5.5 uppdatering 2.
* Stöd för Linux-operativsystem på källservern:
  * RHEL 6 uppdatering 6
  * RHEL 5 uppdatera 11
  * CentOS 6 uppdatering 6
  * CentOS 5 Update 11
* Felkorrigeringar för tänka på följande:
  * Valvet registreringen misslyckas för konfigurationsservern eller RX servern.
  * Klustervolymer visas inte som förväntat när klustrade virtuella datorer är att återaktivera skyddet när de återupptas.
  * Det går inte att återställning efter fel när huvudmålservern finns på en annan server ESXi från lokala virtuella maskiner.
  * Filen konfigurationsbehörighet ändras när du uppgraderar till 8.0.1 som påverkar skydd och åtgärder.
  * Tröskelvärdet för omsynkronisering är inte tillämpas som förväntat, vilket kan leda till inkonsekvent replikering beteende.
  * Inställningarna för Återställningspunktmål visas inte korrekt i servergränssnitt konfiguration. Okomprimerad datavärdet visar felaktigt komprimerade värdet.
  * Åtgärden ta bort ta bort inte som förväntat i guiden vContinuum och replikering tas inte bort från configuration server-gränssnitt.
  * I guiden vContinuum disken är avmarkerat automatiskt när du klickar på **information** i vyn disk under skyddet för MSCS virtuella datorer.
  * Under scenariot fysisk till virtuell (P2V) är inte obligatoriska HP-tjänster, till exempel CIMnotify och CqMgHost, flyttas till manuell i återställning av virtuell dator. Detta resulterar i ytterligare starten.
  * Det går inte att skydd för Linux virtuella datorer när det finns fler än 26 diskar på huvudmålservern.

## <a name="next-steps"></a>Nästa steg
Alla frågor som du har på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
