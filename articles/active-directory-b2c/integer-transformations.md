---
title: Exempel på heltals anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för heltals anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7295e03f0a0f94b3450b99acc4d10d6ff86c92e7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948916"
---
# <a name="integer-claims-transformations"></a>Transformeringar av heltals anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder heltals anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konverterar en lång data typ till en sträng data typ.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | lång | ClaimType som ska konverteras till en sträng. |
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

