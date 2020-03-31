---
title: Haveriberedskap av virtuella datorer/fysiska VM-servrar till en sekundär plats med Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap för virtuella datorer, eller Windows och Linux fysiska servrar, till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256814"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurera haveriberedskap för lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats

InMage Scout i [Azure Site Recovery](site-recovery-overview.md) ger replikering i realtid mellan lokala VMware-platser. InMage Scout ingår i Azure Site Recovery-tjänstprenumerationer.

## <a name="end-of-support-announcement"></a>Meddelande om upphörande support

Azure Site Recovery-scenariot för replikering mellan lokala VMware eller fysiska datacenter når supportens.

-   Från och med augusti 2018 kan scenariot inte konfigureras i Recovery Services-valvet och InMage Scout-programvaran kan inte hämtas från valvet. Befintliga distributioner kommer att stödjas. 
-   Från och med den 31 december 2020 stöds inte scenariot.
- Befintliga partner kan gå ombord på nya kunder till scenariot tills supporten upphör.

Under 2018 och 2019 kommer två uppdateringar att släppas: 

-   Uppdatering 7: Åtgärdar problem med nätverkskonfiguration och efterlevnad och ger stöd för TLS 1.2.
-   Uppdatering 8: Lägger till stöd för Linux-operativsystem RHEL/CentOS 7.3/7.4/7.5 och för SUSE 12

Efter uppdatering 8 kommer inga ytterligare uppdateringar att släppas. Det kommer att finnas begränsat stöd för snabbkorrigering för operativsystem som läggs till i uppdatering 8 och buggfixar baserat på bästa möjliga ansträngning.

Azure Site Recovery fortsätter att förnya sig genom att ge VMware- och Hyper-V-kunder en sömlös och förstklassig DRaaS-lösning med Azure som en plats för katastrofåterställning. Microsoft rekommenderar att befintliga InMage / ASR Scout kunder överväga att använda Azure Site Recovery vmware till Azure scenario för deras behov av affärskontinuitet. Azure Site Recoverys VMware to Azure-scenario är en DR-lösning i företagsklass för VMware-program, som erbjuder RPO och RTO med minuter, stöd för programreplikering och återställning med flera virtuella datorer, sömlös introduktion, omfattande övervakning och betydande fördel inom den höge koderna.

### <a name="scenario-migration"></a>Scenariomigrering
Som ett alternativ rekommenderar vi att du konfigurerar haveriberedskap för lokala virtuella datorer och fysiska datorer genom att replikera dem till Azure. Gör på följande sätt:

