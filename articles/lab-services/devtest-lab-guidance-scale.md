---
title: Skala upp infrastrukturen för Azure DevTest Labs
description: Den här artikeln innehåller vägledning för att skala upp din Azure DevTest Labs-infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644842"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skala upp infrastrukturen för Azure DevTest Labs
Innan du implementerar DevTest Labs på företagsnivå finns det flera viktiga beslutspunkter. Att förstå dessa beslutspunkter på en hög nivå hjälper en organisation med designbeslut i framtiden. Dessa punkter bör dock inte hålla tillbaka en organisation från att starta ett proof of concept. De tre översta områdena för inledande uppskalningsplanering är:

- Nätverk och säkerhet
- Topologi för prenumeration
- Roller och ansvarsområden

## <a name="networking-and-security"></a>Nätverk och säkerhet
Nätverk och säkerhet är hörnstenar för alla organisationer. Medan en företagsomfattande distribution kräver en mycket djupare analys, finns det ett minskat antal krav för att framgångsrikt utföra ett konceptbevis. Några viktiga fokusområden är:

- **Azure-prenumeration** – För att distribuera DevTest Labs måste du ha åtkomst till en Azure-prenumeration med lämpliga rättigheter för att skapa resurser. Det finns ett antal sätt att få åtkomst till Azure-prenumerationer, inklusive ett Enterprise-avtal och Pay As You Go. Mer information om hur du får åtkomst till en Azure-prenumeration finns [i Licensiering av Azure för företaget](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Åtkomst till lokala resurser** – Vissa organisationer kräver att deras resurser i DevTest Labs har åtkomst till lokala resurser. En säker anslutning från din lokala miljö till Azure behövs. Därför är det viktigt att du konfigurerar/konfigurerar antingen en VPN- eller Express Route-anslutning innan du börjar. Mer information finns i [översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- **Ytterligare säkerhetskrav** – Andra säkerhetskrav som datorprinciper, åtkomst till offentliga IP-adresser, anslutning till internet är scenarier som kan behöva granskas innan ett konceptbevis implementeras. 

## <a name="subscription-topology"></a>Topologi för prenumeration
Prenumerationstopologi är en kritisk designövervägande vid distribution av DevTest Labs till Enterprise. Det är dock inte skyldigt att stelna alla beslut förrän efter ett bevis på begreppet har slutförts. Vid utvärdering av antalet prenumerationer som krävs för en företagsimplementering finns det två ytterligheter: 

- En prenumeration för hela organisationen
- Prenumeration per användare

Därefter lyfter vi fram proffsen för varje tillvägagångssätt.

### <a name="one-subscription"></a>Ett abonnemang
Ofta är tillvägagångssättet för en prenumeration inte hanterbar i ett stort företag. Att begränsa antalet prenumerationer ger dock följande fördelar:

- **Prognoskostnader** för företag.  Budgetering blir mycket enklare i en enda prenumeration eftersom alla resurser finns i en enda pool. Detta tillvägagångssätt möjliggör enklare beslutsfattande om när kostnadskontrollåtgärder ska vidtas vid en viss tidpunkt under en faktureringsperiod.
- **Hanterbarhet för** virtuella datorer, artefakter, formler, nätverkskonfiguration, behörigheter, principer etc. är enklare eftersom alla uppdateringar endast krävs i en prenumeration i stället för att göra uppdateringar över många prenumerationer.
- **Nätverksinsatser** förenklas avsevärt i en enda prenumeration för företag där lokal konnektivitet är ett krav. Ansluta virtuella nätverk över prenumerationer (hub-spoke-modell) krävs med ytterligare prenumerationer, vilket kräver ytterligare konfiguration, hantering, IP-adressutrymmen osv.
- **Teamsamarbete** är enklare när alla arbetar i samma prenumeration – till exempel är det enklare att tilldela om en virtuell dator till en medarbetare, dela gruppresurser osv.

### <a name="subscription-per-user"></a>Prenumeration per användare
En separat prenumeration per användare ger lika möjligheter till det alternativa spektrumet. Fördelarna med att ha många prenumerationer är:

- **Azure skalning kvoter** kommer inte att hindra antagandet. Till exempel, när detta skriver Azure tillåter 200 lagringskonton per prenumeration. Det finns driftkvoter för de flesta tjänster i Azure (många kan anpassas, vissa kan inte). I den här modellen med en prenumeration per användare är det högst osannolikt att de flesta kvoter uppnås. Mer information om aktuella Azure-skalningskvoter finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- **Återbetalningar** till grupper eller enskilda utvecklare blir mycket enklare att låta organisationer ta hänsyn till kostnader med hjälp av sin nuvarande modell.
- **Ägarskap & behörigheter** för DevTest Labs-miljöer är enkla. Du ger utvecklare åtkomst på prenumerationsnivå och de är 100 % ansvariga för allt, inklusive nätverkskonfiguration, labbprinciper och vm-hantering.

I Enterprise kan det finnas tillräckligt med begränsningar för spektrumets ytterligheter. Därför kan du behöva ställa in prenumerationer på ett sätt som faller i mitten av dessa ytterligheter. Som bästa praxis bör målet för en organisation vara att använda det minsta antalet prenumerationer som möjligt med tanke på de tvingande funktioner som ökar det totala antalet prenumerationer. För att upprepa, prenumerationstopologi är avgörande för en företagsdistribution av DevTest Labs men bör inte fördröja ett konceptbevis. Det finns ytterligare information i artikeln [Styrning](devtest-lab-guidance-governance-policy-compliance.md) om hur du bestämmer dig för prenumeration och lab granularitet i organisationen.

## <a name="roles-and-responsibilities"></a>Roller och ansvarsområden
Ett DevTest Labs proof of concept har tre primära roller med definierade ansvarsområden – Prenumerationsägare, DevTest Labs ägare, DevTest Labs-användare och eventuellt en contributor.

- **Prenumerationsägare** – Prenumerationsägaren har behörighet att administrera en Azure-prenumeration, inklusive tilldelning av användare, hantera principer, skapa & hantera nätverkstopologi, begära kvotökningar osv. Mer information finns i [den här artikeln](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs ägare** – DevTest Labs-ägaren har fullständig administrativ åtkomst till labbet. Den här personen ansvarar för att lägga till/ta bort användare, hantera kostnadsinställningar, allmänna labbinställningar och andra VM/artefaktbaserade uppgifter. En labbägare har också alla rättigheter för en DevTest Labs-användare.
- **DevTest Labs användare** - DevTest Labs användaren kan skapa och använda virtuella datorer i labbet. Dessa personer har några minimala administrativa funktioner på virtuella datorer som de skapar (start/stop/delete/configure sina virtuella datorer). Användarna kan inte hantera virtuella datorer för andra användare.

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Dirigera implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)