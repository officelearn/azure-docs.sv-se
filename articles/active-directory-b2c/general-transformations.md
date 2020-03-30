---
title: Allmänna exempel på anspråksomvandling för anpassade principer
titleSuffix: Azure AD B2C
description: Allmänna anspråksomvandlingsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188553"
---
# <a name="general-claims-transformations"></a>Allmänna anspråksomvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder allmänna anspråksomvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>KopieraClaim

Kopiera värdet för ett anspråk till ett annat. Båda anspråken måste vara från samma typ.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng, int | Anspråkstypen som ska kopieras. |
| OutputClaim | outputClaim | sträng, int | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

Använd den här anspråksomvandlingen om du vill kopiera ett värde från ett sträng- eller numeriskt anspråk till ett annat anspråk. I följande exempel kopieras värdet för externEmail-anspråk till e-postanspråk.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputClaim**:bob@contoso.com
- Utdataanspråk:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>HarClaimExist

Kontrollerar om **indataClaim** finns eller inte och ställer **in outputClaim** till sant eller falskt i enlighet med detta.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Alla | Påståendet om indata vars existens måste kontrolleras. |
| OutputClaim | outputClaim | boolean | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

Använd den här anspråksomvandlingen för att kontrollera om det finns ett anspråk eller innehåller något värde. Returvärdet är ett booleskt som anger om anspråket finns. I det här exemplet kontrolleras om e-postadressen finns.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **inputClaim**:someone@contoso.com
- Utdataanspråk:
  - **outputClaim**: sant

## <a name="hash"></a>Hash

Hash den medföljande oformaterad text med hjälp av salt och en hemlighet. Hashalgoritmen som används är SHA-256.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Klartext | sträng | Indataanspråket ska krypteras |
| InputClaim | Salt | sträng | Saltparametern. Du kan skapa ett `CreateRandomString` slumpmässigt värde med hjälp av anspråksomvandling. |
| InputParameter | randomizerSecret | sträng | Pekar på en befintlig Azure AD **B2C-principnyckel**. Så här skapar du en ny principnyckel: I din Azure AD B2C-klient under **Hantera**väljer du **Identity Experience Framework**. Välj **Principnycklar** om du vill visa de nycklar som är tillgängliga i din klientorganisation. Välj **Lägg till**. För **Alternativ**väljer du **Manuell**. Ange ett namn (prefixet *B2C_1A_* kan läggas till automatiskt.). I textrutan **Hemlig** anger du vilken hemlighet du vill använda, till exempel 1234567890. För **nyckelanvändning**väljer du **Signatur**. Välj **Skapa**. |
| OutputClaim | hash | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. Anspråket som konfigurerats i `plaintext` indataClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **klartext:**MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Utdataanspråk:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxMWhA5YQNihzV6U=
