---
title: Exempel på heltals anspråk för identitets upplevelse Framework-schemat för Azure Active Directory B2C | Microsoft Docs
description: Exempel på heltals anspråk för identitets upplevelse Framework-schemat för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064220"
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

I det här exemplet `numericUserId` konverteras anspråket med värde typen Long till ett `UserId` anspråk med ett värde av sträng typ.

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

