---
title: Exempel på boolesk anspråksomvandling för anpassade principer
titleSuffix: Azure AD B2C
description: Booleska anspråkstransformationsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea3b273070702144d5296d07cb8712da044819a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471973"
---
# <a name="boolean-claims-transformations"></a>Boolesk anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder booleska anspråksomvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>OchClaims

Utför en Och drift av två booleska indataKnörningar och ställer in outputClaim med resultatet av operationen.

| Objekt  | TransformationClaimType  | Datatyp  | Anteckningar |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | ingångClaim1 | boolean | Den första ClaimType som ska utvärderas. |
| InputClaim | ingångClaim2  | boolean | Den andra ClaimType att utvärdera. |
|OutputClaim | outputClaim | boolean | De ClaimTypes som kommer att produceras efter att den här anspråksomvandlingen har anropats (sant eller falskt). |

Följande anspråksomvandling visar hur du gör `isEmailNotExist`och `isSocialAccount`två booleska ClaimTypes: och . Utdataanspråket `presentEmailSelfAsserted` `true` är inställt på om `true`värdet för båda indataanspråken är . I ett orchestration-steg kan du använda en förutsättning för att förinställa en självbekände sida, endast om ett e-postmeddelande för ett socialt konto är tomt.

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

- Ingående anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utdataanspråk:
    - **outputClaim**: falskt


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontrollerar att booleska värden för två anspråk är lika och genererar ett undantag om de inte är det.

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Den ClaimType som ska hävdas. |
| InputParameter |värdeTillCompareTo | boolean | Värdet att jämföra (sant eller falskt). |

**AssertBooleanClaimIsEqualToValue-anspråksomvandlingen** utförs alltid från en [teknisk profil](validation-technical-profile.md) för validering som anropas av en [självpått teknisk profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** självsäkra tekniska profilmetadata styr felmeddelandet som den tekniska profilen presenterar för användaren. Felmeddelandena kan [lokaliseras](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual utförande](./media/boolean-transformations/assert-execution.png)

Följande anspråksomvandling visar hur du kontrollerar värdet för `true` en boolesk ClaimType med ett värde. Om värdet för `accountEnabled` ClaimType är falskt genereras ett felmeddelande.

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


Den `login-NonInteractive` tekniska profilen `AssertAccountEnabledIsTrue` för validering anropar anspråksomvandlingen.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självsäkra tekniska profilen anropar den tekniska profilen för **valideringslogisan-NonInteractive.**

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

- Ingående anspråk:
    - **inputClaim**: falskt
    - **valueToCompareTo**: true
- Resultat: Fel uppstod

## <a name="comparebooleanclaimtovalue"></a>JämförBooleanClaimToVärde

Kontrollerar att det booleska `true` värdet `false`för ett anspråk är lika med eller och returnerar resultatet av komprimeringen.

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | Den ClaimType som ska hävdas. |
| InputParameter |värdeTillCompareTo | boolean | Värdet att jämföra (sant eller falskt). |
| OutputClaim | jämförResultat | boolean | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |


Följande anspråksomvandling visar hur du kontrollerar värdet för `true` en boolesk ClaimType med ett värde. Om värdet för `IsAgeOver21Years` ClaimType är `true`lika med `true`returnerar `false`anspråksomvandlingen , annars .

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

- Ingående anspråk:
    - **inputClaim**: falskt
- Indataparametrar:
    - **valueToCompareTo**: true
- Utdataanspråk:
    - **compareResult**: falskt



## <a name="notclaims"></a>NotClaims (Ej 1999)

Utför en Inte-funktion av den booleska indataClaim och ställer in outputClaim med resultatet av operationen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | Påståendet att drivas. |
| OutputClaim | outputClaim | boolean | De ClaimTypes som produceras efter denna ClaimsTransformation har anropats (sant eller falskt). |

Använd den här anspråksomvandlingen för att utföra logisk negation på ett anspråk.

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

- Ingående anspråk:
    - **inputClaim**: falskt
- Utdataanspråk:
    - **outputClaim**: sant

## <a name="orclaims"></a>EllerKlamerna

Beräknar en eller av två boolesk inputClaims och ställer in outputClaim med resultatet av operationen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångClaim1 | boolean | Den första ClaimType som ska utvärderas. |
| InputClaim | ingångClaim2 | boolean | Den andra ClaimType att utvärdera. |
| OutputClaim | outputClaim | boolean | De ClaimTypes som kommer att produceras efter denna ClaimsTransformation har anropats (sant eller falskt). |

Följande anspråksomvandling visar `Or` hur man två booleska ClaimTypes. I orchestration-steget kan du använda en förutsättning för att förinställa en självpåförd sida, om värdet för en av anspråken är `true`.

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

- Ingående anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utdataanspråk:
    - **outputClaim**: sant
