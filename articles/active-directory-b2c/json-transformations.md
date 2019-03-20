---
title: JSON-anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: JSON-anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88d447f86dd54fc9479a6d7d2006b9a8639ad09e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089241"
---
# <a name="json-claims-transformations"></a>JSON-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda JSON-anspråksomvandlingar av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Hämta det angivna elementet från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråkstransformering för att hämta objektet. |
| InputParameter | claimToExtract | sträng | namnet på JSON-element som ska extraheras. |
| OutputClaim | extractedClaim | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats kan elementvärdet som anges i den _claimToExtract_ indataparameter. |

I följande exempel visas anspråkstransformering extraherade den `emailAddress` element från JSON-data: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": ”Någon”}
- Indataparametern:
    - **claimToExtract**: emailAddress
- Utgående anspråk: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Hämta en lista över angivna element från Json-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | sträng | ClaimTypes som används av anspråkstransformering för att hämta anspråk. |
| InputParameter | errorOnMissingClaims | boolesk | Anger om ett fel genereras om ett av anspråken saknas. |
| InputParameter | includeEmptyClaims | sträng | Ange om du vill inkludera tom anspråk. |
| InputParameter | jsonSourceKeyName | sträng | Elementet nyckelnamn |
| InputParameter | jsonSourceValueName | sträng | Värdet elementnamn |
| OutputClaim | Samling | sträng, int, boolean och datetime |Lista över anspråk för att extrahera. Anspråkets namn bör vara samma som den som angetts i _jsonSourceClaim_ inkommande anspråk. |

I följande exempel, extraherar anspråkstransformering följande anspråk: e-post (sträng), displayName (sträng), membershipNum (int), aktiv (boolesk) och födelsedatum (datetime) från JSON-data.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Inkommande anspråk:
  - **jsonSourceClaim**: [{”nyckeln”: ”e”, ”value” ”:someone@example.com”}, {”nyckeln”: ”displayName”, ”value”: ”någon”}, {”nyckeln”: ”membershipNum”, ”value”: 6353399}, {”nyckeln”: ”aktiv”, ”value”: true}, {”nyckeln”: ”födelsedatum”, ”value” ”: 1980-09-23T00:0 0:00Z ”}]
- Indataparametrar:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Utgående anspråk:
  - **e-post**”:someone@example.com”
  - **displayName**: ”Någon”
  - **membershipNum**: 6353399
  - **aktiva**: true
  - **födelsedatumet**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Hämtar det angivna elementet för numeriska (lång tid) från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråkstransformering för att hämta anspråket. |
| InputParameter | claimToExtract | sträng | Namnet på JSON-element för att extrahera. |
| OutputClaim | extractedClaim | lång | ClaimType som skapas när den här ClaimsTransformation har anropats elementets värde som angetts i den _claimToExtract_ indataparametrar. |

I följande exempel visas anspråkstransformering extraherar den `id` element från JSON-data.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": ”Någon”, ”id”: 6353399}
- Indataparametrar
    - **claimToExtract**: id
- Utgående anspråk: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Hämtar det första elementet från en JSON-matris för data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | sträng | ClaimTypes som används av anspråk omvandling för att hämta objektet från JSON-matris. |
| OutputClaim | extractedClaim | sträng | ClaimType som skapas när den här ClaimsTransformation har anropats, det första elementet i JSON-matris. |

I följande exempel visas anspråkstransformering extraherar det första elementet (e-postadress) från JSON-matris `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Utgående anspråk: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konverterar XML-data till JSON-format.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | sträng | ClaimTypes som används av anspråkstransformering för att konvertera data från XML till JSON-format. |
| OutputClaim | JSON | sträng | ClaimType som skapas när den här ClaimsTransformation har anropats kommer data i JSON-format. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

I följande exempel konverterar anspråkstransformering följande XML-data till JSON-format.

#### <a name="example"></a>Exempel
Inkommande anspråk:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Utdata-anspråket:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

