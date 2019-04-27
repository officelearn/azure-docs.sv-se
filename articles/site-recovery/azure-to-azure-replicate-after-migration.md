---
title: Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder du datorer för att konfigurera haveriberedskap mellan Azure-regioner efter migrering till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 019c6ec776277a9102cb95cd685bbae0fc660d66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789696"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure 


Följ den här artikeln om du har [migreras lokala datorer till virtuella Azure-datorer](tutorial-migrate-on-premises-to-azure.md) med hjälp av den [Site Recovery](site-recovery-overview.md) , och du nu vill hämta de virtuella datorerna för haveriberedskap till en sekundär Azure-region. Artikeln beskriver hur du kontrollera att Azure VM-agenten är installerad på migrerade virtuella datorer och hur du tar bort den Site Recovery-mobilitetstjänsten som inte längre behövs efter migreringen.



## <a name="verify-migration"></a>Kontrollera migreringen

Innan du konfigurerar haveriberedskap, se till att migreringen har slutförts som förväntat. För att slutföra en migrering, efter redundansen, ska du välja den **fullständig migrering** alternativet för varje dator som du vill migrera. 

## <a name="verify-the-azure-vm-agent"></a>Verifiera Azure VM-agenten

Varje virtuell Azure-dator måste ha den [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) installerad. Om du vill replikera virtuella datorer i Azure installerar Site Recovery ett tillägg på agenten.

- Om datorn kör version 9.7.0.0 eller senare av Site Recovery-mobilitetstjänsten Azure VM-agenten installeras automatiskt av mobilitetstjänsten på virtuella Windows-datorer. Du måste installera agenten automatiskt på tidigare versioner av mobilitetstjänsten.
- För virtuella Linux-datorer måste du installera Azure VM-agenten manuellt. Du behöver bara installera Azure VM-agenten om mobilitetstjänsten installerad på den migrerade datorn är v9.6 eller tidigare.


### <a name="install-the-agent-on-windows-vms"></a>Installera agenten på Windows virtuella datorer

Om du kör en version av tidigare än 9.7.0.0 mobilitetstjänsten Site Recovery, eller du har några andra behovet av att installera agenten manuellt, gör du följande:  

1. Se till att du har administratörsbehörigheter på den virtuella datorn.
2. Ladda ned den [VM-agenten installer](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Kör installationsfilen.

#### <a name="validate-the-installation"></a>Verifiera installationen
Kontrollera att agenten är installerad:

1. Du bör se filen WaAppAgent.exe på Azure-dator, i mappen C:\WindowsAzure\Packages.
2. Högerklicka på filen, och i **egenskaper**väljer den **information** fliken.
3. Kontrollera att den **produktversion** fältet visar 2.6.1198.718 eller högre.

[Läs mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) om agentinstallationen för Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installera agenten på virtuella Linux-datorer

Installera den [virtuell Linux-dator](../virtual-machines/extensions/agent-linux.md) agenten manuellt på följande sätt:

1. Kontrollera att du har administratörsbehörigheter på datorn.
2. Vi rekommenderar starkt att du installerar Linux VM-agenten med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution. Alla de [godkända distribution providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrera Azure Linux-agenten i sina avbildningar och databaser.
    - Vi rekommenderar starkt att du uppdaterar agenten endast via en lagringsplats för distribution.
    - Vi rekommenderar inte att installera Linux VM-agenten direkt från GitHub och uppdaterar den.
    -  Om den senaste agenten för din distribution inte är tillgänglig, kontakta distribution stöd för instruktioner om hur du installerar den. 

#### <a name="validate-the-installation"></a>Verifiera installationen 

1. Kör det här kommandot: **ps -e** så att Azure-agenten körs på Linux-VM.
2. Om processen inte körs måste du starta om den med hjälp av följande kommandon:
    - För Ubuntu: **tjänsten walinuxagent start**
    - För andra distributioner: **tjänsten waagent start**


## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitetstjänsten

1. Avinstallera mobilitetstjänsten manuellt från Azure-dator, med någon av följande metoder. 
    - För Windows i Kontrollpanelen > **Lägg till/ta bort program**, avinstallera **Microsoft Azure Site Recovery Mobility Service/huvudmålservern**. Kör i en upphöjd kommandotolk:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Logga in som en rotanvändare för Linux. I en terminal, går du till **/user/local/ASR**, och kör följande kommando:
        ```
        ./uninstall.sh -Y
        ```
2. Starta om den virtuella datorn innan du konfigurerar replikering.

## <a name="next-steps"></a>Nästa steg

[Granska felsökning](site-recovery-extension-troubleshoot.md) för Site Recovery-tillägget på Azure VM-agenten.
[Replikera snabbt](azure-to-azure-quickstart.md) en Azure-dator till en sekundär region.
