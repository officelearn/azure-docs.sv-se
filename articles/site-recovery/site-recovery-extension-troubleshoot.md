---
title: 'Felsöka Azure Site Recovery-agenten fel: Gäst Agent Status otillgänglig | Microsoft Docs'
description: Symptom, orsaker och lösningar för Azure Site Recovery-fel som rör agent och tillägg
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 44f2016dacf1433cfe3a61058a167c42700e37d6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Felsöka Azure Site Recovery-tillägg-fel: problem med agenten eller tillägg

Den här artikeln innehåller felsökning som kan hjälpa dig att lösa Azure Site Recovery-fel som rör VM-agent och tillägg.


## <a name="azure-site-recovery-extension-time-out"></a>Timeout för Azure Site Recovery-tillägg  

Felmeddelande: ”uppgiftskörningen nåddes när spårning för tillägget åtgärden startas”<br>
Felkod: ”151076”

 Azure Site Recovery installera tillägget på den virtuella datorn som en del av skyddsaktiveringsjobbet. Något av följande villkor kan förhindra att skydd mot som utlöses och jobbet misslyckas. Slutför följande felsökningssteg och försök sedan utföra åtgärden:

**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Site Recovery-tillägg som inte går att uppdatera eller läsa in](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Skyddet fungerar inte eftersom VM-agenten inte svarar

Felmeddelande: ”uppgiftskörningen nåddes när tillägget åtgärd ska startas”.<br>
Felkod: ”151099”<br>

Det här felet kan inträffa om Azure gästagenten på den virtuella datorn inte är i tillståndet redo.
Du kan kontrollera status för Azure gästagenten i [Azure-portalen](https://portal.azure.com/). Gå till den virtuella datorn som du försöker skydda och kontrollera statusen ”VM > Inställningar > Egenskaper > agentstatus”. I de flesta fall statusen för agenten bli redo efter omstart för den virtuella datorn. Men om omstart är inte ett möjligt alternativ eller om du fortfarande inför problemet, Slutför följande felsökningssteg.

**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  



## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Den Virtuella datoragenten kan vara skadad eller tjänsten kan ha stoppats. Installera om den Virtuella datoragenten hjälper dig att få den senaste versionen. Det kan också starta om kommunikation med tjänsten.

1. Avgöra om den ”Windows Azure gäst-agenttjänsten” körs i VM-tjänster (services.msc). Försök att starta om den ”Windows Azure Gästagenten service”.    
2. Om tjänsten Windows Azure-Gästagenten inte visas i tjänster på Kontrollpanelen, gå till **program och funktioner** avgör om Windows Gästagent-tjänsten är installerad.
4. Om Windows Azure-Gästagenten visas i **program och funktioner**, avinstallera Windows-Gästagenten.
5. Hämta och installera den [senaste versionen av agenten MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsrättigheter för att slutföra installationen.
6. Kontrollera att tjänsten Windows Azure-Gästagenten visas i tjänster.
7. Starta om skyddsjobbet.

Kontrollera också att [Microsoft .NET 4.5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) i den virtuella datorn. .NET 4.5 krävs för VM-agenten kan kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta agent-relaterade tillägget-relaterade fel eller för Linux virtuella datorer orsakas av problem som påverkar en inaktuell VM-agent. Följ dessa allmänna riktlinjer för att felsöka problemet:

1. Följ instruktionerna för [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Vi *rekommenderar* att du uppdaterar agenten endast via en lagringsplats för distribution. Vi rekommenderar inte hämta agent-koden direkt från GitHub och uppdatera den. Om den senaste agenten för din distribution inte är tillgänglig, kontakta distribution stöd för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten, gå till den [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) sidan på GitHub-lagringsplatsen.

2. Kontrollera att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

 Om processen inte körs startar du om den med hjälp av följande kommandon:

 * För Ubuntu: `service walinuxagent start`
 * För andra distributioner: `service waagent start`

3. [Konfigurera automatisk omstart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Aktivera skyddet för den virtuella datorn.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery-tillägg som inte går att uppdatera eller läsa in
Om tillägg status är ”tomt ', 'NotReady' eller övergång.

#### <a name="solution"></a>Lösning

Avinstallera tillägget och gör om åtgärden igen.

Så här avinstallerar tillägget:

1. I den [Azure-portalen](https://portal.azure.com/)går du till den virtuella datorn som upplever säkerhetskopieringen har misslyckats.
2. Välj **inställningar**.
3. Välj **tillägg**.
4. Välj **Site Recovery-tillägget**.
5. Välj **avinstallera**.

För Linux VM, om tillägget VMSnapshot inte visas i Azure-portalen [uppdatera Azure Linux-agenten](../virtual-machines/linux/update-agent.md), och kör sedan skyddet. 

Gör så här gör att tillägg installeras under skyddet.


