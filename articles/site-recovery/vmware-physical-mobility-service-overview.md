---
title: Om Mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Läs mer om mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 3b354492778426d1e3c31e53e277fd9be1e22c93
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048120"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitetstjänsten för VMware-datorer och fysiska servrar

När du ställer in katastrofåterställning för virtuella VMware-datorer och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md), installation av Site Recovery-mobilitetstjänsten på varje virtuell dator med en lokal VMware och fysiska servrar.  Mobilitetstjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern för Site Recovery. Du kan distribuera Mobilitetstjänsten med följande metoder:

- [Push-installation](#push-installation): Site Recovery installerar mobilitetsagenten på servern när skydd är aktiverat via Azure-portalen.
- Installera manuellt: Du kan installera mobilitetstjänsten manuellt på varje dator via [UI](#install-mobility-agent-through-ui) eller [kommandotolk](#install-mobility-agent-through-command-prompt).
- [Automatiserad distribution](vmware-azure-mobility-install-configuration-mgr.md): Du kan automatisera installationen med programdistributionsverktyg som System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Ett virusskyddsprogram på replikerade datorer

Om datorer som du vill replikera har aktiva antivirusprogram som körs, kontrollerar du exkluderar installationsmappen Mobility service från ett virusskyddsprogram åtgärder (*C:\ProgramData\ASR\agent*). Detta garanterar att replikeringen fungerar som förväntat.

## <a name="push-installation"></a>Push-installation

Push-installation är en del av ”[Aktivera replikering](vmware-azure-enable-replication.md#enable-replication)” jobbet utlöses i portalen. När du har valt uppsättning virtuella datorer som du vill skydda och utlösa ”Aktivera replikering” konfigurationsservern skickar mobilitetsagenten in på servrarna, installerar agenten och slutföra registreringen av agenten med konfigurationsservern. För den här åtgärden slutförs

- Se till att alla push-installation [krav](vmware-azure-install-mobility-service.md) är uppfyllda.
- Se till att alla konfigurationer av servrar som görs [supportmatris för VMware till Azure-katastrofåterställning](vmware-physical-azure-support-matrix.md).

Arbetsflöde för push-installation har varit beskrivs i följande avsnitt.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Från [9.23 version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) och senare

Följande steg utförs under push-installation av mobilitetsagenten

1. Agent som push-meddelanden på källdatorn. Kopierar agent in på källdatorn under installationen kan misslyckas på grund av flera fel i miljön. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) att felsöka fel vid push-installation.
2. När agenten har kopierats på till utförs de nödvändiga kontrollerna på servern. Installationen misslyckas om en eller flera av de [krav](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla krav är uppfyllda, utlöses installation.
3. Azure Site Recovery VSS-providern är installerad på servern som en del av Mobility agentinstallation. Den här providern används för att generera programmet konsekvent punkter. Om det inte går att installera VSS-providern, det här steget kommer att hoppas över och kommer att fortsätta installationen av agenten.
4. Om agentinstallationen lyckas men inte det går att installera VSS-providern, markeras jobbstatus som ”varning”. Detta påverkar inte krascher konsekvens punkter generation.

    a. Om du vill generera programmet konsekvent punkter, som avser [vår vägledning](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) att slutföra installationen av Site Recovery VSS-providern manuellt. </br>
    b.  Om du inte vill att programmet konsekvent punkter som ska genereras [ändra replikeringsprincipen](vmware-azure-set-up-replication.md#create-a-policy) att inaktivera programmet konsekvent punkter.

### <a name="before-922-versions"></a>Innan du 9.22 versioner

1. Agent som push-meddelanden på källdatorn. Kopierar agent in på källdatorn under installationen kan misslyckas på grund av flera fel i miljön. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) att felsöka fel vid push-installation.
2. När agenten har kopierats på till utförs de nödvändiga kontrollerna på servern. Installationen misslyckas om en eller flera av de [krav](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla krav är uppfyllda, utlöses installation.
3. Azure Site Recovery VSS-providern är installerad på servern som en del av Mobility agentinstallation. Den här providern används för att generera programmet konsekvent punkter. Om det inte går att installera VSS-providern, misslyckas installationen av agenten. För att undvika fel på mobility agentinstallation kan använda [9.23 version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) eller högre för att generera kraschkonsekvent punkter och installera VSS-providern manuellt.

## <a name="install-mobility-agent-through-ui"></a>Installera mobilitetsagenten via Användargränssnittet

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar som görs [supportmatris för VMware till Azure-katastrofåterställning](vmware-physical-azure-support-matrix.md).
- [Leta upp installationsprogrammet](#locate-installer-files) baserat på operativsystemet på servern.

>[!IMPORTANT]
> Använd inte den här metoden om du replikerar virtuella Azure IaaS-datorer från en Azure-region till en annan. Använd metoden kommandoradsbaserad Command-Line-baserad installation i stället.

1. Kopiera installationsfilen till datorn och köra den.
2. I **installationsalternativet**väljer **installation av mobilitetstjänsten**.
3. Välj installationsplats > **installera**.

    ![Installationssidan för alternativet på Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Övervaka installationen i **Installationsförlopp**. När installationen är klar, väljer **fortsätta konfigurationen** att registrera tjänsten med konfigurationsservern.

    ![Mobility Service-registreringssidan](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. i **information om konfigurationsservern**, ange IP-adressen och lösenfras som du har konfigurerat.  

    ![Mobility Service-registreringssidan](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Välj **registrera** att slutföra registreringen.

    ![Sista sidan Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installera mobilitetsagenten via Kommandotolken

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar som görs [supportmatris för VMware till Azure-katastrofåterställning](vmware-physical-azure-support-matrix.md).
- [Leta upp installationsprogrammet](#locate-installer-files) baserat på operativsystemet på servern.

### <a name="on-a-windows-machine"></a>På en Windows-dator

- Kopiera installationsprogrammet till en lokal mapp (exempelvis C:\Temp) på den server som du vill skydda.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Installera på följande sätt:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registrera agenten med konfigurationsservern.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Inställningar för klientinstallationer
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgent.exe/role < MS|MT > /InstallLocation  <Install Location> /Platform ”VmWare” / silent
Installationsloggar | Under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Role | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmål (MT) ska installeras.
/InstallLocation| Valfri parameter. Anger installationsplatsen för Mobility service (valfri mapp).
/ Plattform | Obligatorisk. Anger plattformen där Mobilitetstjänsten är installerad. **VMware** för VMware-datorer/fysiska servrar. **Azure** för virtuella Azure-datorer.
/ Tyst| Valfri. Anger om du vill köra installationsprogrammet i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Agenten konfigurationsloggar | Under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösenfrasen. Använd valfritt giltigt UNC eller lokal filsökväg.

### <a name="on-a-linux-machine"></a>På en Linux-dator

1. Kopiera installationsprogrammet till en lokal mapp (till exempel/tmp) på den server som du vill skydda. Kör följande kommandon i en terminal:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Installera på följande sätt:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. När installationen är klar, måste Mobilitetstjänsten vara registrerad till konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Inställningar för klientinstallationer
**Inställning** | **Detaljer**
--- | ---
Användning | . / install -d \<installationsplats > - r < MS|MT> -v VmWare -q
-r | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmål (MT) ska installeras.
-d | Valfri parameter. Anger installationsplatsen Mobility service: /usr/local/ASR.
-v | Obligatorisk. Anger plattformen där Mobilitetstjänsten är installerad. **VMware** för VMware-datorer/fysiska servrar. **Azure** för virtuella Azure-datorer.
-q | Valfri. Anger om du vill köra installationsprogrammet i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP > -P \<PassphraseFilePath >
-i | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
-P |  Obligatorisk. Fullständig sökväg till filen där lösenfrasen sparas. Använd en befintlig mapp.

## <a name="azure-virtual-machine-agent"></a>Azure VM-agenten

- **Virtuella Windows-datorer**: Från version 9.7.0.0 av mobilitetstjänsten, den [Azure VM-agenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent) installeras med Mobilitetstjänstens installationsprogram. Detta säkerställer att när datorn växlar till Azure, Azure VM uppfyller agentinstallationen som är nödvändiga för att använda alla Vm-tillägg.
- **Virtuella Linux-datorer**: Den [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) måste installeras manuellt på den virtuella Azure-datorn efter redundans.

## <a name="locate-installer-files"></a>Leta upp installer-filer

Gå till %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mapp på konfigurationsservern. Kontrollera vilka installationsprogrammet måste baserat på operativsystemet. I följande tabell sammanfattas installer-filer för varje VM VMware och fysiska server-operativsystem. Du kan granska [operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.

**Installationsfil** | **Operativsystem (endast 64-bitars)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation av mobilitetstjänsten](vmware-azure-install-mobility-service.md).
