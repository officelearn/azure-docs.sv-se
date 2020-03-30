---
title: Konfigurera haveriberedskap efter migrering till Azure med Azure Site Recovery
description: I den här artikeln beskrivs hur du förbereder datorer för att konfigurera haveriberedskap mellan Azure-regioner efter migreringen till Azure med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159107"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure 


Följ den här artikeln om du har [migrerat lokala datorer till virtuella Azure-datorer](tutorial-migrate-on-premises-to-azure.md) med hjälp av [tjänsten Site Recovery](site-recovery-overview.md) och nu vill konfigurera de virtuella datorerna för haveriberedskap till en sekundär Azure-region. Artikeln beskriver hur du säkerställer att Azure VM-agenten är installerad på migrerade virtuella datorer och hur du tar bort tjänsten Site Recovery Mobility som inte längre behövs efter migreringen.



## <a name="verify-migration"></a>Verifiera migrrering

Innan du ställer in haveriberedskap kontrollerar du att migreringen har slutförts som förväntat. Om du vill slutföra en migrering efter redundansen bör du välja alternativet **Fullständig migrering** för varje dator som du vill migrera. 

## <a name="verify-the-azure-vm-agent"></a>Verifiera Azure VM-agenten

Varje Azure-dator måste ha [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) installerad. Om du vill replikera virtuella Azure-datorer installerar Site Recovery ett tillägg på agenten.

- Om datorn kör version 9.7.0.0 eller senare av tjänsten Site Recovery Mobility installeras Azure VM-agenten automatiskt av mobilitetstjänsten på virtuella datorer i Windows. I tidigare versioner av mobilitetstjänsten installerar du agenten manuellt.
- För virtuella Linux-datorer måste du installera Azure VM-agenten manuellt. Du behöver bara installera Azure VM-agenten om mobilitetstjänsten som är installerad på den migrerade datorn är v9.6 eller tidigare.


### <a name="install-the-agent-on-windows-vms"></a>Installera agenten på virtuella Datorer i Windows

Om du kör en version av mobilitetstjänsten Site Recovery tidigare än 9.7.0.0, eller om du har något annat behov av att installera agenten manuellt, gör du följande:  

1. Kontrollera att du har administratörsbehörighet på den virtuella datorn.
2. Hämta [installationsprogrammet för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Kör installationsfilen.

#### <a name="validate-the-installation"></a>Verifiera installationen
Så här kontrollerar du att agenten är installerad:

1. På den virtuella Azure-datorn i mappen C:\WindowsAzure\Packages bör du se filen WaAppAgent.exe.
2. Högerklicka på filen och välj fliken **Information** i **Egenskaper.**
3. Kontrollera att fältet **Produktversion** visar 2.6.1198.718 eller senare.

[Läs mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) om agentinstallation för Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installera agenten på virtuella Linux-datorer

Installera [Azure Linux VM-agenten](../virtual-machines/extensions/agent-linux.md) manuellt enligt följande:

1. Kontrollera att du har administratörsbehörighet på datorn.
2. Vi rekommenderar starkt att du installerar Linux VM-agenten med hjälp av ett RPM eller ett DEB-paket från distributionens paketarkiv. Alla [godkända distributionsleverantörer](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrerar Azure Linux-agentpaketet i sina avbildningar och databaser.
    - Vi rekommenderar starkt att du uppdaterar agenten endast via en distributionsdatabas.
    - Vi rekommenderar inte att du installerar Linux VM-agenten direkt från GitHub och uppdaterar den.
    -  Om den senaste agenten för distributionen inte är tillgänglig kontaktar du distributionssupporten för instruktioner om hur du installerar den. 

#### <a name="validate-the-installation"></a>Verifiera installationen 

1. Kör det här kommandot: ps -e för att säkerställa att **Azure-agenten** körs på Linux-vm.
2. Om processen inte körs startar du om den med hjälp av följande kommandon:
    - För Ubuntu: **service walinuxagent start**
    - För andra distributioner: **service waagent start**


## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitetstjänsten

1. Avinstallera mobilitetstjänsten manuellt från den virtuella Azure-datorn med någon av följande metoder. 
    - På Kontrollpanelen > **Lägg till/ta bort program**i Kontrollpanelen avinstallerar du Microsoft Azure Site Recovery Mobility **Service/Master Target server**. Kör i en upphöjd kommandotolk:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Logga in som root-användare för Linux. Gå till **/user/local/ASR**i en terminal och kör följande kommando:
        ```
        ./uninstall.sh -Y
        ```
2. Starta om den virtuella datorn innan du konfigurerar replikering.

## <a name="next-steps"></a>Nästa steg

[Granska felsökning](site-recovery-extension-troubleshoot.md) för site recovery-tillägget på Azure VM-agenten.
[Replikera snabbt](azure-to-azure-quickstart.md) en virtuell Azure-dator till en sekundär region.
