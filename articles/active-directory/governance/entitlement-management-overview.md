---
title: Vad är hantering av Azure AD-rättigheter? (För hands version) – Azure Active Directory
description: Få en översikt över Azure Active Directory hantering av rättigheter och hur du kan använda den för att hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna och externa användare.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 896cc495d6f369c034171833a9b90aa53e6477b6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234628"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Vad är hantering av Azure AD-rättigheter? (Förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anställda i organisationer behöver åtkomst till olika grupper, program och webbplatser för att utföra sitt arbete. Det är svårt att hantera den här åtkomsten. I de flesta fall finns det ingen ordnad lista över alla resurser som en användare behöver för ett projekt. Projektledaren har en god förståelse för de resurser som behövs, vilka individer som är inblandade och hur länge projektet kommer att vara sist. Men Project Manager har normalt inte behörighet att godkänna eller bevilja åtkomst till andra. Det här scenariot blir mer komplicerat när du försöker arbeta med externa användare eller företag.

Azure Active Directory (Azure AD) hantering av rättigheter kan hjälpa dig att hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna användare och även användare utanför organisationen.

## <a name="why-use-entitlement-management"></a>Varför ska jag använda hantering av rättigheter?

Företags organisationer är ofta riktade mot utmaningar när de hanterar åtkomst till resurser som:

- Användare kanske inte vet vilken åtkomst de ska ha
- Användare kan ha svårt att hitta rätt personer eller rätt resurser
- När användarna hittar och får åtkomst till en resurs, kan de vänta på att få åtkomst längre än vad som krävs i affärs syfte

Dessa problem är sammansatta för användare som behöver åtkomst från en annan katalog, t. ex. externa användare som är från att tillhandahålla kedje organisationer eller andra affärs partner. Exempel:

- Organisationer kanske inte vet att alla enskilda personer i andra kataloger kan bjuda in dem
- Även om organisationer kunde bjuda in dessa användare, kanske organisationer inte kommer ihåg att hantera all användares åtkomst konsekvent

Hantering av Azure AD-berättigande kan hjälpa dig att lösa dessa utmaningar.

## <a name="what-can-i-do-with-entitlement-management"></a>Vad kan jag göra med hantering av rättigheter?

Här följer några funktioner för hantering av rättigheter:

- Skapa paket med relaterade resurser som användarna kan begära
- Definiera regler för hur du begär resurser och när åtkomsten upphör att gälla
- Styr livs cykeln för åtkomst för både interna och externa användare
- Delegera hantering av resurser
- Ange god kännare för att godkänna begär Anden
- Skapa rapporter för att spåra historik

För en översikt över identitets styrning och hantering av rättigheter kan du titta på följande video från antändnings 2018-konferensen:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Vilka resurser kan jag hantera?

Här följer de typer av resurser som du kan hantera åtkomst till med hantering av rättigheter:

- Azure AD-säkerhetsgrupper
- Office 365-grupper
- Azure AD Enterprise-program, inklusive SaaS-program och anpassade integrerade program som stöder Federation eller etablering
- Webbplats samlingar och platser i SharePoint Online

Du kan också styra åtkomsten till andra resurser som förlitar sig på Azure AD-säkerhetsgrupper eller Office 365-grupper.  Exempel:

- Du kan ge användarna licenser för Microsoft Office 365 med hjälp av en Azure AD-säkerhetsgrupp i ett Access-paket och konfigurera [gruppbaserad licensiering](../users-groups-roles/licensing-groups-assign.md) för gruppen
- Du kan ge användarna åtkomst till att hantera Azure-resurser med hjälp av en Azure AD-säkerhetsgrupp i ett Access-paket och skapa en [Azure-roll tilldelning](../../role-based-access-control/role-assignments-portal.md) för gruppen

## <a name="what-are-access-packages-and-policies"></a>Vad är åtkomst paket och principer?

Rättighets hantering introducerar konceptet med ett *Access-paket*. Ett Access-paket är ett paket med alla resurser som en användare behöver för att arbeta med ett projekt eller utföra sitt arbete. Resurserna innehåller åtkomst till grupper, program eller platser. Åtkomst paket används för att styra åtkomsten till dina interna anställda och även användare utanför organisationen. Åtkomst paket definieras i behållare som kallas *kataloger*.

Åtkomst paket innehåller också en eller flera *principer*. En princip definierar regler eller guardrails för åtkomst till ett Access-paket. Att aktivera en princip kräver att endast rätt användare beviljas åtkomst, till rätt resurser och för rätt tid.

![Komma åt paket och principer](./media/entitlement-management-overview/elm-overview-access-package.png)

Med ett Access-paket och dess principer definierar Access Package Manager:

- Resurser
- Roller som användarna behöver för resurserna
- Interna användare och externa användare som är berättigade att begära åtkomst
- Godkännande process och användare som kan godkänna eller neka åtkomst
- Varaktighet för användarens åtkomst

I följande diagram visas ett exempel på de olika elementen i hantering av rättigheter. Det visar två exempel på åtkomst paket.

