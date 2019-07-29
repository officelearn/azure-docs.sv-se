---
title: Vad är villkorlig åtkomst i Azure Active Directory? | Microsoft Docs
description: Lär dig hur villkorlig åtkomst i Azure Active Directory hjälper dig att implementera automatiska åtkomst beslut som inte bara baseras på vem som försöker komma åt en resurs, men också hur en resurs går att komma åt.
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
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608092"
---
# <a name="what-is-conditional-access"></a>Vad är villkorsstyrd åtkomst?

Säkerhet är en mycket viktig fråga för organisationer som använder molnet. En viktig aspekt av molnsäkerhet är identitet och åtkomst när det gäller att hantera dina molnresurser. I en mobil- och molnorienterad värld kan användare komma åt organisationens resurser med hjälp av en mängd olika enheter och appar från var som helst. Därför räcker det inte att bara fokusera på vem som kan komma åt en resurs. För att uppnå rätt balans mellan säkerhet och produktivitet måste du även beakta hur åtkomsten till en resurs går till när du fattar ett beslut om åtkomstkontroll. Med Azure Active Directory (Azure AD) villkorlig åtkomst kan du hantera det här kravet. Villkorlig åtkomst är en funktion i Azure Active Directory. Med villkorlig åtkomst kan du implementera automatiska åtkomst kontroll beslut för att komma åt dina molnappar som baseras på villkor.

Principer för villkorlig åtkomst tillämpas när den första faktorn har slutförts. Därför är villkorlig åtkomst inte avsedd som ett första rad skydd för scenarier som denial-of-service (DoS)-attacker, men kan använda signaler från dessa händelser (till exempel inloggnings risk nivån, platsen för begäran och så vidare) för att fastställa åtkomst.  

![Kontroll](./media/overview/81.png)

Den här artikeln ger en konceptuell översikt över villkorlig åtkomst i Azure AD.

## <a name="common-scenarios"></a>Vanliga scenarier

I en mobil- och molnorienterad värld ger Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. I och med den ökande mängden enheter (inklusive BYOD), arbete utanför företagsnätverk och SaaS-appar från tredje part står du inför två motsatta mål:

- Underlätta för användarna att vara produktiva oavsett tid och plats
- Se till att företagets resurser alltid är skyddade

Genom att använda principer för villkorlig åtkomst kan du tillämpa rätt åtkomst kontroller under de nödvändiga villkoren. Med villkorlig åtkomst i Azure AD får du ytterligare säkerhet när det behövs.

Nedan följer några vanliga åtkomst problem som villkorlig åtkomst kan hjälpa dig med:

- **[Inloggningsrisk](conditions.md#sign-in-risk)** : Azure AD Identity Protection identifierar inloggningsrisker. Hur begränsar du åtkomsten om en identifierad inloggningsrisk indikerar en obehörig användare? Hur gör du om du behöver starkare bevis på att en inloggning har utförts av legitim användare? Hur gör du om du har misstankar som är starka nog att du vill även blockera enskilda användare från att komma åt en app?  
- **[Nätverksplats](location-condition.md)** : Azure AD kan användas från alla platser. Vad händer om en åtkomstförsök utförs från en nätverksplats som inte kontrolleras av IT-avdelningen? En kombination av användarnamn och lösenord kan vara tillräckligt bra som identitetsbevis för åtkomstförsök från företagsnätverket. Hur gör du om du kräver starkare identitetsbevis för åtkomstförsök som initieras från andra oväntade länder eller regioner i världen? Och kanske vill du till och med blockera åtkomstförsök från vissa regioner?  
- **[Enhetshantering](conditions.md#device-platforms)** : I Azure AD kan användarna komma åt molnappar från en mängd olika enheter, inklusive mobila och privata enheter. Vill du kräva att åtkomstförsök endast utförs med enheter som hanteras av IT-avdelningen? Kanske vill du till och med blockera vissa enhetstyper från att komma åt molnappar i din miljö?
- **[Klientprogram](conditions.md#client-apps)** : I dag kan du komma åt många molnappar med olika apptyper, till exempel webbaserade appar, mobilappar och skrivbordsappar. Vad händer om ett åtkomstförsök utförs med en klientapptyp som orsakar kända problem? Hur gör du om du kräver en enhet som hanteras av IT-avdelningen för vissa typer av appar?

Dessa frågor och relaterade svar representerar vanliga åtkomst scenarier för villkorlig åtkomst i Azure AD.
Villkorlig åtkomst är en funktion i Azure Active Directory som gör att du kan hantera åtkomst scenarier med hjälp av en princip-baserad metod.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Principer för villkorlig åtkomst

En princip för villkorlig åtkomst är en definition av ett åtkomst scenario med följande mönster:

![Kontroll](./media/overview/10.png)


**Om det här händer** definierar anledningen till att utlösa principen. Den här orsaken kännetecknas av en grupp villkor som uppfylls. I villkorlig åtkomst för Azure AD, spelar de två tilldelnings villkoren en särskild roll:

- **[Användare](conditions.md#users-and-groups)** : Den användare som utför ett åtkomstförsök (**Vem**).
- **[Molnappar](conditions.md#cloud-apps-and-actions)** : Målet för ett åtkomstförsök (**Vad**).

Dessa två villkor är obligatoriska i en princip för villkorlig åtkomst. Utöver de två obligatoriska villkoren kan du även inkludera ytterligare villkor som beskriver hur åtkomstförsök utförs. Vanliga exempel använder mobila enheter eller platser som ligger utanför företagets nätverk. Mer information finns i [villkor i Azure Active Directory villkorlig åtkomst](conditions.md).

Kombinationen av villkor med dina åtkomst kontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/overview/51.png)

Med villkorlig åtkomst i Azure AD kan du styra hur behöriga användare får åtkomst till dina molnappar. Syftet med en princip för villkorlig åtkomst är att genomdriva ytterligare åtkomst kontroller för ett åtkomst försök till en molnbaserad app, baserat på hur ett åtkomst försök utförs.

En principbaserad metod för att skydda åtkomst till dina molnappar gör att du kan börja utforma principkraven för din miljö med hjälp av den struktur som beskrivs i den här artikeln utan att behöva bekymra dig om teknisk implementering.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Villkorlig åtkomst för Azure AD och federerad autentisering

Principer för villkorlig åtkomst fungerar sömlöst med [federerad autentisering](../../security/fundamentals/choose-ad-authn.md#federated-authentication). Det här stödet omfattar alla villkor och kontroller och insyn i hur principen tillämpas på aktiva användar inloggningar med hjälp av [Azure AD repor ting](../reports-monitoring/concept-sign-ins.md).

*Federerad autentisering med Azure AD* innebär att betrodd autentiseringstjänst hanterar användarautentisering för Azure AD. En betrodd autentiseringstjänst är till exempel Active Directory Federation Services (AD FS) eller någon annan federationstjänst. I den här konfigurationen utförs primär användarautentisering i tjänsten och sedan används Azure AD till att logga in på enskilda program. Villkorlig åtkomst i Azure AD tillämpas innan åtkomst beviljas till det program som användaren har åtkomst till. 

När den konfigurerade principen för villkorlig åtkomst kräver Multi-Factor Authentication använder Azure AD som standard Azure MFA. Om du använder federationstjänsten för MFA kan du konfigurera Azure AD att omdirigera till federationstjänsten när MFA krävs genom att ange `-SupportsMFA` till `$true` i [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Den här inställningen fungerar för tjänster för federerad autentisering som stöder MFA-utmaningsbegäran som utfärdats av Azure AD med hjälp av `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

När användaren har loggat in på tjänsten för federerad autentisering hanterar Azure AD andra principkrav, till exempel enhetsefterlevnad eller ett godkänt program.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Kunder med [Microsoft 365 Business licenser](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst. 

## <a name="next-steps"></a>Nästa steg

Information om hur du implementerar villkorlig åtkomst i din miljö finns i [Planera din distribution av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
