---
title: Exempel på heltals anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för heltals anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187686"
---
# <a name="integer-claims-transformations"></a>Transformeringar av heltals anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder heltals anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konverterar en lång data typ till en sträng data typ.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | ClaimType som ska konverteras till en sträng. |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

I det här exemplet konverteras `numericUserId`-anspråk med värde typen Long till ett `UserId`-anspråk med en värdetyp.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: 12334 (långt)
- Utgående anspråk:
    - **outputClaim**: "12334" (sträng)

