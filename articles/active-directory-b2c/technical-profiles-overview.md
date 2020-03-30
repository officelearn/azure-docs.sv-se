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
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057318"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Om tekniska profiler i Azure Active Directory B2C-anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk profil ger ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter med hjälp av en anpassad princip i Azure Active Directory B2C (Azure AD B2C). Tekniska profiler används för att kommunicera med din Azure AD B2C-klientorganisation, för att skapa en användare eller läsa en användarprofil. En teknisk profil kan självförsäkras för att möjliggöra interaktion med användaren. Samla till exempel in användarens autentiseringsuppgifter för att logga in och sedan återge registreringssidan eller sidan för återställning av lösenord.

## <a name="type-of-technical-profiles"></a>Typ av tekniska profiler

En teknisk profil möjliggör följande typer av scenarier:

- [Application Insights](application-insights-technical-profile.md) - Skicka händelsedata till [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) - Ger stöd för Azure Active Directory B2C-användarhantering.
- [Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md) – ger stöd för att verifiera ett telefonnummer med hjälp av Azure Multi-Factor Authentication (MFA). 
- [Anspråksomvandling](claims-transformation-technical-profile.md) – Anropa anspråkstransformationer för att manipulera anspråksvärden, validera anspråk eller ange standardvärden för en uppsättning utdataanspråk.
- [JWT token issuer](jwt-issuer-technical-profile.md) - Avger en JWT-token som returneras tillbaka till det förlitande part-programmet.
- [OAuth1](oauth1-technical-profile.md) - Federation med någon OAuth 1.0-protokollidentitetsprovider.
- [OAuth2](oauth2-technical-profile.md) - Federation med någon OAuth 2.0-protokollidentitetsprovider.
- [Engångslösenord](one-time-password-technical-profile.md) - Ger stöd för att hantera generering och verifiering av ett engångslösenord.
- [OpenID Connect](openid-connect-technical-profile.md) - Federation med valfri OpenID Connect-protokollidentitetsprovider.
- [Telefonfaktor](phone-factor-technical-profile.md) - Stöd för registrering och verifiering av telefonnummer.
- [RESTful provider](restful-technical-profile.md) - Ring till REST API-tjänster, till exempel validera användarindata, berika användardata eller integrera med line-of-business-program.
- [SAML2](saml-technical-profile.md) - Federation med någon SAML-protokollidentitetsleverantör.
- [SAML token issuer](saml-issuer-technical-profile.md) - Avger en SAML-token som returneras tillbaka till det förlitande part-programmet.
- [Självsäkrad](self-asserted-technical-profile.md) - Interagera med användaren. Samla till exempel in användarens autentiseringsuppgifter för att logga in, återge registreringssidan eller återställning av lösenord.
- [Sessionshantering](custom-policy-reference-sso.md) - Hantera olika typer av sessioner.

## <a name="technical-profile-flow"></a>Tekniskt profilflöde

Alla typer av tekniska profiler har samma koncept. Du skickar indataanspråk, kör anspråksomvandling och kommunicerar med den konfigurerade parten, till exempel en identitetsprovider, REST API eller Azure AD-katalogtjänster. När processen har slutförts returnerar den tekniska profilen utdataanspråken och kan köra omvandling av utdataanspråk. Följande diagram visar hur de omvandlingar och mappningar som refereras i den tekniska profilen bearbetas. Oavsett vilken part den tekniska profilen interagerar med, efter att eventuella anspråksomvandling har utförts, lagras utdataanspråken från den tekniska profilen omedelbart i anspråkspåsen.

