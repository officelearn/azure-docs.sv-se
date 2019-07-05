---
title: Vad är villkorlig åtkomst i Azure Active Directory? | Microsoft Docs
description: Läs om hur villkorlig åtkomst i Azure Active Directory hjälper dig att implementera automatisk åtkomst beslut som inte bara baserat på vem som försöker få åtkomst till en resurs, men också hur en resurs används.
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
ms.openlocfilehash: a5ef72f1db329d04809a1069c1916d1ffcfffe65
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509378"
---
# <a name="what-is-conditional-access"></a>Vad är villkorsstyrd åtkomst?

Säkerhet är en mycket viktig fråga för organisationer som använder molnet. En viktig aspekt av molnsäkerhet är identitet och åtkomst när det gäller att hantera dina molnresurser. I en mobil- och molnorienterad värld kan användare komma åt organisationens resurser med hjälp av en mängd olika enheter och appar från var som helst. Därför räcker det inte att bara fokusera på vem som kan komma åt en resurs. För att uppnå rätt balans mellan säkerhet och produktivitet måste du även beakta hur åtkomsten till en resurs går till när du fattar ett beslut om åtkomstkontroll. Med villkorlig åtkomst för Azure Active Directory (AD Azure), kan du lösa det här kravet. Villkorlig åtkomst är en funktion i Azure Active Directory. Du kan implementera automatiserade besluten om åtkomstkontroll för att komma åt dina appar i molnet som baseras på villkor med villkorlig åtkomst.

Principer för villkorlig åtkomst tillämpas efter den första-factor-autentiseringen har slutförts. Därför villkorlig åtkomst är inte avsedd som en första rad ansvarsfrihetsgrund för scenarier som attacker för denial of service (DoS), men kan använda signaler från dessa händelser (till exempel risknivå för inloggning, platsen för begäran och så vidare) för att fastställa åtkomst.  

![Kontroll](./media/overview/81.png)

Den här artikeln innehåller en översikt av villkorlig åtkomst i Azure AD.

## <a name="common-scenarios"></a>Vanliga scenarier

I en mobil- och molnorienterad värld ger Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. I och med den ökande mängden enheter (inklusive BYOD), arbete utanför företagsnätverk och SaaS-appar från tredje part står du inför två motsatta mål:

- Underlätta för användarna att vara produktiva oavsett tid och plats
- Se till att företagets resurser alltid är skyddade

Du kan använda rätt åtkomstkontroller på angivna villkor genom att använda principer för villkorlig åtkomst. Azure AD villkorlig åtkomst ger ökad säkerhet vid behov och ligger utanför användarnas sätt när det inte.

Nedan följer några vanliga problem för åtkomst som villkorlig åtkomst kan hjälpa dig med:

