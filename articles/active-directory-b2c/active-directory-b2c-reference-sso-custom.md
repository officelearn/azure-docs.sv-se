---
title: SSO sessionshantering anpassade principer - Azure AD B2C | Microsoft Docs
description: "Lär dig hur du hanterar SSO-sessioner som använder anpassade principer i Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 809f6000-2e52-43e4-995d-089d85747e1f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: parja
ms.openlocfilehash: 676b277ae3fbf4554838eee70c5d3e2d8e12c33d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: Enkel inloggning (SSO) sessionshantering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C kan administratören att styra hur Azure AD B2C samverkar med en användare när användaren redan har autentiserats. Detta görs via sessionshantering för enkel inloggning. Administratören kan styra om valet av identitetsleverantörer ska visas eller om lokala kontoinformation måste anges igen. Den här artikeln beskriver hur du konfigurerar inställningarna för enkel inloggning för Azure AD B2C.

## <a name="overview"></a>Översikt

SSO sessionshantering består av två delar. Först behandlar användarens interaktion direkt med Azure AD B2C och andra avtal med användarens interaktion med externa parter som Facebook. Azure AD B2C inte åsidosätta eller kringgå SSO-sessioner som kan innehas av externa parter. I stället sparas vidarebefordra via Azure AD B2C för att komma till extern part ””, så behöver du reprompt användaren att välja sina social eller enterprise identitetsleverantör. Ultimate SSO beslutet förblir med extern part.

## <a name="how-does-it-work"></a>Hur fungerar det?

SSO sessionshantering använder samma semantik som andra tekniska profil i anpassade principer. När en orchestration-steg utförs tekniska profilen som är associerade med steget efterfrågas en `UseTechnicalProfileForSessionManagement` referens. Om det finns ett kontrolleras sedan refererade SSO session providern om användaren är en session-deltagare. Om så SSO session providern används för igen sessionen. När körningen av ett orchestration-steg har slutförts, används providern på samma sätt att lagra information i sessionen om en session SSO-provider har angetts.

Azure AD B2C har definierat ett antal SSO session providers som kan användas:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO-klasser för lagringshantering anges med den `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` element i en teknisk profil.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Som namnet avgör ingenting den här providern. Den här providern kan användas för att undertrycka SSO beteende för en viss teknisk profil.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligtvis i en teknisk profil som används för att hantera lokala konton. 

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

Om du vill lägga till anspråk i sessionen använder den `<PersistedClaims>` elementet av den tekniska profilen. När providern används för att igen sessionen, den beständiga anspråk läggs till i uppsättningen anspråk. `<OutputClaims>`används för att hämta anspråk från sessionen.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Den här providern används för att undertrycka skärmen ”Välj identitetsleverantör”. Vanligtvis refererar till i en teknisk profil som är konfigurerad för en extern identitetsleverantör, till exempel Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan appar samt externa SAML identitetsleverantörer.

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

Det finns två metadataobjekt i tekniska profilen:

| Objekt | Standardvärde | Möjliga värden | Beskrivning
| --- | --- | --- | --- |
| IncludeSessionIndex | true | SANT/FALSKT | Anger indexet session ska sparas till providern. |
| RegisterServiceProviders | true | SANT/FALSKT | Anger att providern ska registrera SAML leverantörer som har utfärdats ett intyg. |

När du använder providern för att lagra en SAML identitet provider-session, måste objekten ovan båda ha värdet false. När du använder providern för att lagra B2C SAML-session, vara artiklar ovan SANT eller utelämnat som standardvärdena är true.

>[!NOTE]
> Logga ut SAML-session kräver den `SessionIndex` och `NameID` att slutföra.

## <a name="next-steps"></a>Nästa steg

Vi håller av feedback och förslag! Om du har problem med det här avsnittet Publicera på Stack Overflow med taggen ['azure-ad b2c-](https://stackoverflow.com/questions/tagged/azure-ad-b2c). För funktionsbegäranden, rösta på dem i vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

