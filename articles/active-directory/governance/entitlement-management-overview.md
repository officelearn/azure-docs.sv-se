---
title: Vad är berättigandehantering? - Azure AD
description: Få en översikt över Hantering av Azure Active Directory-rättigheter och hur du kan använda den för att hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna och externa användare.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 477bb5430214b497f90e3cb6d5df69f9fcf4035f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546165"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Vad är berättigandehantering i Azure AD?

Azure Active Directory (Azure AD) berättigandehantering är en [identitetsstyrningsfunktion](identity-governance-overview.md) som gör det möjligt för organisationer att hantera identitets- och åtkomstlivscykeln i stor skala, genom att automatisera arbetsflöden för åtkomstbegäran, åtkomsttilldelningar, granskningar och förfallodatum.

Anställda i organisationer behöver åtkomst till olika grupper, program och webbplatser för att utföra sitt jobb. Hantering av den här åtkomsten är en utmaning eftersom kraven ändras – nya program läggs till eller användare behöver ytterligare åtkomsträttigheter.  Det här scenariot blir mer komplicerat när du samarbetar med externa organisationer – du kanske inte vet vem i den andra organisationen som behöver åtkomst till organisationens resurser och de vet inte vilka program, grupper eller webbplatser din organisation använder.

Hantering av Azure AD-rättigheter kan hjälpa dig att mer effektivt hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna användare och även för användare utanför organisationen som behöver åtkomst till dessa resurser.

## <a name="why-use-entitlement-management"></a>Varför använda rättighetshantering?

Företagsorganisationer står ofta inför utmaningar när de hanterar anställdas åtkomst till resurser som:

- Användare kanske inte vet vilken åtkomst de ska ha, och även om de gör det kan de ha svårt att hitta rätt personer att godkänna deras åtkomst
- När användarna har hittat och fått åtkomst till en resurs kan de behålla åtkomsten längre än vad som krävs för affärsändamål

Dessa problem förvärras för användare som behöver åtkomst från en annan organisation, till exempel externa användare som kommer från supply chain-organisationer eller andra affärspartners. Ett exempel:

- Ingen person kanske inte känner till alla specifika individer i andra organisationers kataloger för att kunna bjuda in dem
- Även om de kunde bjuda in dessa användare, ingen i den organisationen kanske kommer ihåg att hantera alla användarens åtkomst konsekvent