- **[Inloggningsrisk](conditions.md#sign-in-risk)** : Azure AD Identity Protection identifierar inloggningsrisker. Hur begränsar du åtkomsten om en identifierad inloggningsrisk indikerar en obehörig användare? Hur gör du om du behöver starkare bevis på att en inloggning har utförts av legitim användare? Hur gör du om du har misstankar som är starka nog att du vill även blockera enskilda användare från att komma åt en app?  
- **[Nätverksplats](location-condition.md)** : Azure AD kan användas från alla platser. Vad händer om en åtkomstförsök utförs från en nätverksplats som inte kontrolleras av IT-avdelningen? En kombination av användarnamn och lösenord kan vara tillräckligt bra som identitetsbevis för åtkomstförsök från företagsnätverket. Hur gör du om du kräver starkare identitetsbevis för åtkomstförsök som initieras från andra oväntade länder eller regioner i världen? Och kanske vill du till och med blockera åtkomstförsök från vissa regioner?  
- **[Enhetshantering](conditions.md#device-platforms)** : I Azure AD kan användarna komma åt molnappar från en mängd olika enheter, inklusive mobila och privata enheter. Vill du kräva att åtkomstförsök endast utförs med enheter som hanteras av IT-avdelningen? Kanske vill du till och med blockera vissa enhetstyper från att komma åt molnappar i din miljö?
- **[Klientprogram](conditions.md#client-apps)** : I dag kan du komma åt många molnappar med olika apptyper, till exempel webbaserade appar, mobilappar och skrivbordsappar. Vad händer om ett åtkomstförsök utförs med en klientapptyp som orsakar kända problem? Hur gör du om du kräver en enhet som hanteras av IT-avdelningen för vissa typer av appar?

Dessa frågor och svar relaterade representerar vanliga scenarier för åtkomst för Azure AD villkorlig åtkomst.
Villkorlig åtkomst är en funktion i Azure Active Directory som gör det möjligt att hantera åtkomst scenarier med hjälp av en principbaserad metod.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Principer för villkorlig åtkomst

Principer för villkorlig åtkomst är en definition av ett scenario för åtkomst med hjälp av följande mönster:

![Kontroll](./media/overview/10.png)


**Om det här händer** definierar anledningen till att utlösa principen. Den här orsaken kännetecknas av en grupp villkor som uppfylls. Spela upp en särskild roll i Azure AD villkorlig åtkomst, tilldelning av två villkor:

- **[Användare](conditions.md#users-and-groups)** : Den användare som utför ett åtkomstförsök (**Vem**).
- **[Molnappar](conditions.md#cloud-apps-and-actions)** : Målet för ett åtkomstförsök (**Vad**).

Dessa två villkor är obligatoriskt i en princip för villkorlig åtkomst. Utöver de två obligatoriska villkoren kan du även inkludera ytterligare villkor som beskriver hur åtkomstförsök utförs. Vanliga exempel använder mobila enheter eller platser som ligger utanför företagets nätverk. Mer information finns i [villkor i Azure Active Directory villkorsstyrd åtkomst](conditions.md).

Kombinationen av villkor med din åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/overview/51.png)

Med Azure AD villkorlig åtkomst kan du styra hur behöriga användare kan komma åt dina appar i molnet. Målet med en princip för villkorlig åtkomst är att tillämpa ytterligare åtkomstkontroller på ett åtkomstförsök till en molnapp baserat på hur ett åtkomstförsök utförs.

En principbaserad metod för att skydda åtkomst till dina molnappar gör att du kan börja utforma principkraven för din miljö med hjälp av den struktur som beskrivs i den här artikeln utan att behöva bekymra dig om teknisk implementering.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Azure AD villkorlig åtkomst och federerad autentisering

Principer för villkorlig åtkomst fungerar sömlöst med [federerad autentisering](../../security/azure-ad-choose-authn.md#federated-authentication). Det här stödet innehåller alla villkor och kontroller och insyn i hur principen tillämpas på aktiv användarinloggningar med [Azure AD-rapportering](../reports-monitoring/concept-sign-ins.md).

*Federerad autentisering med Azure AD* innebär att betrodd autentiseringstjänst hanterar användarautentisering för Azure AD. En betrodd autentiseringstjänst är till exempel Active Directory Federation Services (AD FS) eller någon annan federationstjänst. I den här konfigurationen utförs primär användarautentisering i tjänsten och sedan används Azure AD till att logga in på enskilda program. Azure AD villkorlig åtkomst tillämpas innan åtkomst beviljas till programmet som användaren har åtkomst till. 

När den konfigurerade principen för villkorlig åtkomst kräver multifaktorautentisering, som Azure AD standard med hjälp av Azure MFA. Om du använder federationstjänsten för MFA kan du konfigurera Azure AD att omdirigera till federationstjänsten när MFA krävs genom att ange `-SupportsMFA` till `$true` i [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Den här inställningen fungerar för tjänster för federerad autentisering som stöder MFA-utmaningsbegäran som utfärdats av Azure AD med hjälp av `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

När användaren har loggat in på tjänsten för federerad autentisering hanterar Azure AD andra principkrav, till exempel enhetsefterlevnad eller ett godkänt program.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Kunder med [licenser för Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description) även ha åtkomst till funktioner för villkorlig åtkomst. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att implementera villkorlig åtkomst i din miljö, se [planera distributionen av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
