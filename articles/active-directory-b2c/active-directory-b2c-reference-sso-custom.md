---
title: Sessionshantering för enkel inloggning med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att hantera SSO-sessioner med anpassade principer i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 351b48f2e2766b4974a5a41b5e95acfbd63dbfc9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443230"
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: Enkel inloggning (SSO) sessionshantering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C kan en administratör att styra hur Azure AD B2C interagerar med en användare när användaren redan har autentiserats. Detta görs via hantering av SSO-session. Administratören kan till exempel styra om valet av identitetsleverantörer ska visas eller om lokala kontoinformation måste anges igen. Den här artikeln beskriver hur du konfigurerar inställningar för enkel inloggning för Azure AD B2C.

## <a name="overview"></a>Översikt

SSO sessionshantering består av två delar. Först innehåller användarens interaktioner direkt med Azure AD B2C och andra avtal med användarens interaktioner med externa parter, till exempel Facebook. Azure AD B2C inte åsidosätta eller kringgå SSO-sessioner som lagras av externa parter. I stället sparas vidarebefordra via Azure AD B2C för att komma till den externa parten ””, så behöver du reprompt att användaren väljer sina identitetsprovider för socialt eller företag. Ultimate SSO beslutet förblir med extern part.

## <a name="how-does-it-work"></a>Hur fungerar det?

Hantering av SSO-session använder samma semantik som andra tekniska profilen i anpassade principer. När en orchestration-steg utförs den tekniska profilen som är associerade med steg krävs för en `UseTechnicalProfileForSessionManagement` referens. Om en sådan finns, kontrolleras sedan den refererade SSO-session providern och se om användaren är en session-deltagare. Om så provider för SSO-session används för att åter fylla sessionen. När körningen av en orchestration-steg har slutförts, används providern på samma sätt att lagra information i sessionen om du har angett en provider för SSO-session.

Azure AD B2C har definierat ett antal providrar för SSO-session som kan användas:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Hanteringsklasser för enkel inloggning har angetts med hjälp av den `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` element för den tekniska profilen.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Som namnet avgör sker ingenting i den här providern. Den här providern kan användas för att förbjuda SSO beteende för en specifik tekniska profil.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligtvis i en tekniska profil som används för att hantera lokala konton. 

> [!NOTE]
> När du använder DefaultSSOSessionProvider för att lagra anspråk i en session kan behöva du se till att anspråk som returneras till programmet eller används av villkoren i efterföljande steg ska lagras i en session eller förstärkas med en läsning från användarens profil i katalogen. Det säkerställer att resan autentisering inte kan utföras på saknas anspråk.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Lägg till anspråk i sessionen genom att använda den `<PersistedClaims>` elementet i den tekniska profilen. När providern används för att åter fylla sessionen, den beständiga läggs anspråken till uppsättningen anspråk. `<OutputClaims>` används för att hämta anspråk från sessionen.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Den här providern används för att ignorera skärmen ”välja identitetsprovider”. Det är vanligtvis refereras till i en tekniska profil som konfigurerats för en extern identitetsleverantör, till exempel Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan appar samt externa SAML-identitetsleverantörer.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Det finns två metadataobjekt i den tekniska profilen:

| Objekt | Standardvärde | Möjliga värden | Beskrivning
| --- | --- | --- | --- |
| IncludeSessionIndex | true | SANT/FALSKT | Anger att providern session indexet ska lagras. |
| RegisterServiceProviders | true | SANT/FALSKT | Anger att providern ska registrera alla SAML-leverantörer som har utfärdats ett intyg. |

När du använder providern för att lagra en SAML identitet provider-session, ska objekten ovan båda vara false. När du använder providern för att lagra B2C SAML-session, vara objekten ovan SANT eller utelämnat som standardvärdena är sant.

>[!NOTE]
> SAML-session utloggning kräver den `SessionIndex` och `NameID` att slutföra.

## <a name="next-steps"></a>Nästa steg

Vi gärna feedback och förslag! Om du har problem med det här avsnittet Publicera på Stack Overflow med taggen [”azure-ad-b2c”](https://stackoverflow.com/questions/tagged/azure-ad-b2c). För funktionsförfrågningar om rösta på dem i våra [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

