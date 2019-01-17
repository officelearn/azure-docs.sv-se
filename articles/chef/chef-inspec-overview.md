---
title: Använd InSpec för efterlevnad automatisering av Azure-infrastrukturen
description: Lär dig hur du använder InSpec för att identifiera problem i dina Azure-distributioner
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359229"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Använd InSpec för efterlevnad automatisering av Azure-infrastrukturen
[InSpec](https://www.chef.io/inspec/) är chefs öppen källkod språk för att beskriva säkerhet och efterlevnad regler som kan delas mellan program, åtgärder och säkerhetstekniker. InSpec fungerar genom att jämföra faktiska tillståndet för din infrastruktur av önskat tillstånd som du express i enkelt att läsa och lätt att skriva InSpec kod. InSpec identifierar överträdelser och visar resultat i form av en rapport, men ger dig kontroll för reparation.

Du kan använda InSpec för att verifiera tillståndet för resurser och resursgrupper i en prenumeration, inklusive virtuella datorer, nätverkskonfigurationer, Azure Active Directory-inställningar och mer.

Den här artikeln beskriver fördelarna med att använda InSpec för att underlätta säkerhet och efterlevnad i Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Gör det enkelt att förstå och utvärdera efterlevnad
Efterlevnad dokumentation som är skrivna i kalkylblad eller Word-dokument lämna krav öppen tolkning. Med InSpec, kan du omvandla dina krav till en ny version, körbara, läsbara kod. Kod ersätter konversationer om vad som ska utvärderas inaktuell och ersatts med praktisk erfarenhet tester med tydlig avsikt.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Identifiera hela flottan problem och prioritera sina reparation
InSpec användarens utan Agent identifiera läge kan du snabbt utvärdera - skalenligt - exponering-nivå. Inbyggda metadata för bedömning av påverkan/allvarlighetsgrad hjälper dig att bestämma vilka områden att fokusera på för reparation. Du kan också skriva regler snabbt svar på nya säkerhetsrisker och förordningar och distribuera dem direkt.

## <a name="satisfy-audits"></a>Uppfyller granskningar
Med InSpec, kan du svara för att granska frågor när som helst – inte bara vid förutbestämda intervall, till exempel varje kvartal eller år. Genom att köra kontinuerligt InSpec tester kan du ange en sexmånadersförnyelsen vetskapen om att dina exakta efterlevnadsstatus och historik, i stället för att överraskad av en granskare resultat.

## <a name="next-steps"></a>Nästa steg

* Försök InSpec i Azure Cloudshell [ ![starta Cloudshell](https://shell.azure.com/images/launchcloudshell.png "starta Cloudshell")](https://shell.azure.com)
