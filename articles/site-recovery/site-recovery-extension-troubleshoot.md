---
title: Felsöka problem med Azure Site Recoverys agenter | Microsoft Docs
description: Innehåller information om symptom, orsaker och lösningar på Azure Site Recovery agent fel.
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: carmonm
ms.openlocfilehash: 0de5a9843b8029c1e1926ae296f43fc95b48106c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930134"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Felsök problem med Azure Site Recovery agent

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Site Recovery fel som rör VM-agenten och tillägget.


## <a name="azure-site-recovery-extension-time-out"></a>Tids gräns för Azure Site Recovery-tillägget  

Fel meddelande: "tids gränsen nåddes för aktivitets körningen under spårningen för att tilläggs åtgärden skulle startats"<br>
Felkod: "151076"

 Azure Site Recovery installera ett tillägg på den virtuella datorn som en del av jobbet Aktivera skydd. Något av följande villkor kan förhindra att skyddet utlöses och att jobbet Miss fungerar. Slutför följande fel söknings steg och försök sedan igen:

**Orsak 1: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Site Recovery-tillägget kan inte uppdateras eller läsas in](#the-site-recovery-extension-fails-to-update-or-load)**  

Fel meddelande: "föregående åtgärd för Site Recovery-tillägg tar längre tid än förväntat."<br>
Felkod: "150066"<br>

**Orsak 1: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Site Recoverys tilläggets status är felaktig](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Skyddet Miss lyckas eftersom den virtuella dator agenten inte svarar

Fel meddelande: "tids gränsen nåddes för uppgifts körningen under spårningen för att tilläggs åtgärden skulle starta."<br>
Felkod: "151099"<br>

Det här felet kan inträffa om Azures gästa Gent på den virtuella datorn inte är i klar läge.
Du kan kontrol lera statusen för Azures gästa Gent i [Azure Portal](https://portal.azure.com/). Gå till den virtuella datorn som du försöker skydda och kontrol lera statusen i "VM > Inställningar > Egenskaper > agent status". Merparten av tiden som agentens status blir klar efter omstart av den virtuella datorn. Om starta om inte är ett möjligt alternativ, eller om du fortfarande har problem, kan du utföra följande fel söknings steg.

**Orsak 1: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Fel meddelande: "tids gränsen nåddes för uppgifts körningen under spårningen för att tilläggs åtgärden skulle starta."<br>
Felkod: "151095"<br>

Detta inträffar när Agent versionen på Linux-datorn är gammal. Slutför följande fel söknings steg.<br>
  **Orsak 1: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Den virtuella dator agenten kan ha skadats eller också har tjänsten stoppats. Genom att installera om VM-agenten får du den senaste versionen. Den hjälper också till att starta om kommunikationen med tjänsten.

1. Ta reda på om tjänsten Windows Azure gästa Gent körs i VM-tjänsterna (Services. msc). Försök att starta om tjänsten Windows Azure gästa Gent.    
2. Om tjänsten Windows Azure gästa Gent inte visas i tjänster går du till **program och funktioner** i kontroll panelen för att avgöra om Windows gästa Gent tjänst är installerad.
4. Om Windows Azures gästa Gent visas i **program och funktioner**avinstallerar du Windows gäst-agenten.
5. Hämta och installera den [senaste versionen av agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
6. Kontrol lera att Windows Azures gästa Gent tjänster visas i tjänster.
7. Starta om skydds jobbet.

Kontrol lera också att [Microsoft .NET 4,5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. .NET 4,5 krävs för att den virtuella dator agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta Agent-relaterade eller felrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en gammal VM-agent. Du kan felsöka det här problemet genom att följa dessa allmänna rikt linjer:

1. Följ anvisningarna för [att uppdatera Linux VM-agenten](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du bara uppdaterar agenten via en distributions lagrings plats. Vi rekommenderar inte att du laddar ned agent koden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till sidan [Windows Azure Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   * För Ubuntu: `service walinuxagent start`
   * För andra distributioner: `service waagent start`

3. [Konfigurera agenten för automatisk omstart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Aktivera skydd av den virtuella datorn.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery-tillägget kan inte uppdateras eller läsas in
Om tillägg status är "Tom", "notrappsteg" eller över gång.

#### <a name="solution"></a>Lösning

Avinstallera tillägget och försök sedan igen.

Så här avinstallerar du tillägget:

1. I [Azure Portal](https://portal.azure.com/)går du till den virtuella dator som har problem med säkerhets kopieringen.
2. Välj **inställningar**.
3. Välj **Tillägg**.
4. Välj **Site Recovery tillägg**.
5. Välj **Avinstallera**.

För virtuella Linux-datorer, om VMSnapshot-tillägget inte visas i Azure Portal, [uppdaterar du Azure Linux-agenten](../virtual-machines/linux/update-agent.md)och kör sedan skyddet. 

Om du slutför de här stegen installeras tillägget om under skyddet.


