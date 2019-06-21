---
title: Vad är Azure AD rättigheten management? (Förhandsversion) – Azure Active Directory
description: Få en översikt över Azure Active Directory rättigheten hantering och hur du kan använda den för att hantera åtkomst till grupper, program och SharePoint Online-platser för interna och externa användare.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb4001e9496d31d9c2879721f8cf8e26b74ddf3
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204541"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Vad är Azure AD rättigheten management? (Förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anställda i organisationer behöver åtkomst till olika grupper, program och webbplatser att utföra sitt arbete. Det är svårt att hantera den här åtkomsten. I de flesta fall finns ingen ordnad lista över alla resurser som en användare behöver för ett projekt. Projektledaren har en god förståelse för de resurser som behövs, personer som ingår och hur länge varar i projektet. Dock har projektledaren vanligtvis inte behörighet att godkänna eller bevilja åtkomst till andra. Det här scenariot hämtar mer komplicerad vid försök att arbeta med externa enskilda personer eller företag.

Azure Active Directory (Azure AD) rättigheten management kan hjälpa dig att hantera åtkomst till grupper, program och SharePoint Online-platser för interna användare och användare utanför organisationen.

## <a name="why-use-entitlement-management"></a>Varför använda rätt management?

Företag kan du ofta står inför när de har åtkomst till resurser som:

- Användare kanske inte vet vad de ska ha åtkomst
- Användare kan ha svårt att hitta rätt personer eller rätt resurser
- När användare hitta och få åtkomst till en resurs, kan de håller att få åtkomst till längre än vad som krävs för företag

Dessa problem är beredda för användare som behöver åtkomst från andra kataloger, till exempel externa användare från strömförsörjning kedja organisationer eller andra affärspartner. Exempel:

- Organisationer kan inte känner till alla enskilda användare i andra kataloger att bjuda in dem
- Även om organisationer har bjuda in dessa användare organisationer inte kommer ihåg att hantera alla användarens åtkomst konsekvent

Azure AD rättigheten management kan hjälpa dig att lösa dessa problem.

## <a name="what-can-i-do-with-entitlement-management"></a>Vad kan jag göra med rätt management?

Här följer några av funktionerna för hantering av behörighet:

- Skapa paket av relaterade resurser som användarna kan begära
- Definiera regler för hur du begär resurser och när åtkomst upphör att gälla
- Styr livscykeln för både interna och externa användare
- Delegera hanteringen av resurser
- Ange godkännare att godkänna begäranden
- Skapa rapporter för att spåra historiken

Titta på följande videoklipp från 2018 Ignite-konferensen för en översikt över Identitetsstyrning och rättigheten hantering:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Vilka resurser kan hantera?

Här är typerna av resurser som du kan hantera åtkomst till med hantering av behörighet:

- Azure AD-säkerhetsgrupper
- Office 365-grupper
- Azure AD företagsprogram, inklusive SaaS-program och anpassade-integrerade program som stöder federation eller etablering
- SharePoint Online-webbplatssamlingar och platser

Du kan också styra åtkomst till andra resurser som förlitar sig på Azure AD-säkerhetsgrupper eller Office 365-grupper.  Exempel:

- Du kan ge användarlicenser för Microsoft Office 365 använder en Azure AD-säkerhetsgrupp i ett paket för åtkomst och konfigurerar [gruppbaserad licensiering](../users-groups-roles/licensing-groups-assign.md) för gruppen
- Du kan ge användare åtkomst till att hantera Azure-resurser med hjälp av en Azure AD-säkerhetsgrupp i ett paket för åtkomst och skapa en [Azure rolltilldelning](../../role-based-access-control/role-assignments-portal.md) för gruppen

## <a name="what-are-access-packages-and-policies"></a>Vad är access paket och principer?

Berättigande management introduceras konceptet med en *åtkomst paketet*. Ett åtkomst-paket är ett paket med alla resurser som en användare behöver för att arbeta med ett projekt eller utföra sitt arbete. Resurser omfattar tillgång till grupper, program eller webbplatser. Åtkomst paket används för att styra åtkomst för internt anställda och även användare utanför organisationen. Åtkomst-paket har definierats i behållare som kallas *kataloger*.

Åtkomst paket även innehålla en eller flera *principer*. En princip definierar regler eller guardrails att komma åt ett åtkomst-paket. När en principen framtvingar att endast rätt användare har beviljats åtkomst till rätt resurser med rätt mängd tid.

![Paket för åtkomst och principer](./media/entitlement-management-overview/elm-overview-access-package.png)

Med en åtkomst-paketet och dess principer definierar åtkomsten package manager:

- Resurser
- Roller användarna behöver för resurser
- Interna användare och externa användare som är berättigade att begära åtkomst
- Godkännandeprocessen och de användare som kan godkänna eller neka åtkomst
- Varaktigheten för användarens åtkomst

Följande diagram visar ett exempel på de olika elementen i rätt management. Den visar två exempel åtkomst paket.

