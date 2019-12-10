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
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950501"
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

SSO-hanterings klasser anges med `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />`-elementet i en teknisk profil.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Den här providern gör inget under namnet. Den här providern kan användas för att förhindra SSO-beteende för en speciell teknisk profil.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligt vis till i en teknisk profil som används för att hantera lokala konton. När du använder DefaultSSOSessionProvider för att lagra anspråk i en session måste du se till att alla anspråk som behöver returneras till programmet eller används av för hands villkor i efterföljande steg, lagras i sessionen eller förstärkas av en läsning från användar profilen i katalogen. Detta säkerställer att din autentiserings resa inte fungerar på saknade anspråk.

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

Om du vill lägga till anspråk i sessionen använder du `<PersistedClaims>` elementet i den tekniska profilen. När providern används för att fylla i sessionen läggs de beständiga anspråken till i anspråks säcken. `<OutputClaims>` används för att hämta anspråk från sessionen.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Den här providern används för att utelämna skärmen "Välj identitetsprovider". Det hänvisas vanligt vis till en teknisk profil som kon figurer ATS för en extern identitetsprovider, till exempel Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan appar samt externa SAML Identity providers.

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

Det finns två metadata-objekt i den tekniska profilen:

| Objekt | Default Value | Möjliga värden | Beskrivning
| --- | --- | --- | --- |
| IncludeSessionIndex | sant | True/false | Anger vilken provider som ska lagras i sessionens index. |
| RegisterServiceProviders | sant | True/false | Anger att leverantören ska registrera alla SAML-tjänstleverantörer som har utfärdat en kontroll. |

När du använder providern för att lagra en SAML-identitetsprovider måste objekten ovan vara falska. När du använder providern för att lagra B2C SAML-sessionen ska objekten ovan vara sanna eller utelämnade eftersom standardvärdena är sanna. Utloggningen av SAML-sessionen kräver att `SessionIndex` och `NameID` har slutförts.

