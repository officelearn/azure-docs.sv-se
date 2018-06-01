---
title: Installera Mobilitetstjänsten (VMware eller fysisk till Azure) | Microsoft Docs
description: Lär dig mer om att installera Mobilitetstjänsten agenten för att skydda din lokala virtuella VMware-datorer och fysiska servrar med Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 3279dbc2eeecd3a3f0f36a47d8dd51ef4f3f503f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942143"
---
# <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten 

Azure Site Recovery-Mobilitetstjänsten har installerats på virtuella VMware-datorer och fysiska servrar som du vill replikera till Azure. Tjänsten samlar in dataskrivningar på en dator och vidarebefordrar dem till processervern. Distribuera Mobilitetstjänsten till varje dator (VMware VM eller fysiska server) som du vill replikera till Azure. Du kan distribuera Mobilitetstjänsten på servrar och virtuella VMware-datorer du vill skydda med hjälp av följande metoder:


* [Installera med hjälp av verktyg för programvarudistribution som System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Installera med Azure Automation och Desired State Configuration (Automation DSC)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Installera manuellt från Användargränssnittet](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Installera manuellt från en kommandotolk](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Installera med återställning av push-installation](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Från och med version 9.7.0.0, på virtuella Windows-datorer, Mobilitetstjänsten installerar installationsprogrammet också den senaste tillgängliga [Virtuella Azure-datoragenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent). När en dator växlar till Azure måste uppfyller datorn installationen av nödvändiga för att använda alla VM-tillägg.

## <a name="prerequisites"></a>Förutsättningar
Gör följande förutsättningar innan du installerar Mobilitetstjänsten manuellt på servern:
1. Logga in på din server för konfiguration och öppna ett kommandotolksfönster som administratör.
2. Ändra katalogen till bin-mappen och sedan skapa en lösenfras-fil.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Lagra filen lösenfras på en säker plats. Du kan använda filen vid installation av Mobilitetstjänsten.
4. Mobility Service installationsprogram för alla operativsystem som stöds finns i mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mobility-installationsprogram för operativsystem systemmappning

| Installer filnamn för mallen| Operativsystem |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64-bitars) </br> Windows Server 2012 (64-bitars) </br> Windows Server 2012 R2 (64-bitars) </br> Windows Server 2016 (64-bitars) |
|Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6,9 (endast 64-bitars) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6,9 (endast 64-bitars) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (endast 64-bitars) </br> CentOS 7.0, 7.1, 7.2, 7.3 (endast 64-bitars) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (endast 64-bitars)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (endast 64-bitars)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (endast 64-bitars)|
|Microsoft ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (endast 64-bitars)|
|Microsoft ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server (endast 64-bitars)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (endast 64-bitars)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (endast 64-bitars)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Installera Mobilitetstjänsten manuellt med hjälp av det grafiska Användargränssnittet

>[!IMPORTANT]
> Om du använder en konfigurationsserver replikeras Azure IaaS-virtuella datorer från ett Azure-prenumeration eller en region till en annan, kan du använda metoden Kommandotolken Command-Line-baserad installation.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Installera Mobilitetstjänsten manuellt i en kommandotolk

### <a name="command-line-installation-on-a-windows-computer"></a>Kommandoradsparametrarna för installation på en Windows-dator
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Kommandoradsparametrarna för installation på en Linux-dator
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Installera Mobilitetstjänsten genom push-installation från Azure Site Recovery
Du kan göra en push-installation av Mobilitetstjänsten genom att använda Site Recovery. Alla måldatorer måste uppfylla följande krav.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
När Mobilitetstjänsten är installerad i Azure portal, väljer du **+ replikera** att börja skydda dessa virtuella datorer.

## <a name="update-mobility-service"></a>Uppdatera Mobilitetstjänsten

> [!WARNING]
> Kontrollera att konfigurationsservern, skalbara servrar och alla huvudmålservern-servrar som är en del av distributionen uppdateras innan du uppdaterar Mobilitetstjänsten på skyddade servrar.

1. På Azure-portalen går du till den *namnet på ditt valv* > **replikerade objekt** vyn.
2. Om konfigurationsservern har redan uppdaterats till den senaste versionen kan se du ett meddelande som läser du ”ny Site recovery replikering agentuppdatering är tillgänglig. Klicka om du vill installera ”.

     ![Replikerade objekt fönster](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Välj meddelandet för att öppna sidan för val av virtuell dator.
4. Välj de virtuella datorerna som du vill uppgradera mobilitetstjänsten på och välj **OK**.

     ![Replikerade objekt VM lista](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Uppdatera Mobilitetstjänsten jobbet startar för var och en av de valda virtuella datorerna.

> [!NOTE]
> [Läs mer](vmware-azure-manage-configuration-server.md) om hur du uppdaterar lösenordet för kontot som används för att installera Mobilitetstjänsten.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Avinstallera Mobilitetstjänsten på en dator med Windows Server
Använd någon av följande metoder för att avinstallera Mobilitetstjänsten på en Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Avinstallera genom att använda det grafiska Användargränssnittet
1. På Kontrollpanelen, Välj **program**.
2. Välj **Microsoft Azure Site Recovery Mobility tjänsten eller Huvudtjänsten målservern**, och välj sedan **avinstallera**.

### <a name="uninstall-at-a-command-prompt"></a>Avinstallera i en kommandotolk
1. Öppna ett kommandotolksfönster som administratör.
2. Om du vill avinstallera Mobilitetstjänsten, kör du följande kommando:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Avinstallera Mobilitetstjänsten på en Linux-dator
1. Logga in på Linux-servern som en **rot** användare.
2. Gå till /user/local/ASR i en terminal.
3. Om du vill avinstallera Mobilitetstjänsten, kör du följande kommando:

    ```
    uninstall.sh -Y
    ```
