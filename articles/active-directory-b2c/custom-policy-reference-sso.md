---
title: Hantering av enstaka inloggningssessioner med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du hanterar SSO-sessioner med hjälp av anpassade principer i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246037"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Hantering av ensession i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

SSO-sessionshantering (Single sign-on) i Azure Active Directory B2C (Azure AD B2C) gör det möjligt för en administratör att kontrollera interaktionen med en användare när användaren redan har autentiserats. Administratören kan till exempel styra om valet av identitetsleverantörer visas eller om lokal kontoinformation måste anges igen. I den här artikeln beskrivs hur du konfigurerar SSO-inställningarna för Azure AD B2C.

SSO-sessionshanteringen har två delar. Den första handlar om användarens interaktioner direkt med Azure AD B2C och den andra handlar om användarens interaktioner med externa parter som Facebook. Azure AD B2C åsidosätter eller kringgår inte SSO-sessioner som kan innehas av externa parter. Snarare vägen genom Azure AD B2C för att komma till den externa parten är "ihågkommen", undvika behovet av att återbefordran användaren att välja sin sociala eller företagsidentitet leverantör. Det slutliga SSO-beslutet ligger kvar hos den externa parten.

SSO-sessionshantering använder samma semantik som alla andra tekniska profiler i anpassade principer. När ett orkestreringssteg körs efterfrågas den tekniska profilen `UseTechnicalProfileForSessionManagement` som är associerad med steget efter en referens. Om det finns en sådan kontrolleras den refererade SSO-sessionsprovidern för att se om användaren är en sessionsdeltagare. I så fall används SSO-sessionsprovidern för att återbefolka sessionen. På samma sätt, när körningen av ett orchestration-steg är slutfört, används providern för att lagra information i sessionen om en SSO-sessionsprovider har angetts.

Azure AD B2C har definierat ett antal SSO-sessionsleverantörer som kan användas:

* NoopSSOSessionProvider
* StandardSSOSessionProvider
* ExternLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO-hanteringsklasser anges `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` med hjälp av elementet i en teknisk profil.

## <a name="input-claims"></a>Ingående anspråk

Elementet `InputClaims` är tomt eller frånvarande.

## <a name="persisted-claims"></a>Kvarstående fordringar

Anspråk som måste returneras till programmet eller användas av förutsättningar i efterföljande steg, bör lagras i sessionen eller utökas med en läsning från användarens profil i katalogen. Genom att använda beständiga anspråk säkerställs att dina autentiseringsresor inte misslyckas med saknade anspråk. Om du vill lägga till `<PersistedClaims>` anspråk i sessionen använder du elementet i den tekniska profilen. När leverantören används för att återbefolka sessionen läggs de kvarstående anspråken till i anspråkspåsen.

## <a name="output-claims"></a>Utgående fordringar

Används `<OutputClaims>` för att hämta anspråk från sessionen.

## <a name="session-providers"></a>Sessionsleverantörer

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Som namnet säger, gör denna leverantör ingenting. Den här providern kan användas för att undertrycka SSO-beteende för en viss teknisk profil. Följande `SM-Noop` tekniska profil ingår i [startpaketet](custom-policy-get-started.md#custom-policy-starter-pack)för anpassad princip .

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>StandardSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligtvis i en teknisk profil som används för att hantera lokala konton. Följande `SM-AAD` tekniska profil ingår i [startpaketet](custom-policy-get-started.md#custom-policy-starter-pack)för anpassad princip .

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Följande `SM-MFA` tekniska profil ingår i [startpaketet](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`för anpassad princip . Den här tekniska profilen hanterar multifaktorautentiseringssessionen.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternLoginSSOSessionProvider

Den här providern används för att undertrycka skärmen "välj identitetsprovider". Det refereras vanligtvis i en teknisk profil som konfigurerats för en extern identitetsleverantör, till exempel Facebook. Följande `SM-SocialLogin` tekniska profil ingår i [startpaketet](custom-policy-get-started.md#custom-policy-starter-pack)för anpassad princip .

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Inga | Används för närvarande, kan ignoreras. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan ett förlitande partsprogram eller en federerad SAML-identitetsprovider. När SSO-providern använder för att lagra en `RegisterServiceProviders` SAML-identitetsprovider session, måste vara inställd på `false`. Följande `SM-Saml-idp` tekniska profil används av [DEN TEKNISKA PROFILEN FÖR SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

När du använder providern för att lagra B2C SAML-sessionen `RegisterServiceProviders` måste måste den inställda på `true`. SAML session utloggning kräver `SessionIndex` och `NameID` att slutföra.

Följande `SM-Saml-idp` tekniska profil används av [SAML-utfärdarens tekniska profil](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning|
| --- | --- | --- |
| InkluderaSessionIndex | Inga | Används för närvarande, kan ignoreras.|
| RegisterServiceProviders | Inga | Anger att leverantören ska registrera alla SAML-tjänsteleverantörer som har utfärdats ett påstående. Möjliga värden: `true` (standard) eller `false`.|



