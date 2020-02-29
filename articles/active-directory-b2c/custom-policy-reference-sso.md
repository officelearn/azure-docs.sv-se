---
title: Hantering av enkel inloggning med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du hanterar SSO-sessioner med anpassade principer i Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b905591266b90e5bba83e7c74b27e7f6b3cab610
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912553"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Hantering av enkel inloggning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Hantering av enkel inloggning (SSO) i Azure Active Directory B2C (Azure AD B2C) gör det möjligt för en administratör att styra interaktionen med en användare när användaren redan har autentiserats. Administratören kan till exempel kontrol lera om valet av identitets leverantörer visas eller om information om lokalt konto måste anges igen. I den här artikeln beskrivs hur du konfigurerar SSO-inställningarna för Azure AD B2C.

Hantering av SSO-sessioner har två delar. Det första handlar med användarens interaktioner direkt med Azure AD B2C och den andra hanterar användarens interaktioner med externa parter som Facebook. Azure AD B2C åsidosätter eller kringgår inte SSO-sessioner som kan innehas av externa parter. I stället för väg genom Azure AD B2C för att komma till den externa parten är "Sparad", så undviker du att be användaren att ange sin sociala eller företags identitets leverantör. Det ultimata SSO-beslutet är kvar med den externa parten.

Hantering av SSO-sessioner använder samma semantik som andra tekniska profiler i anpassade principer. När ett Orchestration-steg körs, frågas den tekniska profilen som är associerad med steget för en `UseTechnicalProfileForSessionManagement` referens. Om det finns en sådan, kontrol leras den refererade SSO-replikeringsprovidern sedan för att se om användaren är en session-deltagare. I så fall, används SSO-replikeringsprovidern för att fylla i sessionen igen. När körningen av ett Dirigerings steg har slutförts används providern för att lagra information i sessionen om en SSO-replikeringsprovider har angetts.

Azure AD B2C har definierat ett antal SSO-användarsessioner som kan användas:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO-hanterings klasser anges med `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />`-elementet i en teknisk profil.

## <a name="input-claims"></a>Inmatade anspråk

`InputClaims`-elementet är tomt eller saknas. 

## <a name="persisted-claims"></a>Beständiga anspråk

Anspråk som måste returneras till programmet eller används av villkor i efterföljande steg, ska lagras i sessionen eller utökas av en läsning från användarens profil i katalogen. Om du använder beständiga anspråk ser du till att det inte går att köra autentiseringen på saknade anspråk. Om du vill lägga till anspråk i sessionen använder du `<PersistedClaims>` elementet i den tekniska profilen. När providern används för att fylla i sessionen läggs de beständiga anspråken till i anspråks säcken. 

## <a name="output-claims"></a>Utgående anspråk

`<OutputClaims>` används för att hämta anspråk från sessionen.

## <a name="session-providers"></a>Sessions-providers

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Den här providern gör inget under namnet. Den här providern kan användas för att förhindra SSO-beteende för en speciell teknisk profil. Följande `SM-Noop` teknisk profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack).  

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligt vis till i en teknisk profil som används för att hantera lokala konton. Följande `SM-AAD` teknisk profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack). 

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

Följande `SM-MFA` teknisk profil ingår i `SocialAndLocalAccountsWithMfa`för [anpassad princip start paket](custom-policy-get-started.md#custom-policy-starter-pack) . Den här tekniska profilen hanterar Multi-Factor Authentication-sessionen. 

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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Den här providern används för att utelämna skärmen "Välj identitetsprovider". Det hänvisas vanligt vis till en teknisk profil som kon figurer ATS för en extern identitetsprovider, till exempel Facebook. Följande `SM-SocialLogin` teknisk profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack).

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
| AlwaysFetchClaimsFromProvider | Nej | Används inte för närvarande, kan ignoreras. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan ett förlitande parts program eller en federerad SAML-identitetsprovider. När du använder SSO-providern för att lagra en SAML-identitetsprovider, måste `IncludeSessionIndex` och `RegisterServiceProviders` anges till `false`. Följande `SM-Saml-idp` teknisk profil används av den [tekniska SAML-profilen](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

När du använder providern för att lagra B2C SAML-sessionen måste `IncludeSessionIndex` och `RegisterServiceProviders` anges till `true`. Utloggningen av SAML-sessionen kräver att `SessionIndex` och `NameID` har slutförts.
 
Följande `SM-Saml-idp` teknisk profil används av den [tekniska profilen för SAML-utfärdaren](connect-with-saml-service-providers.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadata
        
| Attribut | Krävs | Beskrivning|
| --- | --- | --- |
| IncludeSessionIndex | Nej | Anger vilken provider som ska lagras i sessionens index. Möjliga värden: `true` (standard) eller `false`.|
| RegisterServiceProviders | Nej | Anger att leverantören ska registrera alla SAML-tjänstleverantörer som har utfärdat en kontroll. Möjliga värden: `true` (standard) eller `false`.|



