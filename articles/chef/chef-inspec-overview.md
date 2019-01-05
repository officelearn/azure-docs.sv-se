---
title: Använd InSpec för efterlevnad automatisering av Azure-infrastrukturen
description: Lär dig hur du använder InSpec för att identifiera problem i dina Azure-distributioner
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera, inspce
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050735"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Använd InSpec för efterlevnad automatisering av Azure-infrastrukturen
[InSpec](https://www.chef.io/inspec/) är ett ramverk för kostnadsfria och öppen källkod för att testa och granska dina program och infrastruktur. InSpec fungerar genom att jämföra det aktuella läget i systemet av önskat tillstånd som du express i enkelt att läsa och lätt att skriva InSpec kod. InSpec identifierar överträdelser och visar resultat i form av en rapport, men ger dig kontroll för reparation. Du kan använda InSpec för att verifiera tillståndet för dina virtuella datorer som körs i Azure. Du kan också använda InSpec för att söka igenom och kontrollera tillståndet för resurser och resursgrupper i en prenumeration.

Den här artikeln beskriver fördelarna med att använda InSpec för att underlätta säkerhet och efterlevnad i Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Gör det enkelt att förstå och utvärdera efterlevnad
Med InSpec, kan du omvandla dina krav till en ny version, körbara, läsbara kod. På så sätt kan du organisera dina tester i sammansättningsbar profiler där du kan definiera och anpassa undantag efter behov.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Identifiera hela flottan problem och prioritera sina reparation
Identifieringsläge InSpec utan Agent kan du snabbt utvärdera - skalenligt - exponering-nivå. Inbyggda metadata för bedömning av påverkan/allvarlighetsgrad hjälper dig att bestämma vilka områden att fokusera på för reparation.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Granska datorer, data och nya SaaS-APIs
InSpec molnet API efterlevnad-funktioner kan du göra både grov och detaljerade intyg om ditt moln efterlevnads- och rapportera dem kontinuerligt.

## <a name="satisfy-audits"></a>Uppfyller granskningar
Med InSpec, kan du svara för att granska frågor när som helst – inte bara vid förutbestämda intervall, till exempel varje kvartal eller år. InSpec kan du ange en sexmånadersförnyelsen att känna till din exakta efterlevnadsstatus, i stället för att överraskad av en granskare resultat.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Minska tvetydighet och miscommunication gällande regler
Lämna konfigurationer för dokument och processer öppna tolkning. Körbar kod tar du bort konversationer om vad som ska utvärderas inaktuell och ersatts med praktisk erfarenhet tester med tydlig avsikt.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Håll dig uppdaterad med snabbt förändrade hot och efterlevnad landskap
InSpec kan du skriva och publicera koden samma dag och skriver nya regler som snabba svar på nya föreskrifter. Det innebär att ändringar i hot och förordningar inte längre vara lika med nödsituationer.

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med Chef](/azure/virtual-machines/windows/chef-automation)