---
title: Konfigurera sessionens beteende med anpassade principer – Azure Active Directory B2C | Microsoft Docs
description: Konfigurera sessionens beteende med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961110"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera sessionens beteende med anpassade principer i Azure Active Directory B2C

Hantering av [enkel inloggning (SSO)](session-overview.md) i Azure Active Directory B2C (Azure AD B2C) gör det möjligt för en administratör att styra interaktionen med en användare när användaren redan har autentiserats. Administratören kan till exempel kontrol lera om valet av identitets leverantörer visas eller om konto information måste anges igen. I den här artikeln beskrivs hur du konfigurerar SSO-inställningarna för Azure AD B2C.

## <a name="session-behavior-properties"></a>Egenskaper för sessionens beteende

Du kan använda följande egenskaper för att hantera WebApplication-sessioner:

- **Web App session livs längd (minuter)** – livs längden för Azure AD B2C's-sessionens cookie som lagras i användarens webbläsare vid lyckad autentisering.
  - Standard = 86400 sekunder (1440 minuter).
  - Minimum (inklusive) = 900 sekunder (15 minuter).
  - Max (inklusive) = 86400 sekunder (1440 minuter).
- **Timeout för webbapp** – [sessionens utgångs typ](session-overview.md#session-expiry-type), *rullande*eller *absolut*. 
- **Konfiguration av enkel inloggning** – [sessionens omfång](session-overview.md#session-scope) för beteendet enkel inloggning (SSO) i flera appar och användar flöden i din Azure AD B2C klient. 

## <a name="configure-the-properties"></a>Konfigurera egenskaperna

Om du vill ändra sessionens beteende och SSO-konfigurationer lägger du till ett **UserJourneyBehaviors** -element i [RelyingParty](relyingparty.md) -elementet.  **UserJourneyBehaviors** -elementet måste omedelbart följa **DefaultUserJourney**. Ditt **UserJourneyBehavors** -element bör se ut som i det här exemplet:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>Konfigurera utloggnings beteende

### <a name="secure-your-logout-redirect"></a>Skydda din utloggnings omdirigering

Efter utloggning omdirigeras användaren till den URI som anges i `post_logout_redirect_uri` parametern, oavsett vilka svars-URL: er som har angetts för programmet. Men om ett giltigt `id_token_hint` värde skickas och **Kräv ID-token i utloggnings begär Anden** aktive ras, verifierar Azure AD B2C att värdet för `post_logout_redirect_uri` matchar ett av programmets konfigurerade omdirigerings-URI: er innan omdirigeringen utförs. Om ingen matchande svars-URL har kon figurer ATS för programmet visas ett fel meddelande och användaren omdirigeras inte. 

Om du vill kräva en ID-token i utloggnings begär Anden lägger du till ett **UserJourneyBehaviors** -element i [RelyingParty](relyingparty.md) -elementet. Ange sedan **EnforceIdTokenHintOnLogout** för **SingleSignOn** -elementet till `true` . Ditt **UserJourneyBehaviors** -element bör se ut som i det här exemplet:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

Så här konfigurerar du webb adressen för din program utloggning:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**och välj sedan ditt program.
1. Välj **Autentisering**.
1. I text rutan **utloggnings-URL** skriver du in återutloggning omdirigerings-URI och väljer sedan **Spara**.

### <a name="single-sign-out"></a>Enkel utloggning

#### <a name="configure-the-applications"></a>Konfigurera program

När du omdirigerar användaren till Azure AD B2C utloggnings slut punkt (för både OAuth2-och SAML-protokoll) rensar Azure AD B2C användarens session från webbläsaren.  Om du vill tillåta [enkel utloggning](session-overview.md#single-sign-out)ställer du in `LogoutUrl` programmet från Azure Portal:

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Välj din Azure AD B2C katalog genom att klicka på ditt konto i det övre högra hörnet på sidan.
1. I den vänstra menyn väljer du **Azure AD B2C**, väljer **Appregistreringar**och väljer sedan ditt program.
1. Välj **Autentisering**.
1. I text rutan **utloggnings-URL** skriver du in återutloggning omdirigerings-URI och väljer sedan **Spara**.

#### <a name="configure-the-token-issuer"></a>Konfigurera token Issuer 

För att stödja enkel utloggning måste token Issuer tekniska profiler för både JWT och SAML ange:

- Protokoll namnet, t. ex. `<Protocol Name="OpenIdConnect" />`
- Referens till teknisk profil för sessionen, till exempel `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

I följande exempel illustrerar JWT-och SAML-token-utfärdare med enkel utloggning:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD B2C-sessionen](session-overview.md).