- **Access paket 1** innehåller en enda grupp som en resurs. Åtkomst definieras med en princip som gör det möjligt för en uppsättning användare i katalogen att begära åtkomst.
- **Access paket 2** innehåller en grupp, ett program och en SharePoint Online-webbplats som resurser. Åtkomst definieras med två olika principer. Den första principen gör det möjligt för en uppsättning användare i katalogen att begära åtkomst. Den andra principen gör det möjligt för användare i en extern katalog att begära åtkomst.

![Översikt över rättighets hantering](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Externa användare

När du använder [Azure AD-inbjudan för Business-to-Business (B2B)](../b2b/what-is-b2b.md) måste du redan känna till e-postadresserna till de externa gäst användare som du vill ta med i resurs katalogen och arbeta med. Detta fungerar bra när du arbetar med ett mindre eller kortsiktigt projekt och du redan känner till alla deltagare, men det är svårare att hantera om du har många användare som du vill arbeta med eller om deltagarna ändras med tiden.  Till exempel kanske du arbetar med en annan organisation och har en kontakt punkt med den organisationen, men över tiden kommer ytterligare användare från organisationen också att ha åtkomst.

Med rättighets hantering kan du definiera en princip som tillåter användare från organisationer som du anger, som också använder Azure AD, för att kunna begära ett Access-paket. Du kan ange om godkännande krävs och ett förfallo datum för åtkomsten. Om godkännande krävs kan du också ange som god kännare för en eller flera användare från den externa organisation som du tidigare bjudit in – eftersom de sannolikt vet vilka externa användare från deras organisation som behöver åtkomst. När du har konfigurerat åtkomst paketet kan du skicka en länk till Access-paketet till din kontakt person i den externa organisationen. Den kontakten kan dela med andra användare i den externa organisationen, och de kan använda den här länken för att begära åtkomst paketet.  Användare från den organisationen som redan har bjudits in till din katalog kan också använda den länken.

När en begäran godkänns, etablerar rättighets hantering användaren med nödvändig åtkomst, vilket kan inkludera inbjudan till användaren om de inte redan finns i katalogen. Azure AD skapar automatiskt ett B2B-konto för dem.  Observera att en administratör kan ha tidigare begränsat vilka organisationer som tillåts för samarbete genom att ställa in en [B2B-lista med eller neka](../b2b/allow-deny-list.md) för att tillåta eller blockera inbjudningar till andra organisationer.  Om användaren inte tillåts av listan Tillåt eller blockera kommer de inte att bjudas in.

Eftersom du inte vill att den externa användarens åtkomst till senaste oändligt, anger du ett förfallo datum i principen, till exempel 180 dagar. Om åtkomsten inte förnyas efter 180 dagar tar rättighets hanteringen bort all åtkomst som är kopplad till det Access-paketet.  Om användaren som har bjudits in via rättighets hantering inte har några andra åtkomst paket tilldelningar, kommer deras B2B-konto att blockeras från att logga in i 30 dagar och därefter tas bort när de förlorar sin senaste tilldelning.  Detta förhindrar spridning av onödiga konton.  

## <a name="terminology"></a>Terminologi

För att bättre förstå hantering av rättigheter och dess dokumentation bör du läsa följande villkor.

| Term eller begrepp | Beskrivning |
| --- | --- |
| hantering av rättigheter | En tjänst som tilldelar, återkallar och administrerar åtkomst paket. |
| åtkomst paket | En samling behörigheter och principer till resurser som användare kan begära. Ett Access-paket finns alltid i en katalog. |
| åtkomstbegäran | En begäran om åtkomst till ett Access-paket. En begäran skickas vanligt vis genom ett arbets flöde. |
| policy | En uppsättning regler som definierar åtkomst livs cykeln, till exempel hur användare får åtkomst, vem som kan godkänna och hur länge användare har åtkomst. Exempel på principer är medarbetarnas åtkomst och extern åtkomst. |
| catalog | En behållare för relaterade resurser och åtkomst paket. |
| Allmän katalog | En inbyggd katalog som alltid är tillgänglig. För att lägga till resurser i den allmänna katalogen krävs vissa behörigheter. |
| resource | En till gång eller tjänst (till exempel en grupp, ett program eller en webbplats) som en användare kan beviljas behörighet till. |
| Resurs typ | Typ av resurs, som omfattar grupper, program och SharePoint Online-webbplatser. |
| resurs roll | En samling behörigheter som är kopplade till en resurs. |
| resurs katalog | En katalog som har en eller flera resurser som ska delas. |
| tilldelade användare | En tilldelning av ett Access-paket till en användare eller grupp. |
| Använd | Processen för att göra ett åtkomst paket tillgängligt för användare att begära. |

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specialiserade moln, till exempel Azure Government, Azure Germany och Azure Kina 21Vianet, är inte tillgängliga för användning i den här för hands versionen.

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa ditt första Access-paket](entitlement-management-access-package-first.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
