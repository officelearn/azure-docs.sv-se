---
title: Om Mobilitetstjänsten för haveriberedskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Läs mer om mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: 24102593a294d60b3cc2107bef8d443e672443b3
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019270"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Om mobilitetstjänsten för VMware-datorer och fysiska servrar

När du ställer in katastrofåterställning för virtuella VMware-datorer och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md), installation av Site Recovery-mobilitetstjänsten på varje virtuell dator med en lokal VMware och fysiska servrar.  Mobilitetstjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern för Site Recovery. Du kan distribuera Mobilitetstjänsten med följande metoder:

[Push-installation](vmware-azure-install-mobility-service.md): Konfigurera Site Recovery för att utföra en push-installation av mobilitetstjänsten: Om du vill göra detta, när du konfigurerar haveriberedskap, du också konfigurera ett konto som processervern för Site Recovery kan använda för att få åtkomst till den virtuella datorn eller en fysisk server syftet att installera tjänsten.
[Installera manuellt](vmware-physical-mobility-service-install-manual.md): du kan installera mobilitetstjänsten manuellt på varje dator med hjälp av Användargränssnittet eller Kommandotolken.
[Automatiserad distribution](vmware-azure-mobility-install-configuration-mgr.md): du kan automatisera installationen med programdistributionsverktyg som System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Azure VM-agenten

- **Windows-datorer**: från version 9.7.0.0 av mobilitetstjänsten, den [Azure VM-agenten](../virtual-machines/extensions/features-windows.md#azure-vm-agent) installeras med Mobilitetstjänstens installationsprogram. Detta säkerställer att när datorn växlar till Azure, Azure VM uppfyller agentinstallationen som är nödvändiga för att använda alla Vm-tillägg.
- **Virtuella Linux-datorer**: den [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) måste installeras manuellt på den virtuella Azure-datorn efter redundans.

## <a name="installer-files"></a>Installer-filer

Tabellen sammanfattar installer-filer för varje VM VMware och fysiska server-operativsystem. Du kan granska [operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.


**Installationsfil** | **Operativsystem (endast 64-bitars)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2 Windows Server 2012; Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6 64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12 64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8


## <a name="update-the-mobility-service"></a>Uppdatera mobilitetstjänsten

1. Innan du börjar måste du kontrollera att konfigurationsservern och skala ut processervrar någon huvudmålservern-server som är en del av distributionen uppdateras innan du uppdaterar Mobilitetstjänsten på skyddade datorer.
2. Öppna i portalen valvet > **replikerade objekt**.
3. Om konfigurationsservern är den senaste versionen kan se du ett meddelande som läser ”ny Site recovery replikering agentuppdatering är tillgänglig. Klicka för att installera ”.

     ![Replikerade objekt fönster](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. Klicka på meddelandet och i **agentuppdatering**, Välj de datorer som du vill uppgradera mobilitetstjänsten. Klicka sedan på **OK**.

     ![Replikerade objekt VM-lista](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. Uppdateringen av Mobilitetstjänsten jobbet startar för var och en av de valda datorerna.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Uppdatera det konto som används för push-installation av mobilitetstjänsten

När du har distribuerat Site Recovery för att aktivera push-installation av mobilitetstjänsten, du har angett ett konto som processervern för Site Recovery använder för att få åtkomst till datorerna och installera tjänsten när replikering har aktiverats för datorn. Om du vill uppdatera autentiseringsuppgifterna för det här kontot kan du följa [instruktionerna](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitetstjänsten

### <a name="on-a-windows-machine"></a>På en Windows-dator

Avinstallera i användargränssnittet eller från en kommandotolk.

- **I användargränssnittet**: I i Kontrollpanelen på datorn väljer **program**. Välj **Microsoft Azure Site Recovery Mobility Service/huvudmålservern** > **avinstallera**.
- **Från en kommandotolk**: öppna ett kommandotolksfönster som administratör på datorn. Kör följande kommando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>På en Linux-dator
1. Logga in på Linux-dator som en **rot** användare.
2. Gå till /user/local/ASR i en terminal.
3. Kör följande kommando:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Nästa steg

[Konfigurera push-installation av mobilitetstjänsten](vmware-azure-install-mobility-service.md).