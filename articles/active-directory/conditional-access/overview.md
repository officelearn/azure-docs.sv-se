---
title: Vad är villkorsstyrd åtkomst i Azure Active Directory? | Microsoft Docs
description: Lär dig hur villkorsstyrd åtkomst i Azure Active Directory hjälper dig att implementera beslut om automatisk åtkomst som inte bara baseras på vem som försöker få åtkomst till en resurs, utan även hur en resurs används.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb1289ba615fea6e6c43b256da6978534d9edc7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799396"
---
# <a name="what-is-conditional-access-in-azure-active-directory"></a>Vad är villkorsstyrd åtkomst i Azure Active Directory?

Säkerhet är en mycket viktig fråga för organisationer som använder molnet. En viktig aspekt av molnsäkerhet är identitet och åtkomst när det gäller att hantera dina molnresurser. I en mobil- och molnorienterad värld kan användare komma åt organisationens resurser med hjälp av en mängd olika enheter och appar från var som helst. Därför räcker det inte att bara fokusera på vem som kan komma åt en resurs. För att uppnå rätt balans mellan säkerhet och produktivitet måste du även beakta hur åtkomsten till en resurs går till när du fattar ett beslut om åtkomstkontroll. Med villkorsstyrd åtkomst för Azure Active Directory (Azure AD) kan du hantera det här kravet. Villkorsstyrd åtkomst är en funktion i Azure Active Directory. Med villkorsstyrd åtkomst kan du implementera automatiserade beslut för åtkomstkontroll om åtkomst till dina molnappar, baserat på villkor.

Principer för villkorlig åtkomst tillämpas efter det att den första faktorautentiseringen har slutförts. Därför är inte villkorlig åtkomst avsedd som första åtgärd vid exempelvis DoS-attacker (överbelastningsattacker), men kan använda signaler från dessa händelser (t.ex. risknivån för inloggning, platsen för begäran och så vidare) för att fastställa åtkomst.  

![Kontroll](./media/overview/81.png)

Den här artikeln innehåller en konceptuell översikt av villkorsstyrd åtkomst i Azure AD.

## <a name="common-scenarios"></a>Vanliga scenarier

I en mobil- och molnorienterad värld ger Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. I och med den ökande mängden enheter (inklusive BYOD), arbete utanför företagsnätverk och SaaS-appar från tredje part står du inför två motsatta mål:

- Underlätta för användarna att vara produktiva oavsett tid och plats
- Se till att företagets resurser alltid är skyddade

Genom att använda principer för villkorsstyrd åtkomst kan du använda rätt åtkomstkontroller enligt de villkor som råder. Med villkorsstyrd åtkomst i Azure AD får du extra säkerhet vid behov, och det lägger sig inte i användarnas arbetsflöde när den säkerheten inte behövs.

Nedan följer några vanliga problem med åtkomst som villkorsstyrd åtkomst kan hjälpa dig med:

- **[Inloggningsrisk](conditions.md#sign-in-risk)**: Azure AD Identity Protection identifierar inloggningsrisker. Hur begränsar du åtkomsten om en identifierad inloggningsrisk indikerar en obehörig användare? Hur gör du om du behöver starkare bevis på att en inloggning har utförts av legitim användare? Hur gör du om du har misstankar som är starka nog att du vill även blockera enskilda användare från att komma åt en app?  

- **[Nätverksplats](location-condition.md)**: Azure AD kan användas från alla platser. Vad händer om en åtkomstförsök utförs från en nätverksplats som inte kontrolleras av IT-avdelningen? En kombination av användarnamn och lösenord kan vara tillräckligt bra som identitetsbevis för åtkomstförsök från företagsnätverket. Hur gör du om du kräver starkare identitetsbevis för åtkomstförsök som initieras från andra oväntade länder eller regioner i världen? Och kanske vill du till och med blockera åtkomstförsök från vissa regioner?  

- **[Enhetshantering](conditions.md#device-platforms)**: I Azure AD kan användarna komma åt molnappar från en mängd olika enheter, inklusive mobila och privata enheter. Vill du kräva att åtkomstförsök endast utförs med enheter som hanteras av IT-avdelningen? Kanske vill du till och med blockera vissa enhetstyper från att komma åt molnappar i din miljö?

- **[Klientprogram](conditions.md#client-apps)**: I dag kan du komma åt många molnappar med olika apptyper, till exempel webbaserade appar, mobilappar och skrivbordsappar. Vad händer om ett åtkomstförsök utförs med en klientapptyp som orsakar kända problem? Hur gör du om du kräver en enhet som hanteras av IT-avdelningen för vissa typer av appar?

Dessa frågor och relaterade svar representerar vanliga åtkomstscenarier för villkorsstyrd åtkomst i Azure AD.
Villkorsstyrd åtkomst är en funktion i Azure Active Directory som gör det möjligt att hantera åtkomstscenarier med hjälp av en principbaserad metod.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Principer för villkorsstyrd åtkomst

En principer för villkorsstyrd åtkomst är en definition av ett åtkomstscenario med hjälp av följande mönster:

![Kontroll](./media/overview/10.png)

**Gör då detta** anger svaret på din princip. Observera att målet med en princip för villkorsstyrd åtkomst inte att bevilja åtkomst till en molnapp. Att bevilja åtkomst till molnappar är har i Azure AD att göra med användartilldelningar. Med en princip för villkorsstyrd åtkomst kontrollerar du hur behöriga användare (användare som har beviljats åtkomst till en molnapp) kommer åt molnappar med vissa villkor. I ditt svar tillämpar du ytterligare krav såsom multifaktorautentisering, en hanterad enhet och mer. I samband med villkorsstyrd åtkomst i Azure AD kallas de krav som din princip tillämpar åtkomstkontroller. I den mest restriktiva formen kan din princip blockera åtkomst. Mer information finns på sidan om [åtkomstkontroller i villkorsstyrd åtkomst med Azure Active Directory](controls.md).

**Om det här händer** definierar anledningen till att utlösa principen. Den här orsaken kännetecknas av en grupp villkor som uppfylls. I villkorsstyrd åtkomst i Azure AD spelar de två tilldelningsvillkoren en särskild roll:

- **[Användare](conditions.md#users-and-groups)**: Den användare som utför ett åtkomstförsök (**Vem**).

- **[Molnappar](conditions.md#cloud-apps)**: Målet för ett åtkomstförsök (**Vad**).

De här två villkoren är obligatoriska i en princip för villkorsstyrd åtkomst. Utöver de två obligatoriska villkoren kan du även inkludera ytterligare villkor som beskriver hur åtkomstförsök utförs. Vanliga exempel använder mobila enheter eller platser som ligger utanför företagets nätverk. Mer information finns på sidan om [villkor i villkorsstyrd åtkomst med Azure Active Directory](conditions.md).

Kombinationen av villkor med dina åtkomstkontroller representerar en princip för villkorsstyrd åtkomst.

![Kontroll](./media/overview/51.png)

Med villkorsstyrd åtkomst i Azure AD kan du kontrollera hur behöriga användare kan komma åt dina molnappar. Målet med en princip för villkorsstyrd åtkomst är att tillämpa ytterligare åtkomstkontroller på ett åtkomstförsök till en molnapp som baseras på hur ett åtkomstförsök utförs.

En principbaserad metod för att skydda åtkomst till dina molnappar gör att du kan börja utforma principkraven för din miljö med hjälp av den struktur som beskrivs i den här artikeln utan att behöva bekymra dig om teknisk implementering.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Villkorsstyrd åtkomst för Azure AD och federerad autentisering

Principer för villkorsstyrd åtkomst fungerar sömlöst med [federerad autentisering](../../security/azure-ad-choose-authn.md#federated-authentication). Det här stödet innehåller alla villkor och kontroller och insyn i hur principen tillämpas på aktiv användarinloggningar med [Azure AD-rapportering](../reports-monitoring/concept-sign-ins.md).

*Federerad autentisering med Azure AD* innebär att betrodd autentiseringstjänst hanterar användarautentisering för Azure AD. En betrodd autentiseringstjänst är till exempel Active Directory Federation Services (AD FS) eller någon annan federationstjänst. I den här konfigurationen utförs primär användarautentisering i tjänsten och sedan används Azure AD till att logga in på enskilda program. Villkorsstyrd åtkomst för Azure AD tillämpas innan åtkomst beviljas för programmet som användaren får åtkomst till. 

När den konfigurerade principen för villkorsstyrd åtkomst kräver multifaktorautentisering använder Azure AD som standard Azure MFA. Om du använder federationstjänsten för MFA kan du konfigurera Azure AD att omdirigera till federationstjänsten när MFA krävs genom att ange `-SupportsMFA` till `$true` i [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Den här inställningen fungerar för tjänster för federerad autentisering som stöder MFA-utmaningsbegäran som utfärdats av Azure AD med hjälp av `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

När användaren har loggat in på tjänsten för federerad autentisering hanterar Azure AD andra principkrav, till exempel enhetsefterlevnad eller ett godkänt program.

## <a name="license-requirements-for-using-conditional-access"></a>Licenskrav för att använda villkorsstyrd åtkomst

Användning av villkorsstyrd åtkomst kräver en Azure AD Premium-licens. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att implementera villkorlig åtkomst i din miljö kan du läsa avsnittet om att [planera distribution med villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
