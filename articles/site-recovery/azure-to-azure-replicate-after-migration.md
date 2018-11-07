---
title: Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder du datorer för att konfigurera haveriberedskap mellan Azure-regioner efter migrering till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: ae212bebf5a34e9a80f49a750735137d7a8814be
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232672"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure 


Använd den här artikeln när du har [migreras lokala datorer till virtuella Azure-datorer](tutorial-migrate-on-premises-to-azure.md) med hjälp av den [Site Recovery](site-recovery-overview.md) service. Den här artikeln hjälper dig att förbereda virtuella Azure-datorer för att konfigurera haveriberedskap till en sekundär Azure-region med Site Recovery.



## <a name="before-you-start"></a>Innan du börjar

Innan du konfigurerar haveriberedskap, se till att migreringen har slutförts som förväntat. För att slutföra en migrering, efter redundansen, ska du välja den **fullständig migrering** alternativet för varje dator som du vill migrera. 



## <a name="install-the-azure-vm-agent"></a>Installera Azure VM-agenten

Azure [VM-agenten](../virtual-machines/extensions/agent-windows.md) måste installeras på den virtuella datorn, så att Site Recovery kan replikera den.


1. Installera VM-agenten på virtuella datorer som kör Windows genom att ladda ned och kör den [agentinstalleraren](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du behöver admin-behörighet på den virtuella datorn för att slutföra installationen.
2. Om du vill installera VM-agenten på datorer som kör Linux, installera senast [linuxagenten](../virtual-machines/extensions/agent-linux.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar från din lagringsplats för distribution. Vi rekommenderar inte att du installerar Linux VM-agenten direkt från GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Verifiera installationen på virtuella Windows-datorer

1. Du bör se filen WaAppAgent.exe på Azure-dator, i mappen C:\WindowsAzure\Packages.
2. Högerklicka på filen, och i **egenskaper**väljer den **information** fliken.
3. Kontrollera att den **produktversion** fältet visar 2.6.1198.718 eller högre.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migrering från virtuella VMware-datorer eller fysiska servrar

Observera att om du migrerar lokala virtuella VMware-datorer (eller fysiska servrar) till Azure:

- Du behöver bara installera Azure VM-agenten om mobilitetstjänsten installerad på den migrerade datorn är v9.6 eller tidigare.
- På Windows virtuella datorer som kör version 9.7.0.0 av mobilitetstjänsten och senare, installerar installationsprogrammet service senaste tillgängliga Azure VM-agenten. När du migrerar uppfyller de virtuella datorerna redan kraven för alla VM-tillägg, inklusive Site Recovery-tillägget agentinstallationen.
- Du måste avinstallera mobilitetstjänsten manuellt från Azure-dator, med någon av följande metoder. Starta om den virtuella datorn innan du konfigurerar replikering.
    - För Windows i Kontrollpanelen > **Lägg till/ta bort program**, avinstallera **Microsoft Azure Site Recovery Mobility Service/huvudmålservern**. Kör i en upphöjd kommandotolk:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Logga in som en rotanvändare för Linux. I en terminal, går du till **/user/local/ASR**, och kör följande kommando:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en Azure-dator till en sekundär region.
