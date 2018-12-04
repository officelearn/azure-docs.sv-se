---
title: Felsöka problem med Azure Site Recovery-agenter | Microsoft Docs
description: Innehåller information om problem, orsaker och lösningar för Azure Site Recovery-agentfel.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 7a4ff22bbb4c7c13d8c2feae3638ce8e33a8f7ad
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845832"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Felsöka problem med Azure Site Recovery-agenten

Den här artikeln innehåller åtgärder för felsökning som kan hjälpa dig att lösa Azure Site Recovery-fel som rör VM-agenten och tillägg.


## <a name="azure-site-recovery-extension-time-out"></a>Timeout för Azure Site Recovery-tillägget  

Felmeddelande: ”för körning av aktiviteten nåddes i spårning för tilläggsåtgärden skulle startas”<br>
Felkod: ”151076”

 Azure Site Recovery installerar ett tillägg på den virtuella datorn som en del av aktivering av skydd. Något av följande villkor kan förhindra att skydd mot utlöses och jobb misslyckas. Slutför följande felsökningssteg och försök igen:

**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Site Recovery-tillägget inte går att uppdatera eller läsa in](#the-site-recovery-extension-fails-to-update-or-load)**  

Felmeddelande: ”föregående site recovery-tilläggsåtgärden tar längre tid än förväntat”.<br>
Felkod: ”150066”<br>

**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [statusen för Site Recovery-tillägget är felaktig](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Skyddet fungerar inte eftersom VM-agenten inte svarar

Felmeddelande: ”för körning av aktiviteten nåddes uppgiftskörningens för tilläggsåtgärden skulle startas”.<br>
Felkod: ”151099”<br>

Det här felet kan inträffa om Azure-gästagenten på den virtuella datorn inte är i tillståndet klar.
Du kan kontrollera status för Azure-gästagenten i [Azure-portalen](https://portal.azure.com/). Gå till den virtuella datorn som du vill skydda och kontrollera statusen ”virtuell dator > Inställningar > Egenskaper > agentstatus”. I de flesta fall statusen för agenten bli redo när du startar om den virtuella datorn. Men om omstart är inte ett möjligt alternativ eller om du ändå får problemet, Slutför följande felsökningssteg.

**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Felmeddelande: ”för körning av aktiviteten nåddes uppgiftskörningens för tilläggsåtgärden skulle startas”.<br>
Felkod: ”151095”<br>

Detta kan inträffa när agentversionen på Linux-dator är gammal. Utför följande steg i felsökningen.<br>
  **Orsak 1: [agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)

#### <a name="solution"></a>Lösning
VM-agenten kan vara skadad eller tjänsten har stoppats. Installera VM-agenten kan hämta den senaste versionen. Det hjälper också att starta om kommunikation med tjänsten.

1. Avgöra om den ”Windows Azure-gästagenttjänsten” körs i VM-tjänsterna (services.msc). Försök att starta om den ”Windows Azure-gästagenttjänsten”.    
2. Om Windows Azure-gästagenttjänsten inte visas i tjänster på Kontrollpanelen, gå till **program och funktioner** att avgöra om tjänsten Windows-Gästagenten är installerad.
4. Om Windows Azure-Gästagentversionen som visas i **program och funktioner**, avinstallera Windows-Gästagenten.
5. Ladda ned och installera den [senaste versionen av agenten MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
6. Kontrollera att tjänsten Windows Azure-Gästagentversionen visas i tjänster.
7. Starta om jobb för att skydda.

Kontrollera också att [Microsoft .NET 4.5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. .NET 4.5 krävs för VM-agenten kan kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
Mest agent-relaterade eller tillägget-relaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en inaktuell VM-agent. Följ dessa allmänna riktlinjer för att felsöka problemet:

1. Följ anvisningarna för [uppdaterar Linux VM-agenten](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Vi *rekommenderar* att du uppdaterar agenten endast via en lagringsplats för distribution. Vi rekommenderar inte hämta agent-kod direkt från GitHub och uppdaterar den. Om den senaste agenten för din distribution inte är tillgänglig, kontakta distribution stöd för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten, går du till den [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) sidan i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

 Om processen inte körs måste du starta om den med hjälp av följande kommandon:

 * För Ubuntu: `service walinuxagent start`
 * För andra distributioner: `service waagent start`

3. [Konfigurera automatisk omstart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Aktivera skydd för den virtuella datorn.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery-tillägget inte går att uppdatera eller läsa in
Om tillägg status är ”tom”, ”NotReady” eller övergång.

#### <a name="solution"></a>Lösning

Avinstallera tillägget och starta om igen.

Avinstallera tillägget:

1. I den [Azure-portalen](https://portal.azure.com/)går du till den virtuella datorn som har drabbats av säkerhetskopieringen har misslyckats.
2. Välj **inställningar**.
3. Välj **Tillägg**.
4. Välj **Site Recovery-tillägget**.
5. Välj **avinstallera**.

För Linux VM, om VMSnapshot-tillägget inte visas i Azure-portalen [uppdatera Azure Linux Agent](../virtual-machines/linux/update-agent.md), och kör sedan skyddet. 

Gör så här gör tillägget installeras under skyddet.


