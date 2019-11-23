---
title: Automatisera installationen av tjänsten Azure Site Recovery mobilitet för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av System Center Configuration Manager | Microsoft Docs
description: Den här artikeln hjälper dig att automatisera installationen av mobilitets tjänsten med System Center Configuration Manager för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255583"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatisera mobilitets tjänst installationen med System Center Configuration Manager

Mobilitets tjänsten är installerad på virtuella VMware-datorer och fysiska servrar som du vill replikera till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md)

Den här artikeln innehåller ett exempel på hur du kan använda System Center Configuration Manager för att distribuera Azure Site Recovery mobilitets tjänsten på en virtuell VMware-dator. Användning av ett program distributions verktyg som Configuration Manager har följande fördelar:

* Schemalägga nya installationer och uppgraderingar under den planerade underhålls perioden för program uppdateringar
* Skala distribution till hundratals servrar samtidigt

Den här artikeln använder System Center Configuration Manager 2012 R2 för att demonstrera distributions aktiviteten. Vi förutsätter att du använder version **9.9.4510.1** eller högre av mobilitets tjänsten.

Alternativt kan du automatisera mobilitets tjänst installationen med [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Krav

1. Ett verktyg för program varu distribution, till exempel Configuration Manager, som redan har distribuerats i din miljö.
2. Du bör skapa två [enhets samlingar](https://technet.microsoft.com/library/gg682169.aspx), en för alla **Windows-servrar**och en annan för alla Linux- **servrar**som du vill skydda med hjälp av Site Recovery.
3. En konfigurations server som redan är registrerad i Recovery Services-valvet.
4. En säker nätverks fil resurs (SMB-resurs) som kan nås av Configuration Manager-datorn.

## <a name="deploy-on-windows-machines"></a>Distribuera på Windows-datorer
> [!NOTE]
> Den här artikeln förutsätter att konfigurations serverns IP-adress är 192.168.3.121 och att den säkra nätverks fil resursen är \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Förbereda för distribution
1. Skapa en mapp på nätverks resursen och ge den namnet **MobSvcWindows**.
2. Logga in på konfigurations servern och öppna en administrativ kommando tolk.
3. Kör följande kommandon för att skapa en lösen fras fil:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiera filen **MobSvc. lösen fras** till mappen **MobSvcWindows** på din nätverks resurs.
5. Bläddra till installations platsen för installations programmet på konfigurations servern genom att köra följande kommando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopiera **Microsoft-ASR\_UA\_- *versionen*\_Windows\_GA\_*date*\_release. exe** till mappen **MobSvcWindows** på nätverks resursen.
7. Kopiera följande kod och spara den som **install. bat** i mappen **MobSvcWindows**

   > [!NOTE]
   > Ersätt [CSIP]-plats hållarna i det här skriptet med de faktiska värdena för IP-adressen för konfigurations servern.

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
2. Gå till **program bibliotek** > **program hantering** > - **paket**.
3. Högerklicka på **paket**och välj **skapa paket**.
4. Ange värden för namn, beskrivning, tillverkare, språk och version.
5. Markera kryss rutan det **här paketet innehåller källfiler** .
6. Klicka på **Bläddra**och välj den nätverks resurs där installations programmet lagras (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. På sidan **Välj den program typ som du vill skapa väljer du** **standard program**och klickar sedan på **Nästa**.

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. På sidan **Ange information om det här standard programmet** anger du följande indata och klickar på **Nästa**. (Andra indata kan använda standardvärdena.)

   | **Parameternamn** | **Värde** |
   |--|--|
   | Namn | Installera Microsoft Azure Mobility Service (Windows) |
   | Kommandorad | install.bat |
   | Programmet kan köra | Huruvida en användare är inloggad eller inte |

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. På nästa sida väljer du mål operativ system. 
10. Slutför guiden genom att klicka på **Nästa** två gånger.


> [!NOTE]
> Skriptet stöder både nya installationer av mobilitets tjänst agenter och uppdateringar av agenter som redan har installerats.

### <a name="deploy-the-package"></a>Distribuera paketet
1. Högerklicka på ditt paket i Configuration Manager-konsolen och välj **distribuera innehåll**.
   ![skärm bild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Välj de **[distributions platser](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** som paketen ska kopieras till.
3. Slutför guiden. Paketet börjar sedan replikeras till de angivna distributions platserna.
4. När paket distributionen är färdig högerklickar du på paketet och väljer **distribuera**.
   ![skärm bild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Välj den Windows Server-enhets samling som du skapade i avsnittet förutsättningar som mål samling för distribution.

   ![Skärm bild av guiden distribuera program vara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. På sidan **Ange innehålls målet** väljer du dina **distributions platser**.
7. På sidan **Ange inställningar för att styra hur program varan distribueras** , måste du se till att syftet är **obligatoriskt**.

   ![Skärm bild av guiden distribuera program vara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Ange ett schema på sidan **Ange schema för den här distributionen** . Mer information finns i [Schemalägga paket](https://technet.microsoft.com/library/gg682178.aspx).
9. På sidan **distributions platser** konfigurerar du egenskaperna enligt dina data centers behov. Slutför sedan guiden.

> [!TIP]
> Du kan undvika onödiga omstarter genom att schemalägga paket installationen i fönstret för månatlig underhåll eller fönstret program uppdateringar.

Du kan övervaka distributions förloppet med hjälp av Configuration Manager-konsolen. Gå till **övervakning** > **distributioner** >  *[ditt paket namn]* .

  ![Skärm bild av Configuration Manager alternativ för att övervaka distributioner](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Distribuera på Linux-datorer
> [!NOTE]
> Den här artikeln förutsätter att konfigurations serverns IP-adress är 192.168.3.121 och att den säkra nätverks fil resursen är \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Förbereda för distribution
1. Skapa en mapp på nätverks resursen och ge den namnet **MobSvcLinux**.
2. Logga in på konfigurations servern och öppna en administrativ kommando tolk.
3. Kör följande kommandon för att skapa en lösen fras fil:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiera filen **MobSvc. lösen fras** till mappen **MobSvcLinux** på din nätverks resurs.
5. Bläddra till installations platsen för installations programmet på konfigurations servern genom att köra kommandot:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopiera följande filer till mappen **MobSvcLinux** på din nätverks resurs:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release. tar. gz


7. Kopiera följande kod och spara den som **install_linux. sh** i mappen **MobSvcLinux**
   > [!NOTE]
   > Ersätt [CSIP]-plats hållarna i det här skriptet med de faktiska värdena för IP-adressen för konfigurations servern.

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
2. Gå till **program bibliotek** > **program hantering** > - **paket**.
3. Högerklicka på **paket**och välj **skapa paket**.
4. Ange värden för namn, beskrivning, tillverkare, språk och version.
5. Markera kryss rutan det **här paketet innehåller källfiler** .
6. Klicka på **Bläddra**och välj den nätverks resurs där installations programmet lagras (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. På sidan **Välj den program typ som du vill skapa väljer du** **standard program**och klickar sedan på **Nästa**.

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. På sidan **Ange information om det här standard programmet** anger du följande indata och klickar på **Nästa**. (Andra indata kan använda standardvärdena.)

    | **Parameternamn** | **Värde** |
   |--|--|
   | Namn | Installera Microsoft Azure Mobility Service (Linux) |
   | Kommandorad | ./install_linux.sh |
   | Programmet kan köra | Huruvida en användare är inloggad eller inte |

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. På nästa sida väljer **du det här programmet kan köras på valfri plattform**.
   ![skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Slutför guiden genom att klicka på **Nästa** två gånger.

> [!NOTE]
> Skriptet stöder både nya installationer av mobilitets tjänst agenter och uppdateringar av agenter som redan har installerats.

### <a name="deploy-the-package"></a>Distribuera paketet
1. Högerklicka på ditt paket i Configuration Manager-konsolen och välj **distribuera innehåll**.
   ![skärm bild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Välj de **[distributions platser](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** som paketen ska kopieras till.
3. Slutför guiden. Paketet börjar sedan replikeras till de angivna distributions platserna.
4. När paket distributionen är färdig högerklickar du på paketet och väljer **distribuera**.
   ![skärm bild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Välj den Linux server-enhets samling som du skapade i avsnittet förutsättningar som mål samling för distribution.

   ![Skärm bild av guiden distribuera program vara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. På sidan **Ange innehålls målet** väljer du dina **distributions platser**.
7. På sidan **Ange inställningar för att styra hur program varan distribueras** , måste du se till att syftet är **obligatoriskt**.

   ![Skärm bild av guiden distribuera program vara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Ange ett schema på sidan **Ange schema för den här distributionen** . Mer information finns i [Schemalägga paket](https://technet.microsoft.com/library/gg682178.aspx).
9. På sidan **distributions platser** konfigurerar du egenskaperna enligt dina data centers behov. Slutför sedan guiden.

Mobilitets tjänsten installeras på enhets samlingen för Linux-servern enligt det schema som du har konfigurerat.


## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitets tjänsten
Du kan skapa Configuration Manager paket för att avinstallera mobilitets tjänsten. Använd följande skript för att göra det:

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
