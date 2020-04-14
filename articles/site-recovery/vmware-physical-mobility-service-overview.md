---
title: Om mobilitetstjänsten för haveriberedskap för virtuella datorer med VMware och fysiska servrar med Azure Site Recovery | Microsoft-dokument
description: Lär dig mer om Mobilitetsserviceagenten för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259820"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitetstjänsten för virtuella datorer och fysiska servrar

När du ställer in haveriberedskap för virtuella VMware-datorer (VM) och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md)installerar du tjänsten Site Recovery Mobility på varje lokal virtuell virtuell dator för VMware och fysisk server. Mobilitetstjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till site recovery-processservern. Mobilitetstjänsten installeras av programvaran Mobility service agent som du kan distribuera med följande metoder:

- [Push-installation:](#push-installation)När skydd är aktiverat via Azure-portalen installerar Site Recovery mobilitetstjänsten på servern.
- Manuell installation: Du kan installera mobilitetstjänsten manuellt på varje dator via [användargränssnittet (UI)](#install-the-mobility-service-using-ui) eller [kommandotolken](#install-the-mobility-service-using-command-prompt).
- [Automatiserad distribution](vmware-azure-mobility-install-configuration-mgr.md): Du kan automatisera installationen av mobilitetstjänsten med distributionsverktyg för programvara, till exempel Configuration Manager.

> [!NOTE]
> Mobilitetstjänsten använder cirka 6%-10 % minne på källdatorer för virtuella datorer eller fysiska datorer.

## <a name="antivirus-on-replicated-machines"></a>Antivirus på replikerade datorer

Om datorer som du vill replikera kör antivirusprogram utesluter du mobilitetstjänstens installationsmapp _C:\ProgramData\ASR\agent_ från antivirusåtgärder. Detta undantag säkerställer att replikeringen fungerar som förväntat.

## <a name="push-installation"></a>Push-installation

Push-installation är en integrerad del av jobbet som körs från Azure-portalen för att [aktivera replikering](vmware-azure-enable-replication.md#enable-replication). När du har valt den uppsättning virtuella datorer som du vill skydda och aktivera replikering, skickar konfigurationsservern mobilitetstjänstagenten till servrarna, installerar agenten och slutför agentens registrering hos konfigurationsservern.

### <a name="prerequisites"></a>Krav

- Se till att alla [push-installationsförutsättningskrav](vmware-azure-install-mobility-service.md) är uppfyllda.
- Se till att alla serverkonfigurationer uppfyller kriterierna i [supportmatrisen för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).

Arbetsflödet för push-installation beskrivs i följande avsnitt:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobility service agent version 9.23 och högre

Mer information om version 9.23 finns [i Samlad uppdatering 35 för Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Under en push-installation av mobilitetstjänsten utförs följande steg:

1. Agenten skjuts till källmaskinen. Kopiering av agenten till källmaskinen kan misslyckas på grund av flera miljöfel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installationsfel.
1. När agenten har kopierats till servern utförs en förutsättningskontroll på servern.
   - Om alla förutsättningar är uppfyllda påbörjas installationen.
   - Installationen misslyckas om en eller flera av [förutsättningarna](vmware-physical-azure-support-matrix.md) inte uppfylls.
1. Som en del av agentinstallationen installeras VSS-providern (Volume Shadow Copy Service) för Azure Site Recovery. VSS-providern används för att generera programkonsekventa återställningspunkter. Om installationen av VSS-providern misslyckas hoppas det här steget över och agentinstallationen fortsätter.
1. Om agentinstallationen lyckas men VSS-providerinstallationen misslyckas markeras jobbstatusen som **Varning**. Detta påverkar inte kraschkonsekvent återställningspunktgenerering.

    - Om du vill generera programkonsekventa återställningspunkter läser du [vår vägledning](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) för att slutföra en manuell installation av Site Recovery VSS-leverantören.
    - Om du inte vill generera programkonsekventa återställningspunkter [ändrar du replikeringsprincipen](vmware-azure-set-up-replication.md#create-a-policy) för att inaktivera programkonsekventa återställningspunkter.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobilitetsagent version 9.22 och nedan

1. Agenten skjuts till källmaskinen. Kopiering av agenten till källmaskinen kan misslyckas på grund av flera miljöfel. Se [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installationsfel.
1. När agenten har kopierats till servern utförs en förutsättningskontroll på servern.
   - Om alla förutsättningar är uppfyllda påbörjas installationen.
   - Installationen misslyckas om en eller flera av [förutsättningarna](vmware-physical-azure-support-matrix.md) inte uppfylls.

1. Som en del av agentinstallationen installeras VSS-providern (Volume Shadow Copy Service) för Azure Site Recovery. VSS-providern används för att generera programkonsekventa återställningspunkter.
   - Om VSS-providerinstallationen misslyckas misslyckas agentinstallationen. För att undvika fel på agentinstallationen använder du [version 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) eller senare för att generera kraschkonsekventa återställningspunkter och gör en manuell installation av VSS-leverantören.

## <a name="install-the-mobility-service-using-ui"></a>Installera mobilitetstjänsten med användargränssnittet

### <a name="prerequisites"></a>Krav

- Se till att alla serverkonfigurationer uppfyller kriterierna i [supportmatrisen för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).
- [Leta reda på installationsprogrammet](#locate-installer-files) för serverns operativsystem.

>[!IMPORTANT]
> Använd inte installationsmetoden för användargränssnittet om du replikerar en Azure Infrastructure as a Service (IaaS) VM från en Azure-region till en annan. Använd installationen av [kommandotolken.](#install-the-mobility-service-using-command-prompt)

1. Kopiera installationsfilen till datorn och kör den.
1. I **Installationsalternativ**väljer du **Installera mobilitetstjänst**.
1. Välj installationsplatsen och välj **Installera**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Installationssida för mobilitetstjänster.":::

1. Övervaka installationen under **installationen .** När installationen är klar väljer du **Fortsätt till konfigurationen** för att registrera tjänsten med konfigurationsservern.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Registreringssida för mobilitetstjänster.":::

1. I **Information om konfigurationsserver**anger du den IP-adress och lösenfras som du har konfigurerat.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Registreringssida för mobilitetstjänster.":::

1. Välj **Registrera för** att slutföra registreringen.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Slutsida för registrering av mobilitetstjänster.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Installera mobilitetstjänsten med kommandotolken

### <a name="prerequisites"></a>Krav

- Se till att alla serverkonfigurationer uppfyller kriterierna i [supportmatrisen för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).
- [Leta reda på installationsprogrammet](#locate-installer-files) för serverns operativsystem.

### <a name="windows-machine"></a>Windows-dator

- Från en kommandotolk kör du följande kommandon för att kopiera installationsprogrammet till en lokal mapp, till exempel _C:\Temp_, på den server som du vill skydda. Ersätt installationsprogrammets filnamn med det faktiska filnamnet.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Kör det här kommandot för att installera agenten.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Kör dessa kommandon för att registrera agenten med konfigurationsservern.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Installationsinställningar

Inställning | Information
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Installationsloggar | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmålet (MT) ska installeras.
`/InstallLocation`| Valfri parameter. Anger installationsplatsen för mobilitetstjänsten (valfri mapp).
`/Platform` | Obligatorisk. Anger den plattform där mobilitetstjänsten är installerad: <br/> **VMware** för virtuella VMware-datorer/fysiska servrar. <br/> **Virtuella Azure** för Virtuella Azure-datorer.<br/><br/> Om du behandlar virtuella Azure-datorer som fysiska datorer anger du **VMware**.
`/Silent`| Valfri. Anger om installationsprogrammet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar

Inställning | Information
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Agentkonfigurationsloggar | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Obligatorisk parameter. `<CSIP>`anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
`/PassphraseFilePath` |  Obligatorisk. Plats för lösenfrasen. Använd en giltig UNC- eller lokal filsökväg.

### <a name="linux-machine"></a>Linux-maskin

1. Från en terminalsession kopierar du installationsprogrammet till en lokal mapp, till exempel _/tmp_ på den server som du vill skydda. Ersätt installationsprogrammets filnamn med linuxdistributionens faktiska filnamn och kör sedan kommandona.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Installera enligt följande:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. När installationen är klar måste mobilitetstjänsten registreras på konfigurationsservern. Kör följande kommando för att registrera tjänsten Mobilitet med konfigurationsservern.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Installationsinställningar

Inställning | Information
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmålet (MT) ska installeras.
`-d` | Valfri parameter. Anger installationsplatsen för mobilitetstjänsten: `/usr/local/ASR`.
`-v` | Obligatorisk. Anger den plattform där mobilitetstjänsten är installerad. <br/> **VMware** för virtuella VMware-datorer/fysiska servrar. <br/> **Virtuella Azure** för Virtuella Azure-datorer.
`-q` | Valfri. Anger om installationsprogrammet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar

Inställning | Information
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Obligatorisk parameter. `<CSIP>`anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
`-P` |  Obligatorisk. Fullständig filsökväg för filen där lösenfrasen sparas. Använd vilken giltig mapp som helst.

## <a name="azure-virtual-machine-agent"></a>Azure-agent för virtuella datorer

- **Windows virtuella datorer:** Från version 9.7.0.0 av mobilitetstjänsten installeras [Azure VM-agenten](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) av installationsprogrammet för mobilitetstjänster. Detta säkerställer att när datorn växlar över till Azure, azure-vm uppfyller agentinstallation förutsättningen för att använda alla VM-tillägg.
- **Virtuella Linux-datorer:** [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) måste installeras manuellt på Den virtuella Azure-datorn efter redundans.

## <a name="locate-installer-files"></a>Leta upp installationsfiler

Gå till mappen _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Kontrollera vilken installationsprogram du behöver baserat på operativsystemet. I följande tabell sammanfattas installationsfilerna för varje virtuell dator med VMware och det fysiska serveroperativsystemet. Innan du börjar kan du granska operativsystemen [som stöds](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Filnamnen använder syntaxen som visas i följande tabell med _version_ och _datum_ som platshållare för de verkliga värdena. De faktiska filnamnen kommer att se ut ungefär som dessa exempel:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Installationsfil | Operativsystem (endast 64 bitars)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inkluderar SP2 och SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14,04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16,04 LTS-server
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation för mobilitetstjänsten](vmware-azure-install-mobility-service.md).