![Diagram som illustrerar det tekniska profilflödet](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Hantering av en enda inloggning (SSO)** – Återställer den tekniska profilens sessionstillstånd med hjälp av [SSO-sessionshantering](custom-policy-reference-sso.md).
1. **Omvandling av indataanspråk** - Indataanspråk för varje [indataanspråkstransformation](claimstransformations.md) hämtas från anspråkspåsen.  Utdataanspråken för en omvandling av indataanspråk kan matas in anspråk på en efterföljande omvandling av indataanspråk.
1. **Indataanspråk** - Anspråk hämtas från anspråkspåsen och används för den tekniska profilen. En [självpåstående teknisk profil](self-asserted-technical-profile.md) använder till exempel indataanspråken för att fylla i utdataanspråken som användaren tillhandahåller. En teknisk REST API-profil använder indataanspråken för att skicka indataparametrar till REST API-slutpunkten. Azure Active Directory använder indataanspråk som en unik identifierare för att läsa, uppdatera eller ta bort ett konto.
1. **Teknisk profilkörning** - Den tekniska profilen utbyter anspråken med den konfigurerade parten. Ett exempel:
    - Omdirigera användaren till identitetsprovidern för att slutföra inloggningen. Efter lyckad inloggning returnerar användaren tillbaka och körningen av den tekniska profilen fortsätter.
    - Anropa ett REST API när du skickar parametrar som InputClaims och få tillbaka information som OutputClaims.
    - Skapa eller uppdatera användarkontot.
    - Skickar och verifierar MFA-sms:et.
1. **Tekniska profiler för validering** - En [självförsäkrad teknisk profil](self-asserted-technical-profile.md) kan anropa tekniska profiler för [validering](validation-technical-profile.md). Den tekniska profilen för validering validerar den data som användaren profilerar och returnerar ett felmeddelande eller Ok, med eller utan utdataanspråk. Innan Azure AD B2C till exempel skapar ett nytt konto kontrollerar det om användaren redan finns i katalogtjänsterna. Du kan anropa en teknisk REST API-profil för att lägga till din egen affärslogik.<p>Omfattningen av utdataanspråk för en teknisk profil för validering är begränsad till den tekniska profil som anropar den tekniska profilen för validering. och andra valideringstekniska profiler under samma tekniska profil. Om du vill använda utdataanspråken i nästa orchestration-steg måste du lägga till utdataanspråken i den tekniska profilen som anropar den tekniska profilen för validering.
1. **Utdataanspråk** - Anspråk returneras tillbaka till anspråkspåsen. Du kan använda dessa anspråk i nästa orchestrations steg, eller utdata anspråk omvandlingar.
1. **Omvandling av utdataanspråk** - Indataanspråk för varje omvandling av [utdataanspråk](claimstransformations.md) hämtas från anspråkspåsen. Utdataanspråken för den tekniska profilen från föregående steg kan matas in anspråk på en omvandling av utdataanspråk. Efter körningen sätts utdataanspråken tillbaka i anspråkspåsen. Utdataanspråken för en omvandling av utdataanspråk kan också matas in anspråk på en efterföljande omvandling av utdataanspråk.
1. Hantering av en **enda inloggning (SSO)** – Beständigar den tekniska profilens data till sessionen med hjälp av [SSO-sessionshantering](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Teknisk profil inkludering

En teknisk profil kan innehålla en annan teknisk profil för att ändra inställningar eller lägga till nya funktioner.  Elementet `IncludeTechnicalProfile` är en referens till den grundläggande tekniska profil från vilken en teknisk profil härleds. Det finns ingen gräns för antalet nivåer.

Den tekniska profilen **AAD-UserReadUsingAlternativeSecurityId-NoError** innehåller till exempel den tekniska profilen **AAD-UserReadUsingAlternativeSecurityId**. Den här tekniska `RaiseErrorIfClaimsPrincipalDoesNotExist` profilen `true`anger metadataobjektet till och utlöser ett fel om det inte finns ett socialt konto i katalogen. **AAD-UserReadUsingAlternativeSecurityId-NoError** åsidosätter det här beteendet och inaktiverar det felmeddelandet.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** innehåller `AAD-Common` den tekniska profilen.

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

Både **AAD-UserReadUsingAlternativeSecurityId-NoError** och **AAD-UserReadUsingAlternativeSecurityId** anger inte det **protokollelement** som krävs, eftersom det anges i den tekniska **AAD-Common-profilen.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