1.  Granska den snabba jämförelsen nedan. Innan du kan replikera lokala datorer måste du kontrollera att de uppfyller [kraven](./vmware-physical-azure-support-matrix.md#replicated-machines) för replikering till Azure. Om du replikerar virtuella virtuella datorer med VMware rekommenderar vi att du granskar riktlinjerna för [kapacitetsplanering](./site-recovery-plan-capacity-vmware.md)och kör [verktyget Distributionsplanerare](./site-recovery-deployment-planner.md) för att identifiera kapacitetskrav och verifiera efterlevnad.
2.  När du har kört distributionsplaneraren kan du konfigurera replikering: o För virtuella VMware-datorer följer du dessa självstudier för att [förbereda Azure,](./tutorial-prepare-azure.md) [förbereda din lokala VMware-miljö](./vmware-azure-tutorial-prepare-on-premises.md)och [konfigurera haveriberedskap](./vmware-azure-tutorial-prepare-on-premises.md).
o För fysiska maskiner, följ den här [guiden](./physical-azure-disaster-recovery.md).
3.  När datorer replikerar till Azure kan du köra en [borr för haveriberedskap](./site-recovery-test-failover-to-azure.md) för att se till att allt fungerar som förväntat.

### <a name="quick-comparison"></a>Snabb jämförelse

**Funktion** | **Replikering till Azure** |**Replikering mellan VMware-datacenter**
--|--|--
**Nödvändiga komponenter** |Mobilitetstjänst på replikerade maskiner. Lokal konfigurationsserver, processserver, huvudmålserver. Tillfällig processserver i Azure för återställning efter fel.|Mobilitetstjänst, processserver, konfigurationsserver och huvudmål
**Konfiguration och orkestrering** |Valvet för återställningstjänster i Azure-portalen | Använda vContinuum 
**Replikerad** |Disk (Windows och Linux) |Volym-Windows<br> Disk-Linux
**Delat diskkluster** |Stöds inte|Stöds
**Gränser för dataomsättning (genomsnitt)** |10 MB/s data per disk<br> 25 MB/s data per virtuell dator<br> [Läs mer](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s-data per disk  <br> > 25 MB/s-data per virtuell dator
**Övervakning** |Från Azure-portalen|Från CX (konfigurationsserver)
**Stöd Matrix** | [Klicka här för mer information](./vmware-physical-azure-support-matrix.md)|[Ladda ner ASR Scout-kompatibel matris](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Krav
För att slutföra den här kursen behöver du:

- [Granska](vmware-physical-secondary-support-matrix.md) supportkraven för alla komponenter.
- Se till att de datorer som du vill replikera uppfyller [replikerade maskinstöd](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Hämta och installera komponentuppdateringar

 Granska och installera de senaste [uppdateringarna](#updates). Uppdateringar bör installeras på servrar i följande ordning:

1. RX-server (om tillämpligt)
2. Konfigurationsservrar
3. Bearbeta servrar
4. Huvudmålservrar
5. vContinuum-servrar
6. Källserver (både Windows- och Linux-servrar)

Installera uppdateringarna enligt följande:

> [!NOTE]
>Alla Scout-komponenters filuppdateringsversion kanske inte är desamma i zip-filen för uppdateringen. Den äldre versionen anger att det inte finns någon ändring i komponenten sedan föregående uppdatering av den här uppdateringen.

Ladda ner [uppdateringen](https://aka.ms/asr-scout-update7) .zip-filen och [konfigurationsfilerna för MySQL- och PHP-uppgradering.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) Zip-filen update innehåller alla basbinärer och kumulativa uppgraderingsbinärer för följande komponenter: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Extrahera ZIP-filerna.
  2. **RX-server:** Kopiera **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** till RX-servern och extrahera den. Kör **/Installera**i den extraherade mappen .
  3. **Konfigurationsserver och processserver**: Kopiera **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** till konfigurationsservern och processservern. Dubbelklicka för att köra den.<br>
  4. **Windows Master Target server:** Om du vill uppdatera den enhetliga agenten kopierar **du InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** till servern. Dubbelklicka på den för att köra den. Samma fil kan också användas för ny installation. Samma enhetliga agentuppdatering gäller även för källservern.
  Uppdateringen behöver inte gälla på huvudmålet som utarbetats med **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** eftersom det här är nytt GA-installationsprogram med alla de senaste ändringarna.
  5. **vContinuum-server**: Kopiera **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** till servern.  Kontrollera att du har stängt vContinuum-guiden. Dubbelklicka på filen för att köra den.
  6. **Linux-huvudmålserver:** För att uppdatera den enhetliga agenten, kopiera **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** till Linux Master Target-servern och extrahera den. Kör **/Installera**i den extraherade mappen .
  7. **Windows-källserver:** Om du vill uppdatera den enhetliga agenten kopierar **du InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** till källservern. Dubbelklicka på filen för att köra den. 
  8. **Linux-källserver:** För att uppdatera den enhetliga agenten kopierar du motsvarande version av den enhetliga agentfilen till Linux-servern och extraherar den. Kör **/Installera**i den extraherade mappen .  Exempel: För RHEL 6.7 64-bitarsserver kopierar **du InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** till servern och extraherar den. Kör **/Installera**i den extraherade mappen .
  9. Efter uppgradering av Configuration Server, Process Server och RX-server med de ovan nämnda installatörerna måste PHP- och MySQL-biblioteken uppgraderas manuellt med steg som nämns i avsnitt 7.4 i [snabbinstallationsguiden](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Aktivera replikering

1. Ställ in replikering mellan käll- och mål-VMware-platserna.
2. Mer information om installation, skydd och återställning finns i följande dokument:

   * [Viktig information](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitetsmatris](https://aka.ms/asr-scout-cm)
   * [Användarhandbok](https://aka.ms/asr-scout-user-guide)
   * [RX-användarhandbok](https://aka.ms/asr-scout-rx-user-guide)
   * [Snabbinstallationsguide](https://aka.ms/asr-scout-quick-install-guide)
   * [Uppgradera MYSQL- och PHP-bibliotek](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Uppdateringar

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Uppdatering 7 
Uppdaterad: December 31, 2018 Ladda ner [Scout uppdatering 7](https://aka.ms/asr-scout-update7).
Scout Update 7 är en fullständig installatör som kan användas för ny installation samt för att uppgradera befintliga agenter / MT som är på tidigare uppdateringar (från uppdatering 1 till uppdatering 6). Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 6 plus de nya korrigeringar och förbättringar som beskrivs nedan.
 
#### <a name="new-features"></a>Nya funktioner
* PCI-efterlevnad
* TLS v1.2 Support

#### <a name="bug-and-security-fixes"></a>Fel- och säkerhetskorrigeringar
* Åtgärdat: Windows Kluster-/fristående datorer har felaktig IP-konfiguration vid återställning/DR-Drill.
* Åtgärdat: Lägg ibland till att diskåtgärden misslyckas för V2V-kluster.
* Åtgärdat: vContinuum-guiden fastnar under återställningsfasen om huvudmålet är Windows Server 2016
* Åtgärdat: MySQL-säkerhetsproblem mildras genom att mysql uppgraderas till version 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Manuell uppgradering för PHP och MySQL på CS, PS och RX
PHP-skriptplattformen bör uppgraderas till version 7.2.10 på Configuration Server, Process Server och RX Server.
MySQL-databashanteringssystemet bör uppgraderas till version 5.7.23 på Configuration Server, Process Server och RX Server.
Följ de manuella stegen i [snabbinstallationsguiden](https://aka.ms/asr-scout-quick-install-guide) för att uppgradera PHP- och MySQL-versioner.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Uppdatering 6 
Uppdaterat: 12 oktober 2017

Ladda ner [Scout uppdatering 6](https://aka.ms/asr-scout-update6).

Scout Uppdatering 6 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 5 plus de nya korrigeringar och förbättringar som beskrivs nedan. 

#### <a name="new-platform-support"></a>Nytt plattformsstöd
* Stöd har lagts till för Source Windows Server 2016
* Stöd har lagts till för följande Linux-operativsystem:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6.8
* Stöd har lagts till för VMware Center 6.5

Installera uppdateringarna enligt följande:

> [!NOTE]
>Alla Scout-komponenters filuppdateringsversion kanske inte är desamma i zip-filen för uppdateringen. Den äldre versionen anger att det inte finns någon ändring i komponenten sedan föregående uppdatering av den här uppdateringen.

Hämta [uppdateringsfilen](https://aka.ms/asr-scout-update6) .zip. Filen innehåller följande komponenter: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bitar för RHEL5, OL5, OL6, SUSE 10, SUSE\<11: UA_ Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extrahera ZIP-filerna.
  2. **RX-server:** Kopiera **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** till RX-servern och extrahera den. Kör **/Installera**i den extraherade mappen .
  3. **Konfigurationsserver och processserver**: Kopiera **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** till konfigurationsservern och processservern. Dubbelklicka för att köra den.<br>
  4. **Windows Master Target server:** Om du vill uppdatera den enhetliga agenten kopierar **du UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till servern. Dubbelklicka på den för att köra den. Samma enhetliga agentuppdatering gäller även för källservern. Om källan inte har uppdaterats till uppdatering 4 bör du uppdatera den enhetliga agenten.
  Uppdateringen behöver inte gälla på huvudmålet som utarbetats med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det här är nytt GA-installationsprogram med alla de senaste ändringarna.
  5. **vContinuum-server**: Kopiera **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** till servern.  Kontrollera att du har stängt vContinuum-guiden. Dubbelklicka på filen för att köra den.
  Uppdateringen behöver inte gälla på huvudmålet som utarbetats med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det här är nytt GA-installationsprogram med alla de senaste ändringarna.
  6. **Linux-huvudmålserver:** För att uppdatera den enhetliga agenten, kopiera **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till huvudmålservern och extrahera den. Kör **/Installera**i den extraherade mappen .
  7. **Windows-källserver:** Om du vill uppdatera den enhetliga agenten kopierar **du UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till källservern. Dubbelklicka på filen för att köra den. 
  Du behöver inte installera Update 5-agenten på källservern om den redan har uppdaterats till uppdatering 4 eller källagenten är installerad med den senaste basinstallationsprogrammet **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Linux-källserver:** För att uppdatera den enhetliga agenten kopierar du motsvarande version av den enhetliga agentfilen till Linux-servern och extraherar den. Kör **/Installera**i den extraherade mappen .  Exempel: För RHEL 6.7 64-bitarsserver kopierar **du UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** till servern och extraherar den. Kör **/Installera**i den extraherade mappen .


> [!NOTE]
> * Installationsprogrammet för Base Unified Agent(UA) för Windows har uppdaterats så att det stöder Windows Server 2016. Det nya installationsprogrammet **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** är förpackat med basscout-GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Samma installationsprogram kommer att användas för alla windows-versioner som stöds. 
> * Installationsprogrammet för Windows vContinuum & Master Target har uppdaterats så att det stöder Windows Server 2016. Det nya installationsprogrammet **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** är förpackat med basscout-GA-paketet (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Samma installationsprogram används för att distribuera Windows 2016 Master Target och Windows 2012R2 Master Target.
> * Windows Server 2016 på den fysiska servern stöds inte av ASR Scout. Den stöder endast Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Buggfixar och förbättringar
- Återställningsskyddet misslyckas för Linux VM med en lista över diskar som ska replikeras är tom i slutet av konfigurationen.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Uppdatering 5
Scout Uppdatering 5 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 4, och de nya korrigeringarna som beskrivs nedan.
- Korrigeringar från Site Recovery Scout Update 4 till Uppdatering 5 är specifikt för huvudmål- och vContinuum-komponenterna.
- Om källservrar, huvudmål-, konfigurations-, process- och RX-servrar redan kör uppdatering 4, tillämpas det bara på huvudmålservern. 

#### <a name="new-platform-support"></a>Nytt plattformsstöd
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** är förpackat med basen Scout GA-paketet **(InMage_Scout_Standard_8.0.1 GA.zip).** Hämta GA-paketet från portalen, enligt beskrivningen i Skapa ett valv.


#### <a name="bug-fixes-and-enhancements"></a>Buggfixar och förbättringar

* Korrigeringar för ökad tillförlitlighet i Windows-klusterstöd:
    * Fast- Några av P2V MSCS klusterdiskar blir RAW efter återställning.
    * Fast- P2V MSCS kluster återhämtning misslyckas på grund av en diskorder obalans.
    * Fast- MSCS-klusteråtgärden för att lägga till diskar misslyckas med ett felmatchningsfel i diskstorleken.
    * Fast- Beredskapskontrollen för käll-MSCS-klustret med RDM LUN-mappning misslyckas i storleksverifiering.
    * Skydd för kluster med en nod misslyckas på grund av ett SCSI-problem som inte stämmer överens. 
    * Fast- Återskydd av P2V Windows klusterserver misslyckas om målkluster diskar finns. 
    
* Åtgärdat: Om den valda huvudmålservern inte finns på samma ESXi-server som den skyddade källdatorn (under skyddet framåt) hämtar vContinuum fel huvudmålserver under återställning av återställning av återställning åtgärden misslyckas.

> [!NOTE]
> * P2V-klusterkorrigeringarna är endast tillämpliga på fysiska MSCS-kluster som nyligen har skyddats med Site Recovery Scout Update 5. Om du vill installera klusterkorrigeringar på skyddade P2V MSCS-kluster med äldre uppdateringar följer du uppgraderingsstegen som nämns i avsnitt 12 i [Release Notes för Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Om samma uppsättning diskar vid tidpunkten för återskyddet är aktiva på var och en av klusternoderna som de var när de först skyddades, kan återanvändning av ett fysiskt MSCS-kluster endast återanvända befintliga måldiskar. Om inte, använd sedan de manuella stegen i avsnitt 12 i [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes)för att flytta målsidans diskar till rätt datalagersökväg för återanvändning under återskydd. Om du roterar om MSCS-klustret i P2V-läge utan att följa uppgraderingsstegen skapas en ny disk på mål-ESXi-servern. Du måste manuellt ta bort de gamla diskarna från datalagret.
> * När en källa SLES11 eller SLES11 (med valfri Service Pack-server) **root** startas om på ett smidigt sätt markerar du sedan rotdiskreplikeringsparen manuellt för återsynkronisering. Det finns inget meddelande i CX-gränssnittet.Om du inte markerar rotdisken för omsynkronisering kan det hända att dataintegritetsproblem.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Uppdatering 4
Scout Uppdatering 4 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 3 och de nya korrigeringarna som beskrivs nedan.

#### <a name="new-platform-support"></a>Nytt plattformsstöd

* Stöd har lagts till för vCenter/vSphere 6.0, 6.1 och 6.2
* Stöd har lagts till för dessa Linux-operativsystem:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 och 7,2
  * CentOS 7,0, 7,1 och 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 **bitars InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** är förpackat med basen Scout GA-paketet **InMage_Scout_Standard_8.0.1 GA.zip**. Ladda ner Scout GA-paketet från portalen enligt beskrivningen i Skapa ett valv.

#### <a name="bug-fixes-and-enhancements"></a>Buggfixar och förbättringar

* Förbättrad avstängningshantering för följande Linux-operativsystem och kloner, för att förhindra oönskade omsynkroniseringsproblem:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* För Linux är alla mappåtkomstbehörigheter i den enhetliga agentinstallationskatalogen nu begränsade till endast den lokala användaren.
* I Windows har du en korrigering för ett problem med tidsgränsen som uppstod när vanliga distribuerade bokmärken för konsekvens utfärdades, på kraftigt inlästa distribuerade program som SQL Server- och Share Point-kluster.
* En loggrelaterad korrigering i konfigurationsserverbasinstallationsprogrammet.
* En nedladdningslänk till VMware vCLI 6.0 lades till i Windows-huvudmålbasinstallationsprogrammet.
* Ytterligare kontroller och loggar har lagts till för nätverkskonfigurationsändringar under redundans- och haveriberedskapsövningar.
* En korrigering för ett problem som gjorde att kvarhållningsinformation inte rapporterades till konfigurationsservern.  
* För fysiska kluster kan en korrigering för ett problem som orsakade att volymändringen misslyckades i vContinuum-guiden när källvolymen krymptes.
* En korrigering för ett klusterskyddsproblem som misslyckades med fel: "Det gick inte att hitta disksignaturen", när klusterdisken är en PRDM-disk.
* En korrigering för en cxps-transportserverkrasch som orsakas av ett undantag utanför intervallet.
* Servernamn och IP-adresskolumner kan nu ändras på sidan **Push-installation** i guiden vContinuum.
* Förbättringar av RX API:
  * De fem senaste tillgängliga gemensamma konsekvenspunkterna finns nu tillgängliga (endast garanterade taggar).
  * Information om kapacitet och ledigt utrymme visas för alla skyddade enheter.
  * Scoutdrivrutinstillståndet på källservern är tillgängligt.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip baspaketet** har:
>     * Ett uppdaterat installationsprogram för konfigurationsserverns bas (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Ett windows-huvudmålbasinstallationsprogram (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * För alla nya installationer använder du den nya konfigurationsservern och Windows-huvudmål-GA-bitar.
> * Uppdatering 4 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurationsservern och RX-uppdateringarna kan inte återställas när de har tillämpats.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Uppdatering 3

Alla uppdateringar av webbplatsåterställning är kumulativa. Uppdatering 3 innehåller alla korrigeringar från uppdatering 1 och uppdatering 2. Uppdatering 3 kan tillämpas direkt på 8.0.1 GA. Konfigurationsservern och RX-uppdateringarna kan inte återställas när de har tillämpats.

#### <a name="bug-fixes-and-enhancements"></a>Buggfixar och förbättringar
Uppdatering 3 åtgärdar följande problem:

* Konfigurationsservern och RX registreras inte i valvet när de ligger bakom proxyn.
* Antalet timmar då återställningspunktsmålet (RPO) inte uppnåddes uppdateras inte i hälsorapporten.
* Konfigurationsservern synkroniseras inte med RX när ESX-maskinvaruinformationen, eller nätverksinformationen, innehåller utf-8-tecken.
* Windows Server 2008 R2-domänkontrollanter startar inte efter återställning.
* Offlinesynkronisering fungerar inte som förväntat.
* Efter felfrigång för virtuella datorer fortskrider inte borttagning av replikeringspar i konfigurationsserverkonsolen under lång tid. Användare kan inte slutföra återställningen eller återuppta åtgärder.
* Övergripande ögonblicksbildåtgärder av konsekvensjobbet har optimerats för att minska programfrånkopplingar som SQL Server-klienter.
* Prestandan för konsekvensverktyget (VACP.exe) har förbättrats. Minnesanvändningen som krävs för att skapa ögonblicksbilder i Windows har minskat.
* Push-installationstjänsten kraschar när lösenordet är större än 16 tecken.
* vContinuum söker inte efter nya vCenter-autentiseringsuppgifter när autentiseringsuppgifterna ändras.
* På Linux hämtar inte cachehanteraren (cachemgr) för huvudmålcachen (cachemgr) filer från processservern. Detta resulterar i replikeringsparbegränsning.
* När den fysiska redundansklustret (MSCS) diskordning inte är densamma på alla noder, är replikering inte inställd för vissa klustervolymer. Klustret måste reprotected att dra nytta av denna fix.  
* SMTP-funktionen fungerar inte som förväntat, efter att RX uppgraderats från Scout 7.1 till Scout 8.0.1.
* Mer statistik har lagts till i loggen för återställningsåtgärden för att spåra den tid det tar att slutföra den.
* Stöd har lagts till för Linux-operativsystem på källservern:
  * Red Hat Enterprise Linux (RHEL) 6 uppdatering 7
  * CentOS 6 uppdatering 7
* Konfigurationsservern och RX-konsolerna visar nu meddelanden för paret, som går in i bitmappsläge.
* Följande säkerhetskorrigeringar har lagts till i RX:
    * Förbikoppling av auktorisering via parametermanipulering: Begränsad åtkomst till icke-tillämpliga användare.
    * Förfalskning mellan webbplatser: Sidtoken-konceptet har implementerats och genereras slumpmässigt för varje sida. Det innebär att det bara finns en enda inloggningsinstanstans för samma användare och siduppdatering fungerar inte. I stället omdirigeras den till instrumentpanelen.
    * Skadlig filöverföring: Filer är begränsade till specifika tillägg: z, aiff, asf, avi, bmp, csv, doc, docx, lin, flv, gif, gz, gzip, jpeg, jpg, log, mitten, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml och zip.
    * Beständiga skript över flera webbplatser: Indatavalider har lagts till.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Uppdatering 2 (Uppdatering 03Dec15)

Korrigeringar i uppdatering 2 inkluderar:

* **Konfigurationsserver:** Problem som förhindrade att 31-dagars kostnadsfria mätarfunktionen fungerade som förväntat när konfigurationsservern registrerades i Azure Site Recovery Vault.
* **Enhetlig agent:** Åtgärda för ett problem i uppdatering 1 som resulterade i att uppdateringen inte installerades på huvudmålservern under uppgraderingen från version 8.0 till 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Uppdatering 1
Uppdatering 1 innehåller följande buggfixar och nya funktioner:

* 31 dagars gratis skydd per serverinstans. På så sätt kan du testa funktioner eller ställa in ett proof-of-concept.
* Alla åtgärder på servern, inklusive redundans och redundans, är kostnadsfria under de första 31 dagarna. Tiden börjar när en server först skyddas med Site Recovery Scout. Från den 32:a dagen debiteras varje skyddad server enligt standardinstanshastigheten för skydd för webbplatsåterställning till en kundägd plats.
* När som helst är antalet skyddade servrar som för närvarande debiteras tillgängligt på **instrumentpanelen** i valvet.
* Stöd har lagts till för vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Stöd har lagts till för dessa Linux-operativsystem på källservern:
    * Uppdatering 6 för RHEL 6
    * RHEL 5 Uppdatering 11
    * CentOS 6 Uppdatering 6
    * CentOS 5 Uppdatering 11
* Buggfixar för att lösa följande problem:
  * Valvregistrering misslyckas för konfigurationsservern eller RX-servern.
  * Klustervolymer visas inte som förväntat när klustrade virtuella datorer roteras igen när de återupptas.
  * Återställningen misslyckas när huvudmålservern finns på en annan ESXi-server från de lokala virtuella produktions-datorerna.
  * Konfigurationsfilbehörigheter ändras när du uppgraderar till 8.0.1. Den här ändringen påverkar skydd och åtgärder.
  * Tröskelvärdet för omsynkronisering tillämpas inte som förväntat, vilket orsakar inkonsekvent replikeringsbeteende.
  * RPO-inställningarna visas inte korrekt i konfigurationsserverkonsolen. Det okomprimerade datavärdet visar felaktigt det komprimerade värdet.
  * Åtgärden Ta bort tas inte bort som förväntat i vContinuum-guiden och replikeringen tas inte bort från konfigurationsserverkonsolen.
  * I vContinuum-guiden avmarkeras disken automatiskt när du klickar på **Information** i diskvyn under skydd av virtuella MSCS-datorer.
  * I scenariot fysiskt till virtuellt (P2V) flyttas inte nödvändiga HP-tjänster (till exempel CIMnotify och CqMgHost) till manuell återställning av virtuella datorer. Det här problemet resulterar i ytterligare starttid.
  * Linux VM-skydd misslyckas när det finns fler än 26 diskar på huvudmålservern.

