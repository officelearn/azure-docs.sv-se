---
title: Om mobilitetstjänsten för haveriberedskap för virtuella datorer med VMware och fysiska servrar med Azure Site Recovery | Microsoft-dokument
description: Lär dig mer om Mobility Service-agenten för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256840"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitetstjänsten för virtuella datorer och fysiska servrar

När du ställer in haveriberedskap för virtuella datorer med VMware och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md)installerar du tjänsten Site Recovery Mobility på varje lokal virtuell dator för VMware och fysisk server.  Mobilitetstjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till site recovery-processservern. Du kan distribuera mobilitetstjänsten med följande metoder:

- [Push-installation:](#push-installation)Site Recovery installerar mobilitetsagent på servern när skyddet är aktiverat via Azure-portalen.
- Installera manuellt: Du kan installera mobilitetstjänsten manuellt på varje dator via [användargränssnittet](#install-mobility-agent-through-ui) eller [kommandotolken](#install-mobility-agent-through-command-prompt).
- [Automatisk distribution](vmware-azure-mobility-install-configuration-mgr.md): Du kan automatisera installationen med programdistributionsverktyg som Configuration Manager.

> [!NOTE]
> Mobilitetsagenten använder cirka 6%-10 % minne på källdatorer för virtuella datorer eller fysiska datorer.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus på replikerade maskiner

Om datorer som du vill replikera har aktiva antivirusprogram som körs kontrollerar du att du utesluter installationsmappen för mobilitetstjänsten från antivirusåtgärder (*C:\ProgramData\ASR\agent*). Detta säkerställer att replikeringen fungerar som förväntat.

## <a name="push-installation"></a>Push-installation

Push-installation är en integrerad del av "[Aktivera replikering](vmware-azure-enable-replication.md#enable-replication)" jobb som utlöses i portalen. När du har valt den uppsättning virtuella datorer som du vill skydda och utlösa "Aktivera replikering" skickar konfigurationsservern mobilitetsagenten till servrarna, installerar agenten och fullständig registrering av agent med konfigurationsserver. För att slutföra denna operation framgångsrikt

- Se till att alla [push-installationsförutsättningskrav](vmware-azure-install-mobility-service.md) är uppfyllda.
- Se till att alla konfigurationer av servrar faller under [stöd matris av VMware till Azure DR scenario](vmware-physical-azure-support-matrix.md).

Information om push-installationsarbetsflödet har beskrivits i följande avsnitt.

### <a name="from-923-version-onwards"></a>Från [9.23 version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) och framåt

Vid push-installation av mobilitetsagent utförs följande steg

1. Skjuter agenten på källmaskinen. Kopiering av agenten till källdator kan misslyckas på grund av flera miljöfel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installationsfel.
2. När agenten har kopierats till servern utförs nödvändiga kontroller på servern. Installationen misslyckas om en eller flera av [förutsättningarna](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla förutsättningar uppfylls utlöses installationen.
3. Azure Site Recovery VSS-providern installeras på servern som en del av installation av Mobilitetsagenter. Den här providern används för att generera konsekventa programpunkter. Om installationen av VSS-providern misslyckas hoppas det här steget över och agentinstallationen fortsätter.
4. Om agentinstallationen lyckas men VSS-providerinstallationen misslyckas markeras jobbstatus som "Varning". Detta påverkar inte krasch konsekvens punkter generation.

    a. För att generera konsekventa programpunkter, se [vår vägledning](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) för att slutföra installationen av Site Recovery VSS-leverantören manuellt. </br>
    b.  Om du inte vill att konsekventa programpunkter ska genereras [ändrar du replikeringsprincipen](vmware-azure-set-up-replication.md#create-a-policy) för att stänga av programmets konsekventa punkter.

### <a name="before-922-versions"></a>Före 9.22 versioner

1. Skjuter agenten på källmaskinen. Kopiering av agenten till källdator kan misslyckas på grund av flera miljöfel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installationsfel.
2. När agenten har kopierats till servern utförs nödvändiga kontroller på servern. Installationen misslyckas om en eller flera av [förutsättningarna](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla förutsättningar uppfylls utlöses installationen.
3. Azure Site Recovery VSS-providern installeras på servern som en del av installation av Mobilitetsagenter. Den här providern används för att generera konsekventa programpunkter. Om installationen av VSS-providern misslyckas misslyckas agentinstallationen. Använd [9.23-versionen](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) eller högre för att generera kraschkonsekventa punkter och installera VSS-providern manuellt för att undvika fel på installationen av mobilitetsagenten.

## <a name="install-mobility-agent-through-ui"></a>Installera mobilitetsagent via användargränssnittet

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar faller under [stöd matris av VMware till Azure DR scenario](vmware-physical-azure-support-matrix.md).
- [Leta reda på installationsprogrammet](#locate-installer-files) baserat på serverns operativsystem.

>[!IMPORTANT]
> Om du replikerar Azure IaaS VM från en Azure-region till en annan ska du inte använda den här metoden. Använd den kommandoradsbaserade installationsmetoden i stället.

1. Kopiera installationsfilen till datorn och kör den.
2. I **Installationsalternativ**väljer du **Installera mobilitetstjänst**.
3. Välj installationsplatsen > **Installera**.

    ![Installationssida för mobilitetstjänsten](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Övervaka installationen under **installationen .** När installationen är klar väljer du **Fortsätt till konfigurationen** för att registrera tjänsten med konfigurationsservern.

    ![Registreringssida för mobilitetstjänster](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. I **Information om konfigurationsserver**anger du den IP-adress och lösenfras som du har konfigurerat.

    ![Registreringssida för mobilitetstjänster](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Välj **Registrera för** att slutföra registreringen.

    ![Finalsida för registrering av mobilitetstjänster](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installera mobilitetsagent via kommandotolken

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar faller under [stöd matris av VMware till Azure DR scenario](vmware-physical-azure-support-matrix.md).
- [Leta reda på installationsprogrammet](#locate-installer-files) baserat på serverns operativsystem.

### <a name="on-a-windows-machine"></a>På en Windows-dator

- Kopiera installationsprogrammet till en lokal mapp (till exempel C:\Temp) på den server som du vill skydda.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Installera enligt följande:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registrera agenten med konfigurationsservern.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Installationsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgent.exe /Roll \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent
Installationsloggar | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Roll | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmålet (MT) ska installeras.
/InstallLocation| Valfri parameter. Anger installationsplatsen för mobilitetstjänsten (valfri mapp).
/Plattform | Obligatorisk. Anger den plattform där Mobilitetstjänsten är installerad. **VMware** för virtuella datorer/fysiska VMware-servrar. **Virtuella Azure** för Virtuella Azure-datorer.<br/><br/> Om du behandlar virtuella Azure-datorer som fysiska datorer anger du **VMware**.
/Silent| Valfri. Anger om installationsprogrammet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /LösphraseFilePath-lösenordfraserfilepath \<>
Agentkonfigurationsloggar | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Plats för lösenfrasen. Använd en giltig UNC- eller lokal filsökväg.

### <a name="on-a-linux-machine"></a>På en Linux-maskin

1. Kopiera installationsprogrammet till en lokal mapp (till exempel /tmp) på den server som du vill skydda. I en terminal kör du följande kommandon:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Installera enligt följande:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. När installationen är klar måste Mobilitetstjänsten registreras på konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Installationsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | ./install -d \<Installera plats> \<-r MS/MT> -v VmWare -q
-r | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmålet (MT) ska installeras.
-d. | Valfri parameter. Anger installationsplatsen för mobilitetstjänsten: /usr/local/ASR.
-v | Obligatorisk. Anger den plattform där Mobilitetstjänsten är installerad. **VMware** för virtuella datorer/fysiska VMware-servrar. **Virtuella Azure** för Virtuella Azure-datorer.
-q | Valfri. Anger om installationsprogrammet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | cd /usr/lokal/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<LösenordsfraseFilePath>
-i | Obligatorisk parameter. Anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
-P. |  Obligatorisk. Fullständig filsökväg för filen där lösenfrasen sparas. Använd vilken giltig mapp som helst.

## <a name="azure-virtual-machine-agent"></a>Azure-agent för virtuella datorer

- **Windows virtuella datorer:** Från version 9.7.0.0 av mobilitetstjänsten installeras [Azure VM-agenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent) av installationsprogrammet för mobilitetstjänster. Detta säkerställer att när datorn växlar över till Azure, azure-vm uppfyller agentinstallation förutsättningen för att använda alla Vm-tillägg.
- **Virtuella Linux-datorer:** [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) måste installeras manuellt på Den virtuella Azure-datorn efter redundans.

## <a name="locate-installer-files"></a>Leta upp installationsfiler

Gå till mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository på konfigurationsservern. Kontrollera vilken installationsprogram du behöver baserat på operativsystemet. I följande tabell sammanfattas installationsfilerna för varje virtuell dator med VMware och det fysiska serveroperativsystemet. Du kan granska [operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.

**Installationsfil** | **Operativsystem (endast 64 bitars)**
--- | ---
Microsoft-ASR\_\*UA\*Windows release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16,04 LTS-server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation för mobilitetstjänsten](vmware-azure-install-mobility-service.md).
