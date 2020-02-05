---
title: Transformerings exempel för booleska anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för booleska anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f0d6d74271cc4ff0be4a653b389cc70ad5c56ef9
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983086"
---
# <a name="boolean-claims-transformations"></a>Omvandlingar med booleska anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder booleska anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Utför en och-åtgärd av två booleska inputClaims och anger outputClaim med resultatet av åtgärden.

| Objekt  | TransformationClaimType  | Datatyp  | Anteckningar |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | Den första ClaimType som ska utvärderas. |
| InputClaim | inputClaim2  | boolean | Den andra ClaimType som ska utvärderas. |
|OutputClaim | outputClaim | boolean | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats (sant eller falskt). |

Följande anspråks omvandling visar hur och två booleska ClaimTypes: `isEmailNotExist`och `isSocialAccount`. Den utgående anspråks `presentEmailSelfAsserted` anges till `true` om värdet för båda indata-anspråk är `true`. I ett Orchestration-steg kan du använda ett villkor för att förinställa en självkontrollerad sida, bara om ett e-postmeddelande om sociala konton är tomt.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utgående anspråk:
    - **outputClaim**: falskt


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontrollerar att booleska värden för två anspråk är lika och genererar ett undantag om de inte är det.

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Den ClaimType som ska försäkras. |
| InputParameter |valueToCompareTo | boolean | Värdet som ska jämföras (sant eller falskt). |

Omvandlingen av **AssertBooleanClaimIsEqualToValue** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** -metadata för självkontrollerad teknisk profil styr det fel meddelande som den tekniska profilen presenterar för användaren.

![AssertStringClaimsAreEqual-körning](./media/boolean-transformations/assert-execution.png)

Följande anspråks omvandling visar hur du kontrollerar värdet för en boolesk ClaimType med ett `true`-värde. Om värdet för `accountEnabled` ClaimType är falskt genereras ett fel meddelande.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Den tekniska profilen för `login-NonInteractive` verifiering anropar transformeringen för `AssertAccountEnabledIsTrue`-anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självkontrollerade tekniska profilen anropar verifierings **inloggningen-inaktiv** teknisk profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: falskt
    - **valueToCompareTo**: sant
- Resultat: fel utlöst

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Kontrollerar att booleskt värde för ett anspråk är lika med `true` eller `false`och returnerar resultatet av komprimeringen. 

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Den ClaimType som ska försäkras. |
| InputParameter |valueToCompareTo | boolean | Värdet som ska jämföras (sant eller falskt). |
| OutputClaim | inputClaim | boolean | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |


Följande anspråks omvandling visar hur du kontrollerar värdet för en boolesk ClaimType med ett `true`-värde. Om värdet för `IsAgeOver21Years` ClaimType är lika med `true`, returnerar funktionen Claims `true`, annars `false`.

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: falskt
- Indataparametrar:
    - **valueToCompareTo**: sant
- Utgående anspråk:
    - **compareResult**: falskt 



## <a name="notclaims"></a>NotClaims

Utför en not-åtgärd för den booleska inputClaim och ställer in outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | Det anspråk som ska användas. |
| OutputClaim | outputClaim | boolean | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats (sant eller falskt). |

Använd den här anspråks omvandlingen för att utföra logisk negation på ett anspråk.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: falskt
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="orclaims"></a>OrClaims

Beräknar en eller två booleska inputClaims och anger outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | Den första ClaimType som ska utvärderas. |
| InputClaim | inputClaim2 | boolean | Den andra ClaimType som ska utvärderas. |
| OutputClaim | outputClaim | boolean | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats (sant eller falskt). |

Följande anspråks omvandling visar hur du `Or` två booleska ClaimTypes. I Orchestration-steget kan du använda ett villkor för att förinställa en självkontrollerad sida om värdet för ett av anspråken är `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utgående anspråk:
    - **outputClaim**: sant
