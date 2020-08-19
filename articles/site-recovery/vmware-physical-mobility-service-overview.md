---
title: Om mobilitets tjänsten för haveri beredskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om mobilitets tjänst agenten för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av tjänsten Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 431f1da463e4bd9970bc92b0842393f2de882220
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604730"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitets tjänsten för virtuella VMware-datorer och fysiska servrar

När du konfigurerar haveri beredskap för virtuella VMware-datorer (VM) och fysiska servrar med hjälp av [Azure Site Recovery](site-recovery-overview.md)installerar du tjänsten Site Recovery mobilitet på varje lokal virtuell VMware-dator och fysisk server. Mobilitets tjänsten samlar in data skrivningar på datorn och vidarebefordrar dem till Site Recovery processerver. Mobilitets tjänsten installeras av Mobility Service Agent-programvaran som du kan distribuera med följande metoder:

- [Push-installation](#push-installation): När skydd är aktiverat via Azure Portal, installerar Site Recovery mobilitets tjänsten på servern.
- Manuell installation: du kan installera mobilitets tjänsten manuellt på varje dator via [användar gränssnittet (UI)](#install-the-mobility-service-using-ui) eller [kommando tolken](#install-the-mobility-service-using-command-prompt).
- [Automatiserad distribution](vmware-azure-mobility-install-configuration-mgr.md): du kan automatisera mobilitets tjänst installationen med verktyg för program varu distribution, till exempel Configuration Manager.

> [!NOTE]
> Mobilitets tjänsten använder cirka 6%-10% av minnet på käll datorerna för virtuella VMware-datorer eller fysiska datorer.

## <a name="antivirus-on-replicated-machines"></a>Antivirus program på replikerade datorer

Om datorer som du vill replikera kör antivirus program, utesluter du mobilitets tjänstens installationsmapp _C:\ProgramData\ASR\agent_ från Antivirus åtgärder. Detta undantag säkerställer att replikeringen fungerar som förväntat.

## <a name="push-installation"></a>Push-installation

Push-installation är en integrerad del av jobbet som körs från Azure Portal för att [Aktivera replikering](vmware-azure-enable-replication.md#enable-replication). När du har valt den uppsättning med virtuella datorer som du vill skydda och aktivera replikering, skickar konfigurations servern mobilitets tjänst agenten till servrarna, installerar agenten och slutför agentens registrering med konfigurations servern.

### <a name="prerequisites"></a>Förutsättningar

- Se till att alla [krav](vmware-azure-install-mobility-service.md) för push-installation är uppfyllda.
- Se till att alla serverkonfigurationer uppfyller kriterierna i [support mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).
- Från, [9,36-versionen](https://support.microsoft.com/help/4578241/) , för SUSE Linux Enterprise Server 11 SP4, se till att det senaste installations programmet är [tillgängligt på konfigurations servern och den skalbara processervern](#download-latest-mobility-agent-installer-for-suse-11-sp3-server)

Arbets flödet för push-installation beskrivs i följande avsnitt:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobilitets tjänst agent version 9,23 och senare

Mer information om version 9,23 finns i samlad [uppdatering 35 för Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Under en push-installation av mobilitets tjänsten utförs följande steg:

1. Agenten skickas till käll datorn. Det går inte att kopiera agenten till käll datorn på grund av flera miljö fel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installations fel.
1. När agenten har kopierats till servern utförs en krav kontroll på servern.
   - Om alla krav är uppfyllda börjar installationen.
   - Installationen Miss lyckas om en eller flera av [kraven](vmware-physical-azure-support-matrix.md) inte uppfylls.
1. Som en del av Agent installationen installeras tjänsten Volume Shadow Copy-providern (VSS) för Azure Site Recovery. VSS-providern används för att generera programkonsekventa återställnings punkter. Om installationen av VSS-providern Miss lyckas hoppas det här steget över och Agent installationen fortsätter.
1. Om installationen av agenten lyckas men installationen av VSS-providern Miss lyckas, markeras jobb statusen som **Varning**. Detta påverkar inte genereringen av krascha återställnings punkter.

    - Information om hur du skapar programkonsekventa återställnings punkter finns i [rikt linjerna](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) för att slutföra en manuell installation av Site Recovery VSS-providern.
    - Om du inte vill generera programkonsekventa återställnings punkter [ändrar du replikeringsprincipen](vmware-azure-set-up-replication.md#create-a-policy) för att inaktivera programkonsekventa återställnings punkter.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobilitets tjänst agent version 9,22 och nedåt

1. Agenten skickas till käll datorn. Det går inte att kopiera agenten till käll datorn på grund av flera miljö fel. Se [våra rikt linjer](vmware-azure-troubleshoot-push-install.md) för fel sökning av push-installations fel.
1. När agenten har kopierats till servern utförs en krav kontroll på servern.
   - Om alla krav är uppfyllda börjar installationen.
   - Installationen Miss lyckas om en eller flera av [kraven](vmware-physical-azure-support-matrix.md) inte uppfylls.

1. Som en del av Agent installationen installeras tjänsten Volume Shadow Copy-providern (VSS) för Azure Site Recovery. VSS-providern används för att generera programkonsekventa återställnings punkter.
   - Om installationen av VSS-providern Miss lyckas kommer Agent installationen att Miss lyckas. Du kan undvika att Agent installationen Miss lyckas genom att använda [version 9,23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) eller senare för att generera kraschbaserade återställnings punkter och göra en manuell installation av VSS-providern.

## <a name="install-the-mobility-service-using-ui"></a>Installera mobilitets tjänsten med hjälp av användar gränssnittet

### <a name="prerequisites"></a>Förutsättningar

- Se till att alla serverkonfigurationer uppfyller kriterierna i [support mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).
- [Leta upp installations programmet](#locate-installer-files) för serverns operativ system.

>[!IMPORTANT]
> Använd inte installations metoden för användar gränssnittet om du replikerar en virtuell IaaS-dator (Azure Infrastructure as a Service) från en Azure-region till en annan. Använd [kommando tolks](#install-the-mobility-service-using-command-prompt) installationen.

1. Kopiera installations filen till datorn och kör den.
1. I **installations alternativ**väljer du **Installera mobilitets tjänsten**.
1. Välj installations plats och välj **Installera**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Sidan installations alternativ för mobilitets tjänsten.":::

1. Övervaka installationen i **installations förloppet**. När installationen är färdig väljer du **Fortsätt till konfiguration** för att registrera tjänsten med konfigurations servern.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Registrerings sidan för mobilitets tjänsten.":::

1. I **konfigurations Server information**anger du den IP-adress och den lösen fras som du har konfigurerat.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Registrerings sidan för mobilitets tjänsten.":::

1. Välj **Registrera** för att slutföra registreringen.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Den sista sidan för mobilitets tjänst registrering.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Installera mobilitets tjänsten med hjälp av kommando tolken

### <a name="prerequisites"></a>Förutsättningar

- Se till att alla serverkonfigurationer uppfyller kriterierna i [support mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure](vmware-physical-azure-support-matrix.md).
- [Leta upp installations programmet](#locate-installer-files) för serverns operativ system.

### <a name="windows-machine"></a>Windows-dator

- Från en kommando tolk kör du följande kommandon för att kopiera installations programmet till en lokal mapp, till exempel _C:\Temp_, på den server som du vill skydda. Ersätt installations programmets fil namn med det faktiska fil namnet.

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

- Kör dessa kommandon för att registrera agenten med konfigurations servern.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Installations inställningar

Inställningen | Information
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Installationsloggar | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Obligatorisk installations parameter. Anger om mobilitets tjänsten (MS) eller huvud målet (MT) ska installeras.
`/InstallLocation`| Valfri parameter. Anger mobilitets tjänstens installations plats (valfri mapp).
`/Platform` | Obligatorisk. Anger den plattform där mobilitets tjänsten är installerad: <br/> **VMware** för virtuella VMware-datorer/fysiska servrar. <br/> Virtuella **Azure** -datorer i Azure.<br/><br/> Om du hanterar virtuella Azure-datorer som fysiska datorer anger du **VMware**.
`/Silent`| Valfritt. Anger om installations programmet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registrerings inställningar

Inställningen | Information
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Agent konfigurations loggar | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Obligatorisk parameter. `<CSIP>` anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
`/PassphraseFilePath` |  Obligatorisk. Plats för lösen frasen. Använd en giltig UNC-eller lokal fil Sök väg.

### <a name="linux-machine"></a>Linux-dator

1. Från en terminalsession kopierar du installations programmet till en lokal mapp, till exempel _katalogen/tmp_ på den server som du vill skydda. Ersätt installations programmets fil namn med Linux-distributionens faktiska fil namn och kör sedan kommandona.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Installera enligt följande:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. När installationen är klar måste mobilitets tjänsten vara registrerad på konfigurations servern. Kör följande kommando för att registrera mobilitets tjänsten med konfigurations servern.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Installations inställningar

Inställningen | Information
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Obligatorisk installations parameter. Anger om mobilitets tjänsten (MS) eller huvud målet (MT) ska installeras.
`-d` | Valfri parameter. Anger mobilitets tjänstens installations plats: `/usr/local/ASR` .
`-v` | Obligatorisk. Anger den plattform där mobilitets tjänsten är installerad. <br/> **VMware** för virtuella VMware-datorer/fysiska servrar. <br/> Virtuella **Azure** -datorer i Azure.
`-q` | Valfritt. Anger om installations programmet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registrerings inställningar

Inställningen | Information
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Obligatorisk parameter. `<CSIP>` anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
`-P` |  Obligatorisk. Fullständig sökväg till filen där lösen frasen sparas. Använd en giltig mapp.

## <a name="azure-virtual-machine-agent"></a>Agent för virtuell Azure-dator

- **Virtuella Windows-datorer**: från 9.7.0.0 av mobilitets tjänsten installeras [Azure VM-agenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent) av mobilitets tjänstens installations program. Detta säkerställer att den virtuella Azure-datorn uppfyller kraven för agent installation för att använda alla VM-tillägg när datorn växlar över till Azure.
- **Virtuella Linux-datorer**:  [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) måste installeras manuellt på den virtuella Azure-datorn efter redundansväxlingen.

## <a name="locate-installer-files"></a>Hitta installationsfiler

Gå till mappen _%programdata%\ASR\home\svsystems\pushinstallsvc\repository_på konfigurations servern. Kontrol lera vilka installations program du behöver baserat på operativ systemet. I följande tabell sammanfattas installationsfilerna för varje virtuell VMware-dator och operativ system för fysiska servrar. Innan du börjar kan du granska de [operativ system som stöds](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Fil namnen använder den syntax som visas i följande tabell med _version_ och _datum_ som plats hållare för de verkliga värdena. De faktiska fil namnen kommer att se ut ungefär som i följande exempel:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Installations fil | Operativ system (endast 64-bitars)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Innehåller SP2 och SP3.
[Ska laddas ned och placeras i den här mappen manuellt](#download-latest-mobility-agent-installer-for-suse-11-sp3-server). | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14,04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16,04 LTS-Server
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

### <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-server"></a>Hämta de senaste installations program för mobilitets agenten för SUSE 11 SP3-Server

Som ett **krav för att uppdatera eller skydda SUSE Linux Enterprise Server 11 SP3-datorer** från [9,36-versionen](https://support.microsoft.com/help/4578241/) och senare:

1. Se till att det senaste installations programmet för mobilitets agenten har laddats ned från Microsoft Download Center och placerats i push Installer-lagringsplatsen på konfigurations servern
2. [Hämta](https://download.microsoft.com/download/0/3/4/0341b388-1ff5-4ead-b197-7cf6d2bb3e40/Microsoft-ASR_UA_9.36.0.0_SLES11-SP3-64_GA_06Aug2020_release.tar.gz) installations programmet för SUSE Linux Enterprise Server 11 SP3-agenten.
3. Gå till konfigurations servern, kopiera installations programmet för SUSE Linux Enterprise Server 11 SP3-agenten på följande sökvägar
    1. INSTALL_DIR \home\svsystems\pushinstallsvc\repository
    1.  INSTALL_DIR \home\svsystems\admin\web\sw-mappar
4. Navigera nu till associerade skalbara process servrar & kopiera installations programmet i båda Sök vägarna som nämns i tredje steget.
5. Om **till exempel**installations Sök väg är C:\Program Files (x86) \Microsoft Azure Site Recovery, kommer ovanstående kataloger att
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\admin\web\sw-sökväg

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation för mobilitets tjänsten](vmware-azure-install-mobility-service.md).
