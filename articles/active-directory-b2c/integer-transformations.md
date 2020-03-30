---
title: Exempel på omvandling av heltalsanspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Heltalsanspråksomvandlingsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187686"
---
# <a name="integer-claims-transformations"></a>Heltalsanspråksomvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder heltalsanspråksomvandlingarna i Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konverterar en lång datatyp till en strängdatatyp.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Den ClaimType som ska konverteras till en sträng. |
| OutputClaim | outputClaim | sträng | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

I det här `numericUserId` exemplet konverteras anspråket med en `UserId` värdetyp av lång till ett anspråk med en värdetyp av sträng.

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

- Ingående anspråk:
    - **inputClaim**: 12334 (lång)
- Utdataanspråk:
    - **outputClaim**: "12334" (sträng)

