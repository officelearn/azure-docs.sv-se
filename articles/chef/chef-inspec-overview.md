---
title: Använd InSpec för automatisering av efterlevnad av din Azure-infrastruktur
description: Lär dig hur du använder InSpec för att identifiera problem i dina Azure-distributioner
keywords: azurblå,, devops, virtuella datorer, översikt, automatisera, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158223"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Använd InSpec för automatisering av efterlevnad av din Azure-infrastruktur

[InSpec](https://www.chef.io/inspec/) är Chefs språk med öppen källkod för att beskriva & efterlevnadsregler som kan delas mellan programvaruingenjörer, drift och säkerhetstekniker. InSpec fungerar genom att jämföra det faktiska tillståndet för din infrastruktur med önskat tillstånd som du uttrycker i lättläst och lätt att skriva InSpec-kod. InSpec upptäcker överträdelser och visar resultat i form av en rapport, men ger dig kontroll över reparation.

Du kan använda InSpec för att validera tillståndet för resurser och resursgrupper i en prenumeration, inklusive virtuella datorer, nätverkskonfigurationer, Azure Active Directory-inställningar med mera.

I den här artikeln beskrivs fördelarna med att använda InSpec för att göra säkerhet och efterlevnad enklare på Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Gör efterlevnaden lätt att förstå och bedöma

Dokumentation för regelefterlevnad som är skriven i kalkylblad eller i Word-dokument lämnar kraven öppna för tolkning. Med InSpec omvandlar du dina krav till version av, körbar, läsbar kod som kan läsas av människor. Koden ersätter samtal om vad som bör bedömas till förmån för konkreta tester med tydlig avsikt.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Identifiera problem som omfattar hela flottan och prioritera deras reparation

ISpecs agentlösa detekteringsläge kan du snabbt bedöma - i stor skala - din exponeringsnivå. Inbyggda metadata för bedömning av påverkan/allvarlighetsgrad hjälper dig att avgöra vilka områden som ska fokuseras på för reparation. Du kan också skriva regler snabbt som svar på nya sårbarheter eller förordningar och distribuera dem omedelbart.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Granska virtuella Azure-datorer med principgästkonfiguration

Azure stöder direkt användning av Chef InSpec-definitioner för att granska virtuella Azure-datorer via [Azure Policy Guest Configuration](/azure/governance/policy/concepts/guest-configuration). Gästkonfiguration utvärderar en virtuell Linux-dator till en medföljande Chef InSpec-definition och rapporterar efterlevnad tillbaka via Azure Policy. Resultaten av dessa granskningar rapporteras också via Azure Monitor-loggar. aktivera aviseringar och andra automatiseringsscenarier.

## <a name="satisfy-audits"></a>Tillgodose revisioner

Med InSpec kan du svara på revisionsfrågor när som helst - inte bara med förutbestämda intervall som kvartalsvis eller år. Genom att kontinuerligt köra InSpec-tester går du in i en granskningscykel som känner till din exakta efterlevnadsställning och historik, i stället för att bli överraskad av en revisors resultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Prova InSpec i Azure Cloud Shell](https://shell.azure.com)