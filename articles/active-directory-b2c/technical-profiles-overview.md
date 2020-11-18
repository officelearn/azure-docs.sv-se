---
title: Översikt över tekniska profiler i anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur tekniska profiler används i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d61471e07dda8fcf0c715dcffe85ad3e39ed8ac3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840380"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Om tekniska profiler i Azure Active Directory B2C anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk profil innehåller ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter med hjälp av en anpassad princip i Azure Active Directory B2C (Azure AD B2C). Tekniska profiler används för att kommunicera med din Azure AD B2C-klient, för att skapa en användare eller läsa en användar profil. En teknisk profil kan självkontrolleras för att aktivera interaktion med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in och sedan återge sidan för registrerings sidan eller lösen ords återställningen.

## <a name="type-of-technical-profiles"></a>Typ av tekniska profiler

En teknisk profil möjliggör följande typer av scenarier:

- [Application Insights](application-insights-technical-profile.md) att skicka händelse data till [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) -ger stöd för Azure Active Directory B2C användar hantering.
- [Azure ad Multi-Factor Authentication](multi-factor-auth-technical-profile.md) – ger stöd för att verifiera ett telefonnummer med hjälp av azure AD Multi-Factor Authentication (MFA). 
- [Anspråks omvandling](claims-transformation-technical-profile.md) -anrop för anrop av utdata för att manipulera anspråks värden, validera anspråk eller ange standardvärden för en uppsättning utgående anspråk.
- [ID-token](id-token-hint.md) – verifierar `id_token_hint` JWT-tokens signatur, utfärdarens namn och token-målgruppen och extraherar anspråket från den inkommande token.
- [JWT-token-utfärdare](jwt-issuer-technical-profile.md) – avger en JWT-token som returneras tillbaka till det förlitande part programmet.
- [OAuth1](oauth1-technical-profile.md) -Federation med valfri OAuth 1,0-protokoll identitets leverantör.
- [OAuth2](oauth2-technical-profile.md) -Federation med valfri OAuth 2,0-protokoll identitets leverantör.
- [Ett eng ång slö sen ord](one-time-password-technical-profile.md) – ger stöd för att hantera genereringen och verifieringen av ett eng ång slö sen ord.
- [OpenID Connect](openid-connect-technical-profile.md) -Federation med valfri OpenID Connect Protocol Identity Provider.
- [Telefon faktor](phone-factor-technical-profile.md) – stöd för registrering och verifiering av telefonnummer.
- [RESTful-Provider](restful-technical-profile.md) – anrop till REST API tjänster, till exempel verifiera användarindata, utöka användar data eller integrera med branschspecifika program.
- [SAML Identity Provider](saml-identity-provider-technical-profile.md) – Federation med valfri SAML-protokoll identitets leverantör.
- [Utfärdare av SAML-token](saml-issuer-technical-profile.md) – genererar en SAML-token som returneras tillbaka till den förlitande part appen.
- [Självkontrollerad](self-asserted-technical-profile.md) – interagera med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in, återge registrerings sidan eller lösen ords återställning.
- [Sessionshantering](custom-policy-reference-sso.md) – hantera olika typer av sessioner.

## <a name="technical-profile-flow"></a>Tekniskt profil flöde

Alla typer av tekniska profiler delar samma koncept. Du kan skicka indata-anspråk, köra anspråk och kommunicera med den konfigurerade parten, till exempel en identitetsprovider, REST API eller Azure AD Directory-tjänster. När processen har slutförts returnerar den tekniska profilen utgående anspråk och kan köra transformering av utgående anspråk. Följande diagram visar hur omvandlingar och mappningar som refereras i den tekniska profilen bearbetas. Oavsett vilken part den tekniska profilen interagerar med, efter det att en anspråks omvandling har körts, lagras de utgående anspråken från den tekniska profilen omedelbart i anspråks säcken.

![Diagram som illustrerar det tekniska profil flödet](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **Hantering av enkel inloggning (SSO)** – återställer den tekniska profilens sessionstillstånd med hjälp av SSO- [sessionshantering](custom-policy-reference-sso.md).
1. **Transformering** av inmatade anspråk-inloggade anspråk för varje inloggad [anspråks omvandling](claimstransformations.md) hämtas från anspråks säcken.  De utgående anspråken för en inmatnings anspråks omvandling kan vara inmatade anspråk för en efterföljande omvandling av anspråk.
1. **Ingående anspråk** – anspråk hämtas från anspråks säcken och används för den tekniska profilen. En [självkontrollerad teknisk profil](self-asserted-technical-profile.md) använder till exempel indata-anspråk för att fylla i de utgående anspråk som användaren tillhandahåller. En REST API teknisk profil använder ingångs anspråk för att skicka indataparametrar till REST API-slutpunkten. Azure Active Directory använder ingångs anspråk som unik identifierare för att läsa, uppdatera eller ta bort ett konto.
1. **Körning av teknisk profil** – den tekniska profilen utbyter anspråk med den konfigurerade parten. Exempel:
    - Omdirigera användaren till identitets leverantören för att slutföra inloggningen. Efter lyckad inloggning återgår användaren och den tekniska profil körningen fortsätter.
    - Anropa ett REST API när du skickar parametrar som InputClaims och få tillbaka information som OutputClaims.
    - Skapa eller uppdatera användar kontot.
    - Skickar och verifierar MFA SMS-meddelandet.
1. **Tekniska profiler för verifiering** – en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) kan anropa [verifierings tekniska profiler](validation-technical-profile.md). Den tekniska verifierings profilen verifierar de data som profileras av användaren och returnerar ett fel meddelande eller OK, med eller utan utgående anspråk. Innan Azure AD B2C skapar ett nytt konto kontrollerar det till exempel om användaren redan finns i katalog tjänsterna. Du kan anropa en REST API teknisk profil för att lägga till din egen affärs logik.<p>Omfattningen av de utgående anspråken för en verifierings teknisk profil är begränsad till den tekniska profilen som anropar den tekniska profilen för verifiering. och andra tekniska profiler för verifiering under samma tekniska profil. Om du vill använda de utgående anspråken i nästa Orchestration-steg måste du lägga till de utgående anspråken till den tekniska profilen som anropar den tekniska profilen för verifiering.
1. **Utgående anspråk** – anspråk returneras tillbaka till anspråks säcken. Du kan använda de här anspråken i nästa steg för att dirigera eller utföra anspråk på utdata.
1. Transformeringar av **utgående anspråk** – indata-anspråk för varje utgående [anspråks omvandling](claimstransformations.md) hämtas från anspråks säcken. De utgående anspråken för den tekniska profilen från föregående steg kan vara inmatnings anspråk för en transformering av utdata-anspråk. Efter körningen sätts de utgående anspråken tillbaka i anspråks säcken. De utgående anspråken för en transformering av utgående anspråk kan också vara inmatnings anspråk för en efterföljande transformering av utgående anspråk.
1. **Hantering av enkel inloggning (SSO)** – behåller teknisk profils data till sessionen med hjälp av [SSO-sessionshantering](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inkluderad teknik profil

En teknisk profil kan innehålla en annan teknisk profil för att ändra inställningar eller lägga till nya funktioner.  `IncludeTechnicalProfile`Elementet är en referens till den grundläggande tekniska profil som en teknisk profil härleds från. Det finns ingen gräns för antalet nivåer.

Till exempel innehåller den tekniska profilen **AAD-UserReadUsingAlternativeSecurityId-noerror** filen **AAD-UserReadUsingAlternativeSecurityId**. Den här tekniska profilen anger `RaiseErrorIfClaimsPrincipalDoesNotExist` metadataobjektet till `true` och genererar ett fel om ett socialt konto inte finns i katalogen. **AAD-UserReadUsingAlternativeSecurityId – noerror** åsidosätter detta beteende och inaktiverar det fel meddelandet.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** innehåller den `AAD-Common` tekniska profilen.

```xml
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

Både **AAD-UserReadUsingAlternativeSecurityId-noerror** och  **AAD-UserReadUsingAlternativeSecurityId** anger inte det **protokoll** element som krävs, eftersom det har angetts i **AAD-vanlig** teknisk profil.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
