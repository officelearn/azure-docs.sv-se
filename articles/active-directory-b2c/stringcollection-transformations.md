---
title: Exempel på omformning av StringCollection-anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: StringCollection-anspråkstransformeringsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729708"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection hävdar omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder anspråksomvandlingarna för strängsamling av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringKollyra

Lägger till ett stränganspråk i ett nytt unikt värdesträngInsamlingsanspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | objekt | sträng | Den ClaimType som ska läggas till i utdataanspråket. |
| InputClaim | samling | strängInsamling | [Valfritt] Om det anges kopierar anspråksomvandlingen objekten från den här samlingen och objektet läggs till i slutet av anspråk på utflödesinsamling. |
| OutputClaim | samling | strängInsamling | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats, med det värde som anges i indataanspråket. |

Använd den här anspråksomvandlingen om du vill lägga till en sträng i en ny eller befintlig stringCollection. Det används ofta i en **AAD-UserWriteUsingAlternativeSecurityId** teknisk profil. Innan ett nytt socialt konto skapas läser **CreateOtherMailsFromEmail-anspråksomvandlingen** av ClaimType och tillför värdet till **otherMails** ClaimType.

Följande anspråkstransformation lägger till **e-post** ClaimType till **otherMails** ClaimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **samling**:someone@outlook.com[" "]
  - **Punkt**:admin@contoso.com" "
- Utdataanspråk:
  - **samling**:someone@outlook.com["admin@contoso.com", " "]

## <a name="addparametertostringcollection"></a>Lägg tillParameterToStringKollektion

Lägger till en strängparameter i ett nytt unikt värden stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | strängInsamling | [Valfritt] Om det anges kopierar anspråksomvandlingen objekten från den här samlingen och objektet läggs till i slutet av anspråk på utflödesinsamling. |
| InputParameter | objekt | sträng | Det värde som ska läggas till i utdataanspråket. |
| OutputClaim | samling | strängInsamling | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats, med det värde som anges i indataparametern. |

Använd den här anspråksomvandlingen om du vill lägga till ett strängvärde i en ny eller befintlig stringCollection. I följande exempel läggs enadmin@contoso.comkonstant e-postadress ( ) till **andra E-postanspråk.**

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **samling**:someone@outlook.com[" "]
- Indataparametrar
  - **Punkt**:admin@contoso.com" "
- Utdataanspråk:
  - **samling**:someone@outlook.com["admin@contoso.com", " "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hämtar det första objektet från den medföljande strängsamlingen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | strängInsamling | De ClaimTypes som används av anspråksomvandlingen för att hämta objektet. |
| OutputClaim | extraheradeItm | sträng | De ClaimTypes som produceras efter att den här ClaimsTransformation har anropats. Det första objektet i samlingen. |

I följande exempel **läses det andra e-postanspråket** och returnera det första objektet till **e-postanspråket.**

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **samling**:someone@outlook.com["someone@contoso.com", " "]
- Utdataanspråk:
  - **extraheradItm**:someone@outlook.com" "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Kontrollerar om en stringcollection-anspråkstyp innehåller ett element

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | strängInsamling | Anspråkstypen som ska genomsökas. |
|InputParameter|objekt|sträng|Värdet att söka.|
|InputParameter|ignoreCase (ignoreraFall)|sträng|Anger om den här jämförelsen ska ignorera fallet med de strängar som jämförs.|
| OutputClaim | outputClaim | boolean | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. En boolesk indikator om samlingen innehåller en sådan sträng |

Följande exempel kontrollerar om strängenKollyrningsanspråkstypen `roles` innehåller värdet för **admin**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Ingående anspråk:
    - **inputClaim**: ["läsare", "författare", "admin"]
- Indataparametrar:
    - **objekt:**"Admin"
    - **ignoreCase**: "sant"
- Utdataanspråk:
    - **outputClaim**: "sant"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Kontrollerar om en StringCollection-anspråkstyp innehåller ett anspråksvärde.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | strängInsamling | Anspråkstypen som ska genomsökas. |
| InputClaim | objekt|sträng| Anspråkstypen som innehåller värdet att söka i.|
|InputParameter|ignoreCase (ignoreraFall)|sträng|Anger om den här jämförelsen ska ignorera fallet med de strängar som jämförs.|
| OutputClaim | outputClaim | boolean | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. En boolesk indikator om samlingen innehåller en sådan sträng |

I det här `roles` exemplet kontrolleras om anspråkstypen stringCollection innehåller värdet för anspråkstypen. `role`

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Ingående anspråk:
    - **samling**: ["läsare", "författare", "admin"]
    - **objekt:**"Admin"
- Indataparametrar:
    - **ignoreCase**: "sant"
- Utdataanspråk:
    - **outputClaim**: "sant"
