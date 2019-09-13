---
title: Om mobilitets tjänsten för haveri beredskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om mobilitets tjänst agenten för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av tjänsten Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: aeb00b84ac254232e0d68fd9631fb539a928e67d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931895"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitets tjänsten för virtuella VMware-datorer och fysiska servrar

När du konfigurerar haveri beredskap för virtuella VMware-datorer och fysiska servrar med hjälp av [Azure Site Recovery](site-recovery-overview.md)installerar du Site Recovery mobilitets tjänsten på varje lokal virtuell VMware-dator och fysisk server.  Mobilitets tjänsten samlar in data skrivningar på datorn och vidarebefordrar dem till Site Recovery processerver. Du kan distribuera mobilitets tjänsten på följande sätt:

- [Push-installation](#push-installation): Site Recovery installerar mobilitets agenten på servern när skyddet är aktiverat via Azure Portal.
- Installera manuellt: Du kan installera mobilitets tjänsten manuellt på varje dator via [UI](#install-mobility-agent-through-ui) eller [kommando tolken](#install-mobility-agent-through-command-prompt).
- [Automatiserad distribution](vmware-azure-mobility-install-configuration-mgr.md): Du kan automatisera installationen med verktyg för program varu distribution, till exempel System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Virus skydd på replikerade datorer

Om datorer som du vill replikera har ett aktivt antivirus program som kör, se till att du undantar installationsmappen för mobilitets tjänsten från anti-virus-åtgärder (*C:\ProgramData\ASR\agent*). Detta säkerställer att replikeringen fungerar som förväntat.

## <a name="push-installation"></a>Push-installation

Push-installation är en integrerad del av jobbet "[Aktivera replikering](vmware-azure-enable-replication.md#enable-replication)" som utlöses i portalen. När du har valt uppsättningen med virtuella datorer som du vill skydda och utlösa "Aktivera replikering" skickar konfigurations servern mobilitets agenten till servrarna, installerar agenten och slutför registreringen av agenten med konfigurations servern. För att åtgärden ska slutföras klart

- Se till att alla [krav](vmware-azure-install-mobility-service.md) för push-installation är uppfyllda.
- Se till att alla konfigurationer av servrar omfattas [av support mat ris för VMware till Azure Dr-scenario](vmware-physical-azure-support-matrix.md).

Information om arbets flödet för push-installation har beskrivits i följande avsnitt.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Från och med [9,23-version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Följande steg utförs under en push-installation av mobilitets agenten

1. Push-överför agenten till käll datorn. Det går inte att kopiera agenten till käll datorn på grund av flera miljö fel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installations fel.
2. När agenten har kopierats till krav kontrollerna för servern utförs på servern. Installationen Miss lyckas om en eller flera av [kraven](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla krav är uppfyllda utlöses installationen.
3. Azure Site Recovery VSS-providern är installerad på servern som en del av installationen av mobilitets agenten. Den här providern används för att generera program konsekventa punkter. Om installationen av VSS-providern Miss lyckas kommer det här steget att hoppas över och Agent installationen fortsätter.
4. Om installationen av agenten lyckas men installationen av VSS-providern Miss lyckas, markeras jobb status som "varning". Detta påverkar inte genereringen av krasch konsekvens punkter.

    a. Information om hur du genererar program konsekventa punkter finns i [rikt linjerna](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) för att slutföra installationen av Site Recovery VSS-providern manuellt. </br>
    b.  Om du inte vill att programkonsekventa punkter ska genereras, [ändra replikeringsprincipen](vmware-azure-set-up-replication.md#create-a-policy) för att inaktivera program konsekventa punkter.

### <a name="before-922-versions"></a>Före 9,22 versioner

1. Push-överför agenten till käll datorn. Det går inte att kopiera agenten till käll datorn på grund av flera miljö fel. Besök [vår vägledning](vmware-azure-troubleshoot-push-install.md) för att felsöka push-installations fel.
2. När agenten har kopierats till krav kontrollerna för servern utförs på servern. Installationen Miss lyckas om en eller flera av [kraven](vmware-physical-azure-support-matrix.md) inte uppfylls. Om alla krav är uppfyllda utlöses installationen.
3. Azure Site Recovery VSS-providern är installerad på servern som en del av installationen av mobilitets agenten. Den här providern används för att generera program konsekventa punkter. Om installationen av VSS-providern Miss lyckas kommer Agent installationen att Miss lyckas. Undvik att installera mobilitets agenten genom att använda [9,23-versionen](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) eller högre för att skapa krasch konsekventa punkter och installera VSS-providern manuellt.

## <a name="install-mobility-agent-through-ui"></a>Installera mobilitets agenten via användar gränssnittet

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar omfattas [av support mat ris för VMware till Azure Dr-scenario](vmware-physical-azure-support-matrix.md).
- [Leta upp installations programmet](#locate-installer-files) baserat på serverns operativ system.

>[!IMPORTANT]
> Använd inte den här metoden om du replikerar en virtuell Azure IaaS-dator från en Azure-region till en annan. Använd den kommando radsbaserade installations metoden i stället.

1. Kopiera installations filen till datorn och kör den.
2. I **installations alternativ**väljer du **Installera mobilitets tjänsten**.
3. Välj installations plats > **Installera**.

    ![Sidan installations alternativ för mobilitets tjänsten](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Övervaka installationen i **installations förloppet**. När installationen är färdig väljer du **Fortsätt till konfiguration** för att registrera tjänsten med konfigurations servern.

    ![Registrerings sida för mobilitets tjänsten](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. I **konfigurations Server information**anger du den IP-adress och den lösen fras som du har konfigurerat.  

    ![Registrerings sida för mobilitets tjänsten](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Välj **Registrera** för att slutföra registreringen.

    ![Slut sida för mobilitets tjänst registrering](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installera mobilitets agenten via kommando tolken

### <a name="prerequisite"></a>Krav

- Se till att alla konfigurationer av servrar omfattas [av support mat ris för VMware till Azure Dr-scenario](vmware-physical-azure-support-matrix.md).
- [Leta upp installations programmet](#locate-installer-files) baserat på serverns operativ system.

### <a name="on-a-windows-machine"></a>På en Windows-dator

- Kopiera installations programmet till en lokal mapp (till exempel C:\Temp) på den server som du vill skydda.

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

- Registrera agenten på konfigurations servern.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Installations inställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgent. exe/Role \<MS/MT >/InstallLocation \<installation location >/Platform "VMware"/Silent
Installations loggar | Under%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Obligatorisk installations parameter. Anger om mobilitets tjänsten (MS) eller huvud målet (MT) ska installeras.
/InstallLocation| Valfri parameter. Anger mobilitets tjänstens installations plats (valfri mapp).
/Platform | Obligatorisk. Anger den plattform där mobilitets tjänsten är installerad. **VMware** för virtuella VMware-datorer/fysiska servrar; Virtuella **Azure** -datorer i Azure.<br/><br/> Om du hanterar virtuella Azure-datorer som fysiska datorer anger du **VMware**.
/Silent| Valfritt. Anger om installations programmet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registrerings inställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Agent konfigurations loggar | Under%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Plats för lösen frasen. Använd en giltig UNC-eller lokal fil Sök väg.

### <a name="on-a-linux-machine"></a>På en Linux-dator

1. Kopiera installations programmet till en lokal mapp (till exempel katalogen/tmp) på den server som du vill skydda. I en Terminal kör du följande kommandon:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Installera enligt följande:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. När installationen är klar måste mobilitets tjänsten registreras på konfigurations servern. Kör följande kommando för att registrera mobilitets tjänsten med konfigurations servern:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Installations inställningar
**Inställning** | **Detaljer**
--- | ---
Användning | ./install-d \<installations plats >-r \<MS/MT >-v VMware-q
-r | Obligatorisk installations parameter. Anger om mobilitets tjänsten (MS) eller huvud målet (MT) ska installeras.
d, DDD | Valfri parameter. Anger mobilitets tjänstens installations plats:/usr/local/ASR.
-v | Obligatorisk. Anger den plattform där mobilitets tjänsten är installerad. **VMware** för virtuella VMware-datorer/fysiska servrar; Virtuella **Azure** -datorer i Azure.
-q | Valfritt. Anger om installations programmet ska köras i tyst läge.

#### <a name="registration-settings"></a>Registrerings inställningar
**Inställning** | **Detaljer**
--- | ---
Användning | cd-/usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Obligatorisk parameter. Anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
-P |  Obligatorisk. Fullständig sökväg till filen där lösen frasen sparas. Använd en giltig mapp.

## <a name="azure-virtual-machine-agent"></a>Agent för virtuell Azure-dator

- **Virtuella Windows-datorer**: Från version 9.7.0.0 av mobilitets tjänsten installeras [Azure VM](../virtual-machines/extensions/features-windows.md#azure-vm-agent) -agenten av mobilitets tjänstens installations program. Detta säkerställer att den virtuella Azure-datorn uppfyller kraven för agent installation för att använda alla VM-tillägg när datorn växlar över till Azure.
- **Virtuella Linux-datorer**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) måste installeras manuellt på den virtuella Azure-datorn efter redundansväxlingen.

## <a name="locate-installer-files"></a>Hitta installationsfiler

Gå till mappen%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository på konfigurations servern. Kontrol lera vilka installations program du behöver baserat på operativ system. I följande tabell sammanfattas installationsfilerna för varje virtuell VMware-dator och operativ system för fysiska servrar. Du kan granska [operativ system som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.

**Installations fil** | **Operativ system (endast 64-bitars)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*version. tar. gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*Ubuntu-14.04-64\*version. tar. gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16,04 LTS-Server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation för mobilitets tjänsten](vmware-azure-install-mobility-service.md).
