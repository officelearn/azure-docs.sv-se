---
title: Automatisera installation av Mobilitetstjänsten för Azure Site Recovery med System Center Configuration Manager | Microsoft Docs
description: Den här artikeln hjälper dig att automatisera installation av Mobilitetstjänsten med hjälp av System Center Configuration Manager.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 8382fadc02a7e80b6f28bd777f423013aed9add3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatisera installation av Mobilitetstjänsten med System Center Configuration Manager

Mobilitetstjänsten har installerats på virtuella VMware-datorer och fysiska servrar som du vill replikera till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md)

Den här artikeln innehåller ett exempel på hur du kan använda System Center Configuration Manager för att distribuera Azure Site Recovery Mobility-tjänsten på en VMware-VM. Med ett verktyg för distribution av programvara som Configuration Manager har följande fördelar:

* Schemalägga nya installationer och uppgraderingar under planerat underhållsfönstret för programuppdateringar
* Skala distributionen till hundratals servrar samtidigt

Den här artikeln använder System Center Configuration Manager 2012 R2 för att demonstrera aktiviteten distribution. Vi förutsätter att du använder version **9.9.4510.1** eller senare av mobilitetstjänsten.

Alternativt kan du automatisera installation av Mobilitetstjänsten med [Azure Automation DSC ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Förutsättningar

1. Ett program distributionsverktyg, som Configuration Manager som redan har distribuerats i din miljö.
2. Du bör skapa två [enhetssamlingar](https://technet.microsoft.com/library/gg682169.aspx), en för alla **Windows-servrar**, och en annan för alla **Linux-servrar**, som du vill skydda med Site Recovery.
3. Konfigurationsservern som redan har registrerats i Recovery Services-valvet.
4. En säker nätverksfilresurs (SMB-resurs) som kan användas av configuration manager-dator.

## <a name="deploy-on-windows-machines"></a>Distribuera på Windows-datorer
> [!NOTE]
> Den här artikeln förutsätter att IP-adressen för konfigurationsservern är 192.168.3.121 och att det är säkert nätverksfilresursen \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Förbereda för distribution
1. Skapa en mapp på nätverksresursen och ge den namnet **MobSvcWindows**.
2. Logga in på din server för konfiguration och öppna en administrativ kommandotolk.
3. Kör följande kommandon för att generera en lösenfras fil:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiera den **MobSvc.passphrase** filen till den **MobSvcWindows** mapp på nätverksresursen.
5. Bläddra till installationsprogrammet för databasen på konfigurationsservern genom att köra följande kommando:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Kopiera den **Microsoft ASR\_UA\_*version*\_Windows\_GA\_*datum*\_Release.exe**  till den **MobSvcWindows** mapp på nätverksresursen.
7. Kopiera följande kod och spara den som **install.bat** till den **MobSvcWindows** mapp.

   > [!NOTE]
   > Ersätt platshållarna [CSIP] i det här skriptet med de faktiska värdena för IP-adressen för servern för configuration.

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

### <a name="create-a-package"></a>Skapa ett paket

1. Logga in på Configuration Manager-konsolen.
2. Bläddra till **programbibliotek** > **programhantering** > **paket**.
3. Högerklicka på **paket**, och välj **skapa paket**.
4. Ange värden för namn, beskrivning, tillverkare, språk och version.
5. Välj den **det här paketet innehåller källfiler** kryssrutan.
6. Klicka på **Bläddra**, och välj den nätverksresurs där installationsprogrammet lagras (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. På den **väljer du den typ av program som du vill skapa** väljer **standardprogram**, och klicka på **nästa**.

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. På den **ange information om det här standardprogrammet** , ange följande indata och klicka på **nästa**. (Du kan använda standardvärdena för andra indata.)

  | **Parameternamn** | **Värde** |
  |--|--|
  | Namn | Installera Microsoft Azure-Mobilitetstjänsten (Windows) |
  | Kommandorad | install.bat |
  | Programmet kan köras | Om en användare är inloggad |

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Markera operativsystem på nästa sida. Mobilitetstjänsten kan installeras endast på Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2.

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Slutför guiden genom att klicka på **nästa** två gånger.


> [!NOTE]
> Skriptet har stöd för både nya installationer av Mobilitetstjänsten agenter och agenter som redan är installerade uppdateringar.

### <a name="deploy-the-package"></a>Distribuera paketet
1. Högerklicka på paketet i Configuration Manager-konsolen och välj **distribuera innehåll**.
  ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Välj den **[distributionsplatser](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in som paket ska kopieras.
3. Slutför guiden. Paketet startar sedan replikeras till de angivna Distributionsplatserna.
4. När paketdistributionen görs, högerklicka på paketet och väljer **distribuera**.
  ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Välj den enhetssamling för Windows Server som du skapade i avsnittet förutsättningar som målsamlingen för distributionen.

  ![Guiden Distribuera programvara skärmbild](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. På den **ange innehållsmålet** väljer din **distributionsplatser**.
7. På den **ange för att styra hur programvaran distribueras** , se till att syftet är **krävs**.

  ![Guiden Distribuera programvara skärmbild](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. På den **ange schemat för den här distributionen** anger du ett schema. Mer information finns i [schemaläggning paket](https://technet.microsoft.com/library/gg682178.aspx).
9. På den **distributionsplatser** konfigurerar egenskaper enligt behoven i ditt datacenter. Slutför sedan guiden.

> [!TIP]
> Schemalägga installationen under din månatligt underhåll eller programvara uppdateringar fönster för att undvika onödiga omstarter.

Du kan övervaka förloppet för distributionen med hjälp av Configuration Manager-konsolen. Gå till **övervakning** > **distributioner** > *[package name]*.

  ![Skärmbild av Configuration Manager kan övervaka distributioner](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Distribuera på Linux-datorer
> [!NOTE]
> Den här artikeln förutsätter att IP-adressen för konfigurationsservern är 192.168.3.121 och att det är säkert nätverksfilresursen \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Förbereda för distribution
1. Skapa en mapp på nätverksresursen och kalla den som **MobSvcLinux**.
2. Logga in på din server för konfiguration och öppna en administrativ kommandotolk.
3. Kör följande kommandon för att generera en lösenfras fil:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiera den **MobSvc.passphrase** filen till den **MobSvcLinux** mapp på nätverksresursen.
5. Bläddra till installationsprogrammet för databasen på konfigurationsservern genom att köra kommandot:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Kopiera följande filer till den **MobSvcLinux** mapp på nätverksresursen:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Kopiera följande kod och spara den som **install_linux.sh** till den **MobSvcLinux** mapp.
   > [!NOTE]
   > Ersätt platshållarna [CSIP] i det här skriptet med de faktiska värdena för IP-adressen för servern för configuration.

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

1. Logga in på Configuration Manager-konsolen.
2. Bläddra till **programbibliotek** > **programhantering** > **paket**.
3. Högerklicka på **paket**, och välj **skapa paket**.
4. Ange värden för namn, beskrivning, tillverkare, språk och version.
5. Välj den **det här paketet innehåller källfiler** kryssrutan.
6. Klicka på **Bläddra**, och välj den nätverksresurs där installationsprogrammet lagras (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. På den **väljer du den typ av program som du vill skapa** väljer **standardprogram**, och klicka på **nästa**.

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. På den **ange information om det här standardprogrammet** , ange följande indata och klicka på **nästa**. (Du kan använda standardvärdena för andra indata.)

    | **Parameternamn** | **Värde** |
  |--|--|
  | Namn | Installera Microsoft Azure-Mobilitetstjänsten (Linux) |
  | Kommandorad | ./install_linux.sh |
  | Programmet kan köras | Om en användare är inloggad |

  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. På nästa sida väljer **det här programmet kan köras på alla plattformar**.
  ![Skärmbild av skapa paket och Program Guiden](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Slutför guiden genom att klicka på **nästa** två gånger.

> [!NOTE]
> Skriptet har stöd för både nya installationer av Mobilitetstjänsten agenter och agenter som redan är installerade uppdateringar.

### <a name="deploy-the-package"></a>Distribuera paketet
1. Högerklicka på paketet i Configuration Manager-konsolen och välj **distribuera innehåll**.
  ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Välj den **[distributionsplatser](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in som paket ska kopieras.
3. Slutför guiden. Paketet startar sedan replikeras till de angivna Distributionsplatserna.
4. När paketdistributionen görs, högerklicka på paketet och väljer **distribuera**.
  ![Skärmbild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Välj Server för Linux enhetssamling som du skapade i avsnittet förutsättningar som målsamlingen för distributionen.

  ![Guiden Distribuera programvara skärmbild](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. På den **ange innehållsmålet** väljer din **distributionsplatser**.
7. På den **ange för att styra hur programvaran distribueras** , se till att syftet är **krävs**.

  ![Guiden Distribuera programvara skärmbild](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. På den **ange schemat för den här distributionen** anger du ett schema. Mer information finns i [schemaläggning paket](https://technet.microsoft.com/library/gg682178.aspx).
9. På den **distributionsplatser** konfigurerar egenskaper enligt behoven i ditt datacenter. Slutför sedan guiden.

Mobilitetstjänsten installeras på Enhetssamling för Linux-servern enligt det schema som du har konfigurerat.


## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitetstjänsten
Du kan skapa Configuration Manager-paket för att avinstallera Mobilitetstjänsten. Använd följande skript för att göra det:

```
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
Du är nu redo att [Aktivera skydd](vmware-azure-enable-replication.md) för dina virtuella datorer.
