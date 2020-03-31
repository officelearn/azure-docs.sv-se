---
title: Felsöka Azure VM-tillägget för haveriberedskap med Azure Site Recovery
description: Felsöka problem med Azure VM-tillägget för haveriberedskap med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190728"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Felsöka problem med tillägg till Azure VM

Den här artikeln innehåller felsökningssteg som kan hjälpa dig att lösa Azure Site Recovery-fel relaterade till VM-agenten och tillägget.


## <a name="azure-site-recovery-extension-time-out"></a>Time-out för azure site recovery-tillägg  

Felmeddelande: "Aktivitetskörningen har tagit time out medan spårning för tilläggsåtgärd ska startas"<br>
Felkod: "151076"

 Azure Site Recovery installerade ett tillägg på den virtuella datorn som en del av ett aktivera skyddsjobb. Något av följande villkor kan förhindra att skyddet utlöses och orsaka att jobbet misslyckas. Slutför följande felsökningssteg och försök sedan igen:

- [Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Tillägget Site Recovery kan inte uppdateras eller läsas in](#the-site-recovery-extension-fails-to-update-or-load)

Felmeddelande: "Föregående åtgärd för återställning av webbplats tar längre tid än förväntat."<br>
Felkod: "150066"

- [Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Statusen för tillägget Site Recovery är felaktig](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Skyddet misslyckas eftersom VM-agenten inte svarar

Felmeddelande: "Aktivitetskörningen har tagit time out medan spårning för tilläggsåtgärd som ska startas."<br>
Felkod: "151099"

Det här felet kan inträffa om Azure-gästagenten på den virtuella datorn inte är i redo tillstånd.

Du kan kontrollera status för Azure-gästagenten i [Azure-portalen](https://portal.azure.com/). Gå till den virtuella datorn som du försöker skydda och kontrollera statusen i > **vm-inställningars** >  **VM****egenskapsagentstatus****Properties** > . För det mesta är agentens status klar efter omstart av den virtuella datorn. Men om du inte kan starta om eller om du fortfarande står inför problemet, slutför du följande felsökningssteg:

- [Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Felmeddelande: "Aktivitetskörningen har tagit time out medan spårning för tilläggsåtgärd som ska startas."<br>
Felkod: "151095"

Det här felet uppstår när agentversionen på Linux-datorn är instredd. Slutför följande felsökningssteg:

- [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Vm-agenten kan ha skadats eller så kan tjänsten ha stoppats. Om du installerar om VM-agenten får du den senaste versionen. Det hjälper också starta om kommunikationen med tjänsten.

1. Ta reda på om Tjänsten Windows Azure Guest Agent körs i VM-tjänsterna (services.msc). Starta om tjänsten Windows Azure Guest Agent.    
1. Om Tjänsten Windows Azure Guest Agent inte visas i tjänster öppnar du Kontrollpanelen. Gå till **Program och funktioner** för att se om Tjänsten Windows Guest Agent är installerad.
1. Om Windows Azure-gästagenten visas i **Program och funktioner**avinstallerar du Windows Azure-gästagenten.
1. Hämta och installera den [senaste versionen av agenten MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du behöver administratörsrättigheter för att slutföra installationen.
1. Kontrollera att Tjänsten Windows Azure Guest Agent visas i tjänster.
1. Starta om skyddsjobbet.

Kontrollera också att [Microsoft .NET 4.5 är installerat](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) i den virtuella datorn. Du behöver .NET 4.5 för att VM-agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta agentrelaterade eller tilläggsrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en inaktuell VM-agent. Så här felsöker du problemet:

1. Följ instruktionerna för [uppdatering av Linux VM-agenten](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du uppdaterar agenten endast via en distributionsdatabas. Vi rekommenderar inte att du hämtar agentkoden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kontaktar du distributionssupporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till windows [Azure Linux-agentsidan](https://github.com/Azure/WALinuxAgent/releases) i GitHub-databasen.

1. Kontrollera att Azure-agenten körs på den virtuella datorn genom att köra följande kommando:`ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu:`service walinuxagent start`
   - För andra distributioner:`service waagent start`

1. [Konfigurera den automatiska omstartsagenten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Aktivera skydd av den virtuella datorn.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Tillägget Site Recovery kan inte uppdateras eller läsas in

Tilläggsstatusen visas som "Tom", "NotReady" eller "Övergång".

#### <a name="solution"></a>Lösning

Avinstallera tillägget och starta om åtgärden igen.

Så här avinstallerar du tillägget:

1. Gå till den virtuella datorn som har säkerhetskopieringsfel i [Azure-portalen.](https://portal.azure.com/)
1. Välj **Inställningar**.
1. Välj **Tillägg**.
1. Välj **Tillägg för platsåterställning**.
1. Välj **Avinstallera**.

Om VMSnapshot-tillägget inte visas i Azure-portalen för [Linux-vm uppdaterar du Azure Linux Agent](../virtual-machines/linux/update-agent.md). Kör sedan skyddet.

När du har slutfört dessa steg gör det att tillägget installeras om under skyddet.