Hantering av Azure AD-rättigheter kan hjälpa till att lösa dessa utmaningar.  Om du vill veta mer om hur kunder har använt Azure AD-berättigandehantering kan du läsa [Avanade fallstudie](https://aka.ms/AvanadeELMCase) och [Centrica fallstudie](https://aka.ms/CentricaELMCase).  Det här videoklippet innehåller en översikt över rättighetshantering och dess värde:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Vad kan jag göra med rättighetshantering?

Här är några av funktionerna för rättighetshantering:

- Delegera till icke-administratörer möjligheten att skapa åtkomstpaket. Dessa åtkomstpaket innehåller resurser som användare kan begära och de delegerade åtkomstpakethanterare kan definiera principer med regler som användare kan begära, vem som måste godkänna deras åtkomst och när åtkomsten upphör att gälla.
- Välj anslutna organisationer vars användare kan begära åtkomst.  När en användare som ännu inte finns i katalogen begär åtkomst och godkänns, uppmanas de automatiskt till din katalog och tilldelas åtkomst.  När deras åtkomst går ut, om de inte har några andra åtkomstpakettilldelningar, kan deras B2B-konto i katalogen tas bort automatiskt.

Du kan komma igång med vår [handledning för att skapa ditt första åtkomstpaket.](entitlement-management-access-package-first.md) Du kan också läsa de [vanliga scenarierna](entitlement-management-scenarios.md)eller titta på videoklipp, inklusive

- [Så här distribuerar du hantering av Azure AD-berättigande i din organisation](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Så här övervakar och skalar du din användning av Azure AD-berättigandehantering](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Så här delegerar du i rättighetshantering](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Vad är åtkomstpaket och vilka resurser kan jag hantera med dem?

Rättighetshantering introducerar till Azure AD konceptet för ett *åtkomstpaket*. Ett åtkomstpaket är ett paket med alla resurser med den åtkomst en användare behöver för att arbeta med ett projekt eller utföra sin uppgift. Åtkomstpaket används för att styra åtkomsten för dina interna medarbetare och även användare utanför organisationen.

 Här är de typer av resurser som du kan hantera användarens åtkomst till med rättighetshantering:

- Medlemskap i Azure AD-säkerhetsgrupper
- Medlemskap i Office 365-grupper och -team
- Tilldelning till Azure AD-företagsprogram, inklusive SaaS-program och anpassade integrerade program som stöder federation/enkel inloggning och/eller etablering
- Medlemskap i SharePoint Online-webbplatser

Du kan också styra åtkomsten till andra resurser som är beroende av Azure AD-säkerhetsgrupper eller Office 365-grupper.  Ett exempel:

- Du kan ge användare licenser för Microsoft Office 365 genom att använda en Azure [AD-säkerhetsgrupp](../users-groups-roles/licensing-groups-assign.md) i ett åtkomstpaket och konfigurera gruppbaserad licensiering för den gruppen
- Du kan ge användare åtkomst till hantera Azure-resurser med hjälp av en Azure AD-säkerhetsgrupp i ett åtkomstpaket och skapa en [Azure-rolltilldelning](../../role-based-access-control/role-assignments-portal.md) för den gruppen

## <a name="how-do-i-control-who-gets-access"></a>Hur styr jag vem som får åtkomst?

Med ett åtkomstpaket listar en administratör eller delegerad åtkomstpakethanterare resurserna (grupper, appar och webbplatser) och de roller som användarna behöver för dessa resurser.

Åtkomstpaket innehåller också en eller flera *principer*. En princip definierar regler eller skyddsräcken för tilldelning till åtkomstpaket. Varje princip kan användas för att säkerställa att endast lämpliga användare kan begära åtkomst, att det finns godkännare för deras begäran och att deras åtkomst till dessa resurser är tidsbegränsad och upphör att gälla om de inte förnyas.

![Åtkomstpaket och principer](./media/entitlement-management-overview/elm-overview-access-package.png)

Inom varje princip definierar en administratör eller åtkomstpakethanterare

- Antingen de redan befintliga användarna (vanligtvis anställda eller redan inbjudna gäster) eller partnerorganisationer för externa användare som är berättigade att begära åtkomst
- Godkännandeprocessen och de användare som kan godkänna eller neka åtkomst
- Varaktigheten för en användares åtkomsttilldelning, när den har godkänts, innan tilldelningen upphör att gälla

Följande diagram visar ett exempel på de olika elementen i rättighetshantering. Den visar en katalog med två exempel åtkomstpaket.

- **Åtkomstpaket 1** innehåller en enskild grupp som en resurs. Åtkomst definieras med en princip som gör att en uppsättning användare i katalogen kan begära åtkomst.
- **Accesspaket 2** innehåller en grupp, ett program och en SharePoint Online-webbplats som resurser. Åtkomst definieras med två olika principer. Den första principen gör det möjligt för en uppsättning användare i katalogen att begära åtkomst. Den andra principen gör det möjligt för användare i en extern katalog att begära åtkomst.

![Översikt över hantering av rättigheter](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>När ska jag använda åtkomstpaket?

Åtkomstpaket ersätter inte andra mekanismer för åtkomsttilldelning.  De är lämpligast i situationer som följande:

- Medarbetare behöver tidsbegränsad åtkomst för en viss uppgift.  Du kan till exempel använda gruppbaserad licensiering och en dynamisk grupp för att säkerställa att alla anställda har en Exchange Online-postlåda och sedan använda åtkomstpaket för situationer där medarbetare behöver ytterligare åtkomst, till exempel för att läsa avdelningsresurser från en annan avdelning.
- Åtkomst måste godkännas av en anställds chef eller andra utsedda personer.
- Avdelningarna vill hantera sina egna åtkomstpolicyer för sina resurser utan IT-engagemang.  
- Två eller flera organisationer samarbetar i ett projekt, och därför måste flera användare från en organisation tas in via Azure AD B2B för att komma åt en annan organisations resurser.

## <a name="how-do-i-delegate-access"></a>Hur delegerar jag åtkomst?

 Åtkomstpaket definieras i behållare som kallas *kataloger*.  Du kan ha en enda katalog för alla dina åtkomstpaket, eller så kan du ange personer att skapa och äga sina egna kataloger. En administratör kan lägga till resurser i valfri katalog, men en icke-administratör kan bara lägga till de resurser som de äger i en katalog. En katalogägare kan lägga till andra användare som katalogdelägare eller som åtkomstpakethanterare.  Dessa scenarier beskrivs ytterligare i [artikeldelegeringen och rollerna i Azure AD-rättighetshantering](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Sammanfattning av terminologin

Om du vill bättre förstå rättighetshantering och dess dokumentation kan du referera tillbaka till följande lista med termer.

| Period | Beskrivning |
| --- | --- |
| åtkomstpaket | En bunt resurser som ett team eller ett projekt behöver och styrs med principer. Ett åtkomstpaket finns alltid i en katalog. Du skapar ett nytt åtkomstpaket för ett scenario där användare måste begära åtkomst.  |
| begäran om åtkomst | En begäran om åtkomst till resurserna i ett åtkomstpaket. En begäran går vanligtvis via ett arbetsflöde för godkännande.  Om den begärande användaren godkänns får han en tilldelning av åtkomstpaket. |
| Tilldelning | En tilldelning av ett åtkomstpaket till en användare säkerställer att användaren har alla resursroller i åtkomstpaketet.  Åtkomstpakettilldelningar har vanligtvis en tidsgräns innan de upphör att gälla. |
| Katalog | En behållare med relaterade resurser och åtkomstpaket.  Kataloger används för delegering, så att icke-administratörer kan skapa sina egna åtkomstpaket. Katalogägare kan lägga till resurser som de äger i en katalog. |
| katalog skapare | En samling användare som har behörighet att skapa nya kataloger.  När en användare som inte är administratör som har behörighet att vara en katalogskapare skapar en ny katalog blir de automatiskt ägare till den katalogen. |
| ansluten organisation | En extern Azure AD-katalog eller domän som du har en relation med. Användare från en ansluten organisation kan anges i en princip som tillåts begära åtkomst. |
| policy | En uppsättning regler som definierar åtkomstlivscykeln, till exempel hur användare får åtkomst, vem som kan godkänna och hur länge användare har åtkomst via en tilldelning. En princip är kopplad till ett åtkomstpaket. Ett åtkomstpaket kan till exempel ha två principer - en för anställda att begära åtkomst och en sekund för externa användare att begära åtkomst. |
| resource | En tillgång, till exempel en Office-grupp, en säkerhetsgrupp, ett program eller en SharePoint Online-webbplats, med en roll som en användare kan beviljas behörighet till. |
| resurskatalog | En katalog som har en eller flera resurser att dela. |
| resursroll | En samling behörigheter som är associerade med och definierade av en resurs. En grupp har två roller - medlem och ägare. SharePoint-webbplatser har vanligtvis tre roller men kan ha ytterligare anpassade roller. Program kan ha anpassade roller. |


## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specialiserade moln, till exempel Azure Germany och Azure China 21Vianet, är för närvarande inte tillgängliga för användning.

### <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Kontrollera att din katalog har minst lika många Azure AD Premium P2-licenser som du har:

- Medlemsanvändare som **kan** begära ett åtkomstpaket.
- Medlems- och gästanvändare som begär ett åtkomstpaket.
- Medlems- och gästanvändare som godkänner begäranden om ett åtkomstpaket.
- Medlems- och gästanvändare som har en direkt tilldelning till ett åtkomstpaket.

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Inga licenser krävs för användare med rollen Global administratör som ställer in de första katalogerna, åtkomstpaketen och principerna och delegerar administrativa uppgifter till andra användare.
- Inga licenser krävs för användare som har delegerats administrativa uppgifter, till exempel katalogskapare, katalogägare och åtkomstpakethanterare.
- Inga licenser krävs för gäster som **kan** begära åtkomstpaket, men **inte** begär ett åtkomstpaket.

För varje betald Azure AD Premium P2-licens som du köper för dina medlemsanvändare (anställda) kan du använda Azure AD B2B för att bjuda in upp till fem gästanvändare. Dessa gästanvändare kan också använda Azure AD Premium P2-funktioner. Mer information finns i [Azure AD B2B-licensieringsvägledning](../b2b/licensing-guidance.md)för samarbete .

Mer information om licenser finns i [Tilldela eller ta bort licenser med Azure Active Directory-portalen](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exempel på licensscenarier

Här är några exempel på licensscenarier som hjälper dig att avgöra hur många licenser du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| En global administratör på Woodgrove Bank skapar inledande kataloger och delegerar administrativa uppgifter till 6 andra användare. En av principerna anger att **alla medarbetare** (2 000 medarbetare) kan begära en viss uppsättning åtkomstpaket. 150 anställda begär åtkomstpaket. | 2 000 anställda som **kan** begära åtkomstpaket | 2 000 |
| En global administratör på Woodgrove Bank skapar inledande kataloger och delegerar administrativa uppgifter till 6 andra användare. En av principerna anger att **alla medarbetare** (2 000 medarbetare) kan begära en viss uppsättning åtkomstpaket. En annan princip anger att vissa användare från **Användare från partner Contoso** (gäster) kan begära samma åtkomstpaket som är föremål för godkännande. Contoso har 30 000 användare. 150 anställda begär åtkomstpaket och 10 500 användare från Contoso-begärandeåtkomst. | 2 000 anställda + 500 gästanvändare från Contoso som överstiger förhållandet 1:5 (10 500 - (2 000 * 5)) | 2 500 |

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skapa ditt första åtkomstpaket](entitlement-management-access-package-first.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
