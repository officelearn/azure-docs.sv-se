---
title: Vanliga frågor och svar om Azure Security Center – frågor om virtuella datorer
description: Vanliga frågor och svar om virtuella datorer i Azure Security Center, en produkt som hjälper dig att förebygga, upptäcka och svara på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599371"
---
# <a name="faq---questions-about-virtual-machines"></a>Vanliga frågor - Frågor om virtuella maskiner


## <a name="what-types-of-virtual-machines-are-supported"></a>Vilka typer av virtuella datorer stöds?

Övervakning och rekommendationer är tillgängliga för virtuella datorer (VMs) som skapats med både de [klassiska och Resource Manager-distributionsmodellerna](../azure-classic-rm.md).

Se [Plattformar som stöds i Azure Security Center](security-center-os-coverage.md) för en lista över plattformar som stöds.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Varför känner inte Azure Security Center igen den lösning mot skadlig kod som körs på min virtuella Azure-dator?

Azure Security Center har insyn i skadlig kod som installeras via Azure-tillägg. Security Center kan till exempel inte identifiera skadlig kod som har förinstallerats på en avbildning som du angav eller om du installerade skadlig kod på dina virtuella datorer med dina egna processer (till exempel konfigurationshanteringssystem).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Varför får jag meddelandet "Saknade skanningsdata" för min virtuella dator?

Det här meddelandet visas när det inte finns några genomsökningsdata för en virtuell dator. Det kan ta lite tid (mindre än en timme) för genomsökningsdata att fyllas i när datainsamling har aktiverats i Azure Security Center. Efter den första ifyllningen av genomsökningsdata får du det här meddelandet eftersom det inte finns några genomsökningsdata alls eller det finns inga nyliga genomsökningsdata. Genomsökningar fyller inte i för en virtuell dator i ett stoppat tillstånd. Det här meddelandet kan också visas om sökdata inte har fyllts i nyligen (i enlighet med bevarandeprincipen för Windows-agenten, som har ett standardvärde på 30 dagar).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hur ofta söker Security Center efter sårbarheter, systemuppdateringar och slutpunktsskyddsproblem?

Nedan följer svarstidstiderna för Säkerhetscenter-genomsökningar av sårbarheter, uppdateringar och problem:

- Säkerhetskonfigurationer för operativsystem – data uppdateras inom 48 timmar
- Systemuppdateringar – data uppdateras inom 24 timmar
- Problem med slutpunktsskydd – data uppdateras inom 8 timmar

Security Center söker vanligtvis efter nya data varje timme och uppdaterar rekommendationerna därefter. 

> [!NOTE]
> Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns i [Azure Security Center Platform Migration](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför får jag meddelandet "VM-agent saknas?"

VM-agenten måste installeras på virtuella datorer för att aktivera datainsamling. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar VM-agenten på andra virtuella datorer finns i blogginlägget [VM-agent och tillägg](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).