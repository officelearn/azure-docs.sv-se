---
title: Om tekniska profiler i Azure Active Directory B2C anpassade principer | Microsoft Docs
description: Lär dig mer om hur tekniska profiler används i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f90b69cde4a961457c987f004e2605e6884bf323
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063331"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Om tekniska profiler i Azure Active Directory B2C anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk profil innehåller ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter med hjälp av en anpassad princip i Azure Active Directory B2C (Azure AD B2C). Tekniska profiler används för att kommunicera med din Azure AD B2C-klient, för att skapa en användare eller läsa en användar profil. En teknisk profil kan självkontrolleras för att aktivera interaktion med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in och sedan återge sidan för registrerings sidan eller lösen ords återställningen.

## <a name="type-of-technical-profiles"></a>Typ av tekniska profiler

En teknisk profil möjliggör följande typer av scenarier:

- [Azure Active Directory](active-directory-technical-profile.md) -ger stöd för Azure Active Directory B2C användar hantering.
- [JWT](jwt-issuer-technical-profile.md) -token-utfärdare – avger en JWT-token som returneras tillbaka till det förlitande part programmet.
- **Telefon Factor Provider** – Multi-Factor Authentication.
- [OAuth1](oauth1-technical-profile.md) -Federation med valfri OAuth 1,0-protokoll identitets leverantör.
- [OAuth2](oauth2-technical-profile.md) -Federation med valfri OAuth 2,0-protokoll identitets leverantör.
- [OpenID Connect](openid-connect-technical-profile.md) -Federation med valfri OpenID Connect Protocol Identity Provider.
- [Anspråks omvandling](claims-transformation-technical-profile.md) -anrop för anrop av utdata för att manipulera anspråks värden, validera anspråk eller ange standardvärden för en uppsättning utgående anspråk.
- [RESTful-Provider](restful-technical-profile.md) – anrop till REST API tjänster, till exempel verifiera användarindata, utöka användar data eller integrera med branschspecifika program.
- [SAML2](saml-technical-profile.md) -Federation med valfri SAML-protokoll identitets leverantör.
- [](self-asserted-technical-profile.md) Självkontrollerad – interagera med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in, återge registrerings sidan eller lösen ords återställning.
- [Sessionshantering](active-directory-b2c-reference-sso-custom.md) – hantera olika typer av sessioner.
- **Application Insights**

## <a name="technical-profile-flow"></a>Tekniskt profil flöde

Alla typer av tekniska profiler delar samma koncept. Du kan skicka indata-anspråk, köra anspråk och kommunicera med den konfigurerade parten, till exempel en identitetsprovider, REST API eller Azure AD Directory-tjänster. När processen har slutförts returnerar den tekniska profilen utgående anspråk och kan köra transformering av utgående anspråk. Följande diagram visar hur omvandlingar och mappningar som refereras i den tekniska profilen bearbetas. Oavsett vilken part den tekniska profilen interagerar med, efter det att en anspråks omvandling har körts, lagras de utgående anspråken från den tekniska profilen omedelbart i anspråks säcken.

![Diagram som illustrerar det tekniska profil flödet](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** -indata-anspråk för varje utgående anspråks [omvandling](claimstransformations.md) hämtas från anspråks säcken och efter körningen tas de utgående anspråken tillbaka i anspråks säcken. De utgående anspråken för en inmatnings anspråks omvandling kan vara inmatade anspråk för en efterföljande omvandling av anspråk.
2. **InputClaims** -anspråk hämtas från anspråks säcken och används för den tekniska profilen. En självkontrollerad [teknisk profil](self-asserted-technical-profile.md) använder till exempel indata-anspråk för att fylla i de utgående anspråk som användaren tillhandahåller. En REST API teknisk profil använder ingångs anspråk för att skicka indataparametrar till REST API-slutpunkten. Azure Active Directory använder ingångs anspråk som unik identifierare för att läsa, uppdatera eller ta bort ett konto.
3. **Körning av teknisk profil** – den tekniska profilen utbyter anspråk med den konfigurerade parten. Exempel:
    - Omdirigera användaren till identitets leverantören för att slutföra inloggningen. Efter lyckad inloggning återgår användaren och den tekniska profil körningen fortsätter.
    - Anropa ett REST API när du skickar parametrar som InputClaims och få tillbaka information som OutputClaims.
    - Skapa eller uppdatera användar kontot.
    - Skickar och verifierar MFA SMS-meddelandet.
4. **ValidationTechnicalProfiles** – för en [självkontrollerad teknisk profil](self-asserted-technical-profile.md)kan du anropa en [teknisk profil för verifiering](validation-technical-profile.md)av Indatatyp. Den tekniska verifierings profilen verifierar de data som profileras av användaren och returnerar ett fel meddelande eller OK, med eller utan utgående anspråk. Innan Azure AD B2C skapar ett nytt konto kontrollerar det till exempel om användaren redan finns i katalog tjänsterna. Du kan anropa en REST API teknisk profil för att lägga till din egen affärs logik.<p>Omfattningen av de utgående anspråken för en verifierings teknisk profil är begränsad till den tekniska profilen som anropar den tekniska profilen för verifiering och andra tekniska profiler för verifiering under samma tekniska profil. Om du vill använda de utgående anspråken i nästa Orchestration-steg måste du lägga till de utgående anspråken till den tekniska profilen som anropar den tekniska profilen för verifiering.
5. **OutputClaims** -anspråk returneras tillbaka till anspråks säcken. Du kan använda de här anspråken i nästa steg för att dirigera eller utföra anspråk på utdata.
6. **OutputClaimsTransformations** -indata-anspråk för varje utgående anspråks [omvandling](claimstransformations.md) hämtas från anspråks säcken. De utgående anspråken för den tekniska profilen från föregående steg kan vara inmatnings anspråk för en transformering av utdata-anspråk. Efter körningen sätts de utgående anspråken tillbaka i anspråks säcken. De utgående anspråken för en transformering av utgående anspråk kan också vara inmatnings anspråk för en efterföljande transformering av utgående anspråk.
7. **SSO-sessionshantering för enkel inloggning (SSO)**  - [styr](active-directory-b2c-reference-sso-custom.md) interaktionen med en användare när användaren redan har autentiserats. Administratören kan till exempel kontrol lera om valet av identitets leverantörer visas eller om information om lokalt konto måste anges igen.

En teknisk profil kan ärva från en annan teknisk profil för att ändra inställningar eller lägga till nya funktioner.  **IncludeTechnicalProfile** -elementet är en referens till den grundläggande tekniska profil som en teknisk profil härleds från.

Till exempel innehåller den tekniska profilen **AAD-UserReadUsingAlternativeSecurityId-noerror** filen **AAD-UserReadUsingAlternativeSecurityId**. Den här tekniska profilen anger **RaiseErrorIfClaimsPrincipalDoesNotExist** -objektet för `true`metadata till och genererar ett fel om ett socialt konto inte finns i katalogen. **AAD-UserReadUsingAlternativeSecurityId – noerror** åsidosätter det här beteendet och inaktiverar fel meddelandet om användaren inte fanns.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** innehåller den `AAD-Common` tekniska profilen.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Både **AAD-UserReadUsingAlternativeSecurityId-noerror** och **AAD-UserReadUsingAlternativeSecurityId** anger inte det **protokoll** element som krävs eftersom det har angetts i **AAD-common** Technical Profile.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

En teknisk profil kan innehålla eller ärva en annan teknisk profil, som kan innehålla en annan. Det finns ingen gräns för antalet nivåer. Beroende på affärs kraven kan din användar resa kalla **AAD-UserReadUsingAlternativeSecurityId** som genererar ett fel om det inte finns något socialt konto för användare eller **AAD-UserReadUsingAlternativeSecurityId-no-fel** som inte generera ett fel.











