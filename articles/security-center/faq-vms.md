---
title: Azure Security Center vanliga frågor och svar om virtuella datorer
description: Vanliga frågor och svar om virtuella datorer i Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599371"
---
# <a name="faq---questions-about-virtual-machines"></a>Vanliga frågor och svar om virtuella datorer


## <a name="what-types-of-virtual-machines-are-supported"></a>Vilka typer av virtuella datorer stöds?

Övervakning och rekommendationer är tillgängliga för virtuella datorer som skapats med hjälp av både [klassiska distributions modeller för och Resource Manager](../azure-classic-rm.md).

Se [plattformar som stöds i Azure Security Center](security-center-os-coverage.md) för en lista över plattformar som stöds.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Varför inte Azure Security Center kan identifiera det program mot skadlig kod som körs på min Azure-dator?

Azure Security Center har insyn i program mot skadlig kod installeras via Azure-tillägg. Security Center är till exempel inte kunna identifiera skadlig kod som har förinstallerats på en avbildning eller om du har installerat program mot skadlig kod på dina virtuella datorer med dina egna processer (till exempel konfigurationshanteringssystem).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Varför visas meddelandet ”Genomsök Data som saknas” för den virtuella datorn?

Det här meddelandet visas när det inte finns några genomsökningsdata för en virtuell dator. Det kan ta lite tid (mindre än en timme) för genomsökningsdata att fyllas i när datainsamling har aktiverats i Azure Security Center. Efter den första ifyllningen av genomsökningsdata får du det här meddelandet eftersom det inte finns några genomsökningsdata alls eller det finns inga nyliga genomsökningsdata. Genomsökningar fyller inte i för en virtuell dator i ett stoppat tillstånd. Det här meddelandet kan också visas om genomsökningsdata inte har fyllts i nyligen (i enlighet med policyn för datalagring i för Windows-agenten, som har ett standardvärde på 30 dagar).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hur ofta skannar Security Center för säkerhetsproblem med operativsystem, systemuppdateringar och problem med endpoint protection?

Nedan visas svars tiderna för Security Center genomsökningar av sårbarheter, uppdateringar och problem:

- Operativsystemet säkerhetskonfigurationer – data uppdateras inom 48 timmar
- Systemuppdateringar – data uppdateras inom 24 timmar
- Endpoint Protection utfärdar – data uppdateras inom 8 timmar

Security Center kan du vanligtvis söker efter nya data varje timme, och uppdaterar rekommendationer i enlighet med detta. 

> [!NOTE]
> Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Läs mer i [Azure Security Center plattforms migrering](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför visas meddelandet ”VM-agenten är saknas”?

VM-agenten måste installeras på virtuella datorer för att aktivera insamling av Data. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar VM-agenten på andra virtuella datorer finns i blogg inlägget [VM-agent och tillägg](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).