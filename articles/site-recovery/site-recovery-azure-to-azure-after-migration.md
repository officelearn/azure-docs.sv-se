---
title: "Konfigurera katastrofåterställning för virtuella Azure-datorer efter migrering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du förbereda datorer för att ställa in katastrofåterställning mellan Azure-regioner efter migrering till Azure med Azure Site Recovery."
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: c06af21cd6e273b98c004e8bd0e6eac61ba7d644
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurera katastrofåterställning för virtuella Azure-datorer efter migrering till Azure 

>[!NOTE]
> Haveriberedskap för virtuella Azure-datorer med hjälp av Azure Site Recovery är för närvarande under förhandsgranskning.

Använd den här artikeln när du har [migreras lokala datorer till virtuella datorer i Azure](tutorial-migrate-on-premises-to-azure.md) med hjälp av den [Site Recovery](site-recovery-overview.md) service. Den här artikeln hjälper dig att förbereda virtuella Azure-datorer för att konfigurera återställning till en sekundär Azure region med Site Recovery.



## <a name="before-you-start"></a>Innan du börjar

Kontrollera att migreringen har slutförts korrekt innan du konfigurerar katastrofåterställning. Om du vill för att slutföra en migrering efter växling vid fel, bör du välja den **slutföra migreringen** alternativet för varje dator som du vill migrera. 



## <a name="install-the-azure-vm-agent"></a>Installera Azure VM-agenten

Azure [VM-agenten](../virtual-machines/windows/agent-user-guide.md) måste vara installerat på den virtuella datorn så att Site Recovery kan replikera den.


1. Om du vill installera den Virtuella datoragenten på virtuella datorer som kör Windows, hämta och köra den [installationsprogrammet för agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du behöver administratörsrättigheter på den virtuella datorn för att slutföra installationen.
2. Om du vill installera den Virtuella datoragenten på virtuella datorer som kör Linux, installera senast [Linux-agenten](../virtual-machines/linux/agent-user-guide.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar från databasen för din distribution. Vi rekommenderar inte installation av Linux VM-agenten direkt från GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Verifiera installationen på virtuella Windows-datorer

1. Du bör se filen WaAppAgent.exe på Azure VM, i mappen C:\WindowsAzure\Packages.
2. Högerklicka på filen och i **egenskaper**, Välj den **information** fliken.
3. Kontrollera att den **produktversionen** fältet visar 2.6.1198.718 eller högre.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migrering från virtuella VMware-datorer eller fysiska servrar

Observera att om du migrerar lokala virtuella VMware-datorer (eller fysiska servrar) till Azure:

- Du behöver bara installera den Virtuella Azure-agenten om mobilitetstjänsten installeras på den migrerade datorn är v9.6 eller tidigare.
- På virtuella Windows-datorer kör version 9.7.0.0 mobilitetstjänsten och senare, installerar service installationsprogrammet senaste tillgängliga Virtuella Azure-agenten. När du migrerar uppfylla dessa virtuella datorer redan installationen av nödvändiga för alla VM-tillägg, inklusive tillägget Site Recovery.
- Du måste avinstallera mobilitetstjänsten manuellt från Azure VM, med någon av följande metoder. Starta om den virtuella datorn innan du konfigurerar replikering.
    - För Windows i Kontrollpanelen > **Lägg till/ta bort program**, avinstallera **Microsoft Azure Site Recovery Mobility tjänsten eller Huvudtjänsten målservern**. Kör vid en upphöjd kommandotolk:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - För Linux, logga in en rotanvändare. Gå till i en terminal **/user/local/ASR**, och kör följande kommando:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en Azure VM till en sekundär region.
