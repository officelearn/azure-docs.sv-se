---
title: Använd InSpec för kompatibilitet automatisering av Azure-infrastrukturen
description: Lär dig hur du använder InSpec för att identifiera problem i din Azure-distributioner
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera inspce
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260158"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Använd InSpec för kompatibilitet automatisering av Azure-infrastrukturen
[InSpec](https://www.chef.io/inspec/) är ett gratis och öppen källkod ramverk för testning och granska dina program och infrastruktur. InSpec fungerar genom att jämföra systemet bibliotekets tillstånd med det tillstånd som du express i lätt att läsa och enkelt att skriva InSpec kod. InSpec identifierar överträdelser och visar resultatet i form av en rapport, men ger dig kontroll av korrigeringar. Du kan använda InSpec för att kontrollera tillståndet för dina virtuella datorer som körs i Azure. Du kan också använda InSpec för att söka igenom och kontrollera tillståndet för resurser och resursgrupper i en prenumeration.

Den här artikeln beskriver fördelarna med att använda InSpec för att underlätta säkerhet och regelefterlevnad i Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Gör det lättare att förstå och utvärdera kompatibilitet
Med InSpec transformera dina krav till en ny version, körbara läsbart kod. På så sätt kan du organisera dina tester i sammanställningsbara profiler där du kan definiera och anpassa undantag efter behov.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Identifiera problem med flottan hela och prioritera sina reparation
InSpec utan Agent identifiera läge kan du snabbt bedöma - skalanpassat - exponering-nivå. Inbyggda metadata för inverkan/allvarlighetsgrad poäng hjälper dig att fastställa vilka områden att fokusera på för reparation.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Undersök datorer, data och nya SaaS-APIs
InSpec moln API efterlevnad funktioner kan du göra grov såväl som detaljerad intyg om ditt moln efterlevnad och rapportera om den kontinuerligt.

## <a name="satisfy-audits"></a>Uppfyller granskningar
Med InSpec, kan du svara för att granska frågor när som helst – inte bara med bestämda mellanrum, till exempel varje kvartal eller år. InSpec kan du ange en audit cykel veta din exakt kompatibilitet hållningsdata, i stället för att överraskad av en granskare resultaten.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Minska tvetydighet och miscommunication om regler
Dokument lämnar konfigurationer och processer som öppnar tolkning. Körbar kod tar bort konversationer om vad ska utvärderas för konkreta tester med tydliga syfte.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Håll dig uppdaterad med snabbt ändra landskap hot och efterlevnad
InSpec kan du skriva och publicera koden samma dag och skriver nya regler för snabbt svar på nya förordningar. Det innebär att ändringar i hot och förordningar inte längre är lika med nödfall.

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med hjälp av Chef](/azure/virtual-machines/windows/chef-automation)