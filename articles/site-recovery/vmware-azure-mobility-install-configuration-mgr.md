---
title: Automatisera mobilitetstjänsten för haveriberedskap av installation i Azure Site Recovery
description: Så här installerar du automatiskt mobilitetstjänsten för VMware/physical server disaster recovery med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252235"
---
# <a name="automate-mobility-service-installation"></a>Automatisera installation av mobilitetstjänster

I den här artikeln beskrivs hur du automatiserar installation och uppdateringar för Mobility Service-agenten i [Azure Site Recovery](site-recovery-overview.md).

När du distribuerar Platsåterställning för haveriberedskap av lokala virtuella datorer och fysiska servrar till Azure installerar du Mobility Service-agenten på varje dator som du vill replikera. Mobilitetstjänsten samlar in dataskrivningar på datorn och vidarebefordrar dem till site recovery-processservern för replikering. Du kan distribuera mobilitetstjänsten på några olika sätt:

- **Push-installation:** Låt Site Recovery installera Mobility-serviceagenten när du aktiverar replikering för en dator i Azure-portalen.
- **Manuell installation**: Installera mobilitetstjänsten manuellt på varje maskin. [Läs mer](vmware-physical-mobility-service-overview.md) om push och manuell installation.
- **Automatiserad distribution**: Automatisera installationen med programdistributionsverktyg som Microsoft Endpoint Configuration Manager eller verktyg från tredje part som JetPatch.

Automatisk installation och uppdatering ger en lösning om:

- Din organisation tillåter inte push-installation på skyddade servrar.
- Företagets policy kräver att lösenord ändras med jämna mellanrum. Du måste ange ett lösenord för push-installationen.
- Din säkerhet princip tillåter inte att lägga till brandväggsund undantag för specifika datorer.
- Du fungerar som en värdtjänstleverantör och vill inte tillhandahålla kundmaskinsautentiseringsuppgifter som behövs för push-installation med Site Recovery.
- Du måste skala agentinstallationer till många servrar samtidigt.
- Du vill schemalägga installationer och uppgraderingar under planerade underhållsfönster.

## <a name="prerequisites"></a>Krav

För att automatisera installationen behöver du följande:

- En distribuerad programvaruinstallationslösning som [Configuration Manager](/configmgr/) eller [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Distributionsförutsättningar på plats i [Azure](tutorial-prepare-azure.md) och lokalt för VMware-haveriberedskap eller för [fysisk serverkatastrofåterställning.](physical-azure-disaster-recovery.md) [on-premises](vmware-azure-tutorial-prepare-on-premises.md) Granska [supportkraven](vmware-physical-azure-support-matrix.md) för haveriberedskap.

## <a name="prepare-for-automated-deployment"></a>Förbered för automatiserad distribution

I följande tabell sammanfattas verktyg och processer för automatisering av distribution av Mobilitetstjänster.

**Verktyg** | **Detaljer** | **Instruktioner**
--- | --- | ---
**Configuration Manager** | 1. Kontrollera att du har [förutsättningarna](#prerequisites) ovan på plats. <br/><br/> 2. Distribuera haveriberedskap genom att konfigurera källmiljön, inklusive att hämta en OVA-fil för att distribuera konfigurationsservern för platsåterställning som en virtuell VMware-dator med hjälp av en OVF-mall.<br/><br/> 3. Du registrerar konfigurationsservern med tjänsten Site Recovery, ställer in Azure-miljön för målet och konfigurerar en replikeringsprincip.<br/><br/> 4. För automatisk mobilitetstjänstdistribution skapar du en nätverksresurs som innehåller installationsfilerna för konfigurationsserver och Mobilitetstjänsten.<br/><br/> 5. Du skapar ett Configuration Manager-paket som innehåller installationen eller uppdateringarna och förbereder för Mobility Service-distributionen.<br/><br/> 6. Du kan sedan aktivera replikering till Azure för de datorer som har mobilitetstjänsten installerad. | [Automatisera med Configuration Manager](#automate-with-configuration-manager)
**JetPatch (vatten)** | 1. Kontrollera att du har [förutsättningarna](#prerequisites) ovan på plats. <br/><br/> 2. Distribuera haveriberedskap genom att konfigurera källmiljön, inklusive nedladdning och distribution av JetPatch Agent Manager för Azure Site Recovery i site recovery-miljön, med hjälp av en OVF-mall.<br/><br/> 3. Du registrerar konfigurationsservern med Site Recovery, ställer in Azure-miljön för målet och konfigurerar en replikeringsprincip.<br/><br/> 4. För automatisk distribution, initiera och slutföra JetPatch Agent Manager-konfigurationen.<br/><br/> 5. I JetPatch kan du skapa en policy för webbplatsåterställning för att automatisera distribution och uppgradering av Mobility Service-agenten. <br/><br/> 6. Du kan sedan aktivera replikering till Azure för de datorer som har mobilitetstjänsten installerad. | [Automatisera med JetPatch Agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Felsöka agentinstallation i JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatisera med Configuration Manager

### <a name="prepare-the-installation-files"></a>Förbereda installationsfilerna

1. Se till att du har förutsättningarna på plats.
1. Skapa en säker nätverksfilresurs (SMB-resurs) som kan nås av datorn som kör konfigurationsservern.
1. Kategorisera de [servrar](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) som du vill installera eller uppdatera mobilitetstjänsten på Configuration Manager. En samling bör innehålla alla Windows-servrar, den andra alla Linux-servrar.
1. Skapa en mapp på nätverksresursen:

   - För installation på Windows-datorer, skapa en mapp med namnet _MobSvcWindows_.
   - För installation på Linux-maskiner, skapa en mapp som heter _MobSvcLinux_.

1. Logga in på konfigurationsservermaskinen.
1. Öppna en administrativ kommandotolk på konfigurationsserverdatorn.
1. Om du vill generera lösenfrasfilen kör du det här kommandot:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Kopiera _filen MobSvc.passphrase_ till Windows-mappen och Linux-mappen.
1. Om du vill bläddra till mappen som innehåller installationsfilerna kör du det här kommandot:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Kopiera dessa installationsfiler till nätverksresursen:

   - För Windows kopierar du _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ till _MobSvcWindows_.
   - För Linux, kopiera följande filer till _MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Som beskrivs i följande procedurer kopierar du koden till Windows- eller Linux-mapparna. Vi antar att:

   - Konfigurationsserverns IP-adress `192.168.3.121`är .
   - Den säkra nätverksfilresursen är `\\ContosoSecureFS\MobilityServiceInstallers`.

### <a name="copy-code-to-the-windows-folder"></a>Kopiera kod till Windows-mappen

Kopiera följande kod:

- Spara koden i _mappen MobSvcWindows_ som _install.bat_.
- Ersätt `[CSIP]` platshållarna i det här skriptet med de faktiska värdena för konfigurationsserverns IP-adress.
- Skriptet stöder nya installationer av Mobility Service-agenten och uppdateringar av agenter som redan är installerade.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kopiera kod till Linux-mappen

Kopiera följande kod:

- Spara koden i _mappen MobSvcLinux_ som _install_linux.sh_.
- Ersätt `[CSIP]` platshållarna i det här skriptet med de faktiska värdena för konfigurationsserverns IP-adress.
- Skriptet stöder nya installationer av Mobility Service-agenten och uppdateringar av agenter som redan är installerade.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Skapa ett paket

1. Logga in på Configuration Manager-konsolen och gå till > **programhanteringspaket** > **Packages** **för program**.
1. Högerklicka på **Paket** > **Skapa paket**.
1. Ange paketinformation, inklusive namn, beskrivning, tillverkare, språk och version.
1. Välj **Det här paketet innehåller källfiler**.
1. Klicka på **Bläddra**och välj den nätverksresurs och mapp som innehåller relevant installationsprogrammet (_MobSvcWindows_ eller _MobSvcLinux_). Välj sedan **Nästa**.

   ![Skärmbild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Välj **Standardprogram** > **nästa**i Välj **den programtyp som du vill skapa.**

   ![Skärmbild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Ange **information om den här standardprogramsidan i Ange information om den här standardprogramsidan:**

    **Parametern** | **Windows-värde** | **Linux-värde**
    --- | --- | ---
    **Namn** | Installera Microsoft Azure Mobility Service (Windows) | Installera Microsoft Azure Mobility Service (Linux).
    **Kommandoraden** | install.bat | ./install_linux.sh
    **Programmet kan köras** | Om en användare är inloggad eller inte | Om en användare är inloggad eller inte
    **Andra parametrar** | Använd standardinställning | Använd standardinställning

   ![Skärmbild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Gör följande **i Ange kraven för det här standardprogrammet:**

   - För Windows-datorer väljer du **Det här programmet kan bara köras på angivna plattformar**. Välj sedan de [Windows-operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) och välj **Nästa**.
   - För Linux-maskiner väljer du **Det här programmet kan köras på vilken plattform som helst.** Välj sedan **Nästa**.

1. Slutför guiden.

### <a name="deploy-the-package"></a>Distribuera paketet

1. Högerklicka på paketet i Configuration Manager-konsolen och välj **Distribuera innehåll**.

   ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Välj de distributionsplatser där paketen ska kopieras. [Läs mer](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Slutför guiden. Paketet börjar sedan replikera till de angivna distributionsplatserna.
1. När paketdistributionen är klar högerklickar du på paketet > **Deploy**.

   ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Välj den Windows- eller Linux-enhetssamling som du skapade tidigare.
1. Välj **Distributionsplatser**på sidan **Ange innehållsmål** .
1. Ange **inställningar för att styra hur den här programvaran distribueras** sida, ange **Syfte** till **Obligatorisk .**

   ![Skärmbild av guiden Distribuera programvara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Ställ in ett schema i **Ange schemat för den här distributionen.** [Läs mer](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Mobilitetstjänsten installeras i enlighet med det schema du anger.
   - Om du vill undvika onödiga omstarter schemalägger du paketinstallationen under det månatliga underhållsfönstret eller programuppdateringsfönstret.

1. Konfigurera inställningar och slutför guiden på sidan **Distributionspunkter.**
1. Övervaka driftsättningsstatus i Configuration Manager-konsolen. Gå till **Övervaka** > **distributioner** > _\<ditt paketnamn\>_.

### <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitetstjänsten

Du kan skapa Configuration Manager-paket för att avinstallera mobilitetstjänsten. Följande skript avinstallerar till exempel mobilitetstjänsten:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Nästa steg

[Aktivera replikering](vmware-azure-enable-replication.md) för virtuella datorer.
