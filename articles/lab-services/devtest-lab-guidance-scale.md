---
title: Skala upp din Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller riktlinjer för att skala upp din Azure DevTest Labs-infrastruktur.
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
ms.openlocfilehash: 781a897c99a28fbd1b695876f477b140521068e3
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244887"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skala upp din Azure DevTest Labs-infrastruktur
Innan du implementerar DevTest Labs på företagsnivå, finns det flera viktiga punkter. Förstå dessa beslutspunkter på hög nivå hjälper en organisation med designbeslut i framtiden. Men ska de här punkterna inte lämna en organisation från att starta ett konceptbevis. De tre översta områdena för den inledande planeringen av skala upp är:

- Nätverk och säkerhet
- Topologi för prenumeration
- Roller och ansvarsområden

## <a name="networking-and-security"></a>Nätverk och säkerhet
Nätverk och säkerhet är grunder för alla organisationer. En företagsomfattande distribution kräver en mycket djupare analys, finns men det ett lägre antal krav för att utföra har ett konceptbevis. Några viktiga områden i fokus är:

- **Azure-prenumeration** – för att distribuera DevTest Labs, måste du ha tillgång till en Azure-prenumeration med rätt behörighet att skapa resurser. Det finns ett antal sätt att få åtkomst till Azure-prenumerationer, inklusive en Enterprise-avtal och betala. Mer information om att få åtkomst till en Azure-prenumeration finns i [licensiering för Azure för företaget](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Åtkomst till lokala resurser** – vissa organisationer kräver sina resurser i DevTest Labs har åtkomst till lokala resurser. Det krävs en säker anslutning från din lokala miljö till Azure. Därför är det viktigt att du lägger upp/konfigurera en VPN- eller Expressroute-anslutning för att komma igång. Mer information finns i [översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- **Ytterligare säkerhetskrav** – andra säkerhetskrav, till exempel dator principer för åtkomst till offentliga IP-adresser, ansluter till internet är scenarier som behöver granskas innan du implementerar ett konceptbevis. 

## <a name="subscription-topology"></a>Topologi för prenumeration
Prenumeration topologin är ett kritiskt designövervägande när du distribuerar DevTest Labs till företaget. Det krävs dock inte att stärka alla beslut förrän efter ett konceptbevis har slutförts. När du utvärderar antalet prenumerationer som krävs för en enterprise-implementering, finns det två extremval: 

- En prenumeration för hela organisationen
- Prenumerationen per användare

Nu ska fokusera vi på proffsen med respektive metod.

### <a name="one-subscription"></a>En prenumeration
Ofta kan metod på en prenumeration inte hanteras i ett stort företag. Dock ger begränsar antalet prenumerationer följande fördelar:

- **Prognoser** kostnader för företag.  Budgetera blir mycket enklare för en enskild prenumeration eftersom alla resurser som finns i en enda pool. Den här metoden möjliggör enklare beslutsfattande på när att utöva kostnadskontroll åtgärder vid en given tidpunkt under en faktureringsperiod.
- **Hanterbarhet** av virtuella datorer, artefakter, formler, nätverkskonfiguration, behörigheter, principer, etc är enklare eftersom alla uppdateringar krävs endast i en prenumeration till skillnad från att göra uppdateringar för många prenumerationer.
- **Nätverk** arbete förenklas för en enskild prenumeration för företag där lokal anslutning är ett krav. Ansluta virtuella nätverk över prenumerationer (typen hub-spoke modellen) måste anges med ytterligare prenumerationer som kräver ytterligare konfiguration, hantering, IP-adressutrymmen, osv.
- **Gruppsamarbete** är enklare när alla fungerar i samma prenumeration – till exempel är det enklare att tilldela om en virtuell dator till en medarbetare kan dela teamresurser, osv.

### <a name="subscription-per-user"></a>Prenumerationen per användare
En separat prenumeration per användare ger lika möjligheter till alternativa spektrumet. Fördelarna med att ha många prenumerationer är:

- **Azure skalning kvoter** inte kommer att hindra införande. Till exempel när detta skrivs med Azure kan 200 lagringskonton per prenumeration. Det finns operativa kvoter för de flesta tjänster i Azure (många kan anpassas, inte). I den här modellen för en prenumeration per användare är det mycket osannolikt att de flesta kvoter uppnås. Mer information om aktuella Azure skalning kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- **Återbetalningar** till grupper eller enskilda utvecklare blir mycket enklare så att organisationer att kompensera för kostnader med hjälp av deras aktuella modellen.
- **Ägarskap och behörigheter** för DevTest Labs miljöer är enkel. Du ger utvecklarna åtkomst på prenumerationsnivå och de är 100% ansvaret för allt inklusive konfiguration av nätverk, principer för labbet och hanteringen av virtuella datorer.

I företaget, kan det finnas tillräckligt med begränsningar på extremlägena spektrumet. Därför kan du behöva konfigurera prenumerationer på ett sätt som ligger mitt dessa extremval. Som du bör vara målet för en organisation att använda det lägsta antalet prenumerationer som möjligt och Tänk på de tvingande funktioner som ökar det totala antalet prenumerationer. Jag vill prenumeration topologin är viktiga för en företagsdistribution för DevTest Labs men bör inte fördröja ett konceptbevis. Det finns ytterligare information i den [styrning](devtest-lab-guidance-governance-policy-compliance.md) artikel om hur du bestämt dig för prenumerationen och lab granularitet i organisationen.

## <a name="roles-and-responsibilities"></a>Roller och ansvarsområden
Ett funktionstest av DevTest Labs har tre primära roller med definierade ansvarsområden – Prenumerationens ägare, labb ägare, DevTest Labs-användare och du kan också deltagare.

- **Prenumerationens ägare** – prenumerationsägaren har behörighet att administrera en Azure-prenumeration som inklusive tilldela användare, hantera principer, skapa och hantera nätverkets topologi, begära kvoter och så vidare. Mer information finns i [den här artikeln](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs ägare** – The DevTest Labs ägaren har fullständig administrativ åtkomst till labbet. Den här personen är ansvarig för att lägga till/ta bort användare, hantera kostnader inställningar, allmänna lab inställningar och andra uppgifter för VM/artefakt-baserade. Labbägare har även alla behörigheter för en DevTest Labs-användare.
- **DevTest Labs-användare** – The DevTest Labs-användare kan skapa och använda virtuella datorer i labbet. Dessa personer har vissa minimal administrativa rättigheter på virtuella datorer som de skapar (Starta/Stoppa/delete/konfigurera sina virtuella datorer). Användarna kan inte hantera virtuella datorer av andra användare.

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Dirigera implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)