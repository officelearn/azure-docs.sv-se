---
title: Definiera ett anspråk omvandling tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera ett anspråk omvandling tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0a2904bec34978a33d25534c9e9b32552191ad88
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705319"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera ett anspråk omvandling tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anspråkstransformering tekniska profilen kan du anropa utdataanspråk omvandlingar för att manipulera anspråk värden, pröva kravets eller ange standardvärden för en uppsättning utgående anspråk.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `Proprietary`. Den **hanteraren** attributet måste innehålla det fullständigt kvalificerade namnet på protokoll hanteraren sammansättningen som används av Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

I följande exempel visas en anspråk omvandling tekniska profil:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** element är obligatoriskt. Du måste ange minst en utgående anspråk som returneras av den tekniska profilen. I följande exempel visas hur du ställer in standardvärden i utdataanspråk:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Utgående anspråk omvandlingar

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra anspråk eller generera nya. Följande tekniska profil-anrop i **RemoveAlternativeSecurityIdByIdentityProvider** omvandling av anspråk. Detta anspråk omvandling tar bort en social utläsa från uppsättningen **AlternativeSecurityIds**. Utgående anspråk av den här tekniska profilen **identityProvider2**, som har angetts till `facebook.com`, och **AlternativeSecurityIds**, som innehåller listan över sociala identiteter som är associerade med den här användaren när facebook.com identitet har tagits bort.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Anspråk omvandling tekniska profilen kan du köra en anspråkstransformering från alla användarresa orchestration-steg. I följande exempel orkestreringssteget anropar någon av tekniska profiler länk skulle tas bort som **OAUTH-Facebook-länk skulle tas bort**. Den här tekniska profilen anropar anspråken omvandling tekniska profilen **RemoveAlternativeSecurityIdByIdentityProvider**, vilket genererar en ny **AlternativeSecurityIds2** anspråk som innehåller lista över användare sociala identiteter vid borttagning av Facebook-identitet från samlingarna.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Använd en teknisk verifieringsprofil

Ett anspråk omvandling tekniska profilen kan användas för att validera informationen. I följande exempel visas den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md) med namnet **LocalAccountSignUpWithLogonEmail** uppmanar användaren att ange e-postmeddelandet två gånger och sedan anropar den [Teknisk verifiering profilen](validation-technical-profile.md) med namnet **verifiera e-postadress** att verifiera e-postmeddelanden. Den **verifiera e-postadress** tekniska profilen anropar anspråkstransformering **AssertEmailAreEqual** att jämföra två anspråk **e-post** och **emailRepeat** , och utlöser ett undantag om de inte är lika med enligt angivna jämförelsen.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Anspråk omvandling tekniska profilen anrop den **AssertEmailAreEqual** omvandling som kontrollerar att e-postmeddelanden som användaren är samma av anspråk.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

En lokal verifieringsvillkor tekniska profilen kalla den tekniska profilen verifiering och visa ett felmeddelande som anges i den **UserMessageIfClaimsTransformationStringsAreNotEqual** metadata.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
