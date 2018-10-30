---
title: Installera Mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure | Microsoft Docs
description: Lär dig hur du installerar mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 145affbcff128e0ec599ad1f97c79260b0dcae5a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212700"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>Installera mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar

Azure Site Recovery-Mobilitetstjänsten installeras på virtuella VMware-datorer och fysiska servrar som du vill replikera till Azure. Tjänsten samlar in skrivna data på en dator och vidarebefordrar dem till processervern. Distribuera Mobilitetstjänsten till varje dator (VMware VM eller fysisk) som du vill replikera till Azure. Du kan distribuera Mobilitetstjänsten på servrarna och virtuella VMware-datorer du vill skydda med hjälp av följande metoder:


* [Installera med hjälp av programdistributionsverktyg som System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Installera med Azure Automation och Desired State Configuration (Automation DSC)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Installera manuellt från Användargränssnittet](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Installera manuellt från Kommandotolken](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Installera med hjälp av Site Recovery push-installation](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Från och med version 9.7.0.0, **på virtuella Windows-datorer**, Mobilitetstjänsten installeras också de senaste tillgängliga [Virtuella Azure-datoragenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent). När en dator växlar till Azure måste uppfyller datorn agentinstallationen som är nödvändiga för att använda alla VM-tillägg.
> </br>På **virtuella Linux-datorer**, WALinuxAgent måste installeras manuellt.

## <a name="prerequisites"></a>Förutsättningar
Utför dessa nödvändiga steg innan du installerar Mobilitetstjänsten på servern:
1. Logga in på konfigurationsservern och öppna ett kommandotolksfönster som administratör.
2. Ändra katalogen till bin-mappen och sedan skapa en lösenfras-fil.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store lösenfrasfilen på en säker plats. Du kan använda filen under installationen av Mobilitetstjänsten.
4. Mobility Service installationsprogram för alla operativsystem som stöds finns i mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mobility installationsprogrammet för att driva systemmappning

Visa en lista över operativsystem versioner med en kompatibel Mobilitetstjänsten-paketet finns på listan över [operativsystem som stöds för VMware-datorer och fysiska servrar](vmware-physical-azure-support-matrix.md#replicated-machines).

| Mallnamn för Installer-fil| Operativsystem |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64-bitars) </br> Windows Server 2012 (64-bitars) </br> Windows Server 2012 R2 (64-bitars) </br> Windows Server 2016 (64-bitars) |
|Microsoft-ASR\_UA\*RHEL6 64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* (endast 64-bitars) </br> CentOS 6.* (endast 64-bitars) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* (endast 64-bitars) </br> CentOS 7.* (endast 64-bitars) |
|Microsoft-ASR\_UA\*SLES12 64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 (endast 64-bitars)|
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (endast 64-bitars)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (endast 64-bitars)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (endast 64-bitars)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (endast 64-bitars)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server (endast 64-bitars)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (endast 64-bitars)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (endast 64-bitars)|

## <a name="install-mobility-service-manually-by-using-the-gui"></a>Installera Mobilitetstjänsten manuellt med hjälp av det grafiska Användargränssnittet

>[!IMPORTANT]
> Om du använder en konfigurationsserver för att replikera virtuella Azure IaaS-datorer från en Azure-prenumeration/region till en annan kan du använda metoden kommandoradsbaserad Command-Line-baserad installation.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Installera Mobilitetstjänsten manuellt i en kommandotolk

### <a name="command-line-installation-on-a-windows-computer"></a>Installation från kommandoraden på en Windows-dator
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Installation från kommandoraden på en Linux-dator
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Så här installerar du Mobilitetstjänsten via push-installation från Azure Site Recovery
Du kan göra en push-installation av Mobilitetstjänsten med hjälp av Site Recovery. Alla måldatorer måste uppfylla följande krav.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
När Mobilitetstjänsten har installerats i Azure portal, Välj **+ replikera** att börja skydda dessa virtuella datorer.

## <a name="update-mobility-service"></a>Uppdateringen av Mobilitetstjänsten

> [!WARNING]
> Se till att konfigurationsservern och skala ut processervrar någon huvudmålservern-server som är en del av distributionen uppdateras innan du börjar att uppdatera Mobilitetstjänsten på skyddade servrar.

1. På Azure-portalen bläddrar du till den *namnet på ditt valv* > **replikerade objekt** vy.
2. Om konfigurationsservern har redan har uppdaterats till den senaste versionen kan se du ett meddelande som läser ”ny Site recovery replikering agentuppdatering är tillgänglig. Klicka för att installera ”.

     ![Replikerade objekt fönster](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Välj meddelandet för att öppna sidan för val av virtuell dator.
4. Välj de virtuella datorer som du vill uppgradera mobilitetstjänsten på och välj **OK**.

     ![Replikerade objekt VM-lista](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Uppdateringen av Mobilitetstjänsten jobbet startar för var och en av de valda virtuella datorerna.

> [!NOTE]
> [Läs mer](vmware-azure-manage-configuration-server.md) om hur du uppdaterar lösenordet för kontot som används för att installera Mobilitetstjänsten.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Avinstallera Mobilitetstjänsten på en Windows Server-dator
Använd någon av följande metoder för att avinstallera Mobilitetstjänsten på en Windows Server-dator.

### <a name="uninstall-by-using-the-gui"></a>Avinstallera med hjälp av det grafiska Användargränssnittet
1. I Kontrollpanelen, väljer **program**.
2. Välj **Microsoft Azure Site Recovery Mobility Service/huvudmålservern**, och välj sedan **avinstallera**.

### <a name="uninstall-at-a-command-prompt"></a>Avinstallera i en kommandotolk
1. Öppna ett kommandotolksfönster som administratör.
2. Om du vill avinstallera Mobilitetstjänsten, kör du följande kommando:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Avinstallera Mobilitetstjänsten på en Linux-dator
1. Logga in på din Linux-server som en **rot** användare.
2. Gå till /user/local/ASR i en terminal.
3. Om du vill avinstallera Mobilitetstjänsten, kör du följande kommando:

    ```
    uninstall.sh -Y
    ```