- **Åtkomst till paketet 1** innehåller en grupp som en resurs. Åtkomst har definierats med en princip som gör att en uppsättning användare i katalogen för att begära åtkomst.
- **Åtkomst till paketet 2** innehåller en grupp, ett program och en SharePoint Online-webbplats som resurser. Åtkomst har definierats med två olika principer. Den första principen gör det möjligt för en uppsättning användare i katalogen för att begära åtkomst. Den andra principen gör det möjligt för användare i en extern katalog att begära åtkomst.

![Översikt över berättigande](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Externa användare

När du använder den [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) bjuda in upplevelse, måste du redan kan e-postadresserna för de externa gästanvändare som du vill sätta i din katalog i resursen och arbeta med. Det här fungerar bra när du arbetar med ett mindre eller kortsiktiga projekt och du redan vet alla deltagare, men det är svårare att hantera om du har många användare som du vill arbeta med eller om deltagarna ändras med tiden.  Exempel: du kan arbeta med en annan organisation och har en kontaktpunkt med organisationen, men med tiden måste ytterligare användare från organisationen också ha tillgång.

Med rätt management kan definiera du en princip som tillåter användare från organisationer som du anger, och som också använder Azure AD för att kunna begära ett åtkomst-paket. Du kan ange om godkännande krävs och ett förfallodatum för åtkomst. Om godkännande krävs, kan du också ange som godkännare en eller flera användare från den externa organisation som du tidigare inbjuden - eftersom det är troligt att veta vilka externa användare från organisationen behöver åtkomst. När du har konfigurerat paketet åtkomst kan skicka du en länk till paketet åtkomst till kontaktpersonen hos extern organisation. Kontakten kan dela med andra användare i externa organisationen och de kan använda den här länken för att begära åtkomst till paketet.  Användare från organisationen som har redan bjudits in till din katalog kan också använda länken.

När en begäran har godkänts, kommer rättigheten management etablera användare med den åtkomst som krävs, som kan innehålla bjuda in användaren om de inte redan är i din katalog. Azure AD skapar automatiskt en B2B-konto för dem.  Observera att en administratör kan ha tidigare begränsade vilka organisationer är tillåtna för samarbete, genom att ange en [B2B tillåter eller nekar lista](../b2b/allow-deny-list.md) att tillåta eller blockera inbjudningar till andra organisationer.  Om användaren inte tillåts av listan Tillåt eller blockera och sedan de inte kommer att bjuda in.

Eftersom du inte vill att den externa användaren åtkomst till att alltid ange ett förfallodatum i principen, till exempel 180 dagar. Efter 180 dagar, om deras åtkomst inte förnyas tar rättigheten management bort all åtkomst som är associerade med paketet åtkomst.  Om den användare som har bjudits in via rättighet hantering inte använder andra åtkomst paketet, kommer sedan när de förlorar sin senaste tilldelningen sitt B2B-konto att blockeras från att logga in i 30 dagar och därefter bort.  Detta förhindrar att en ökning av onödiga konton.  

## <a name="terminology"></a>Terminologi

För att bättre förstå rättigheten hanterings- och dess dokumentation, bör du granska följande villkor.

| Term eller begrepp | Beskrivning |
| --- | --- |
| hantering av behörighet | En tjänst som tilldelar återkallar och administrerar åtkomst paket. |
| åtkomst-paketet | En uppsättning behörigheter och principer till resurser som användarna kan begära. En åtkomst-paketet finns alltid i en katalog. |
| åtkomstbegäran | En begäran om åtkomst till en åtkomst-paketet. En begäran går vanligtvis igenom ett arbetsflöde. |
| policy | En uppsättning regler som definierar åtkomst livscykel, till exempel hur användare får åtkomst, som kan godkänna och hur länge användarna har åtkomst. Exempel på principer är medarbetarnas åtkomst och extern åtkomst. |
| catalog | En behållare för relaterade resurser och åtkomst-paket. |
| Allmän catalog | En inbyggd katalog som alltid är tillgänglig. Lägg till resurser i den allmänna katalogen måste du ha vissa behörigheter. |
| resource | En tillgång eller en tjänst (till exempel en grupp, program eller en webbplats) som en användare kan beviljas behörigheter till. |
| Resurstyp | Typ av resurs som innehåller grupper, program och SharePoint Online-platser. |
| resurs-roll | En uppsättning behörigheter som är associerade med en resurs. |
| resursbiblioteket | En katalog som har en eller flera resurser för att dela. |
| tilldelade användare | En tilldelning av ett paket för åtkomst till en användare eller grupp. |
| Aktivera | Processen att göra ett åtkomst-paket tillgängliga för användare att begära. |

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specialiserad moln, till exempel Azure Government, Azure Tyskland och Azure Kina 21Vianet, är inte för närvarande tillgängliga för användning i den här förhandsversionen.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa ditt första åtkomst-paket](entitlement-management-access-package-first.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
