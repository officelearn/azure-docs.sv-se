---
title: Använd INSPEC för att automatisera efterlevnaden av din Azure-infrastruktur
description: Lär dig hur du använder INSPEC för att identifiera problem i dina Azure-distributioner
keywords: Azure, chef, DevOps, virtuella datorer, översikt, automatisera, INSPEC
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158223"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Använd INSPEC för att automatisera efterlevnaden av din Azure-infrastruktur

[INSPEC](https://www.chef.io/inspec/) är chefens språk med öppen källkod för att beskriva säkerhets & regler för efterlevnad som kan delas mellan program varu tekniker, drift-och säkerhets tekniker. INSPEC fungerar genom att jämföra det aktuella läget för din infrastruktur med önskat tillstånd som du uttryckligen har i den lättlästa och lätt inspeca koden. INSPEC identifierar överträdelser och visar resultat i form av en rapport, men ger dig kontroll över reparation.

Du kan använda INSPEC för att verifiera statusen för resurser och resurs grupper i en prenumeration, inklusive virtuella datorer, nätverkskonfigurationer, Azure Active Directory inställningar med mera.

I den här artikeln beskrivs fördelarna med att använda INSPEC för att göra säkerhet och efterlevnad enklare i Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Gör kompatibiliteten lätt att förstå och utvärdera

Dokumentation om efterlevnad som skrivits i kalkyl blad eller Word-dokument lämnar krav som är öppna för tolkning. Med inspecen ska du transformera dina krav till versions-och körbar kod som är läslig. Code ersätter konversationer om vad som bör utvärderas i syfte att bedöma konkreta tester med tydliga avsikter.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Identifiera problem med flottan och prioritera deras reparation

INSPEC: s läge för identifiering av agenter gör att du snabbt kan bedöma vid skala – din exponerings nivå. Inbyggda metadata för påverkan/allvarlighets grad hjälper till att avgöra vilka områden som ska fokuseras på för reparation. Du kan också snabbt skriva regler som svar på nya sårbarheter eller regler och genast återställa dem.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Granska virtuella Azure-datorer med princip-gäst konfiguration

Azure stöder direkt användning av chefs INSPEC-definitioner för att granska virtuella Azure-datorer via [Azure policy gäst konfiguration](/azure/governance/policy/concepts/guest-configuration). Gäst konfigurationen utvärderar en virtuell Linux-dator till en angiven chef INSPEC-definition och rapporterar efterlevnaden genom Azure Policy. Resultaten av dessa granskningar rapporteras även genom Azure Monitor loggar. aktivera aviseringar och andra automatiserings scenarier.

## <a name="satisfy-audits"></a>Uppfylla granskningar

Med INSPEC kan du svara på gransknings frågor när som helst – inte bara vid förinställda intervaller, till exempel kvartals Visa eller årsvis. Genom att köra inspecs test kontinuerligt kan du ange en gransknings cykel som känner till din exakta position och historik, i stället för att bli förvånad i granskarens resultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Prova att INSPEC i Azure Cloud Shell](https://shell.azure.com)