---
title: Transformerings exempel för JSON-anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: JSON-anspråk omvandlings exempel för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ff6f24e30febd57a3a9740ec72a927225b37933
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948919"
---
# <a name="json-claims-transformations"></a>JSON-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder JSON-anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Hämta ett angivet element från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta objektet. |
| InputParameter | claimToExtract | sträng | namnet på det JSON-element som ska extraheras. |
| OutputClaim | extractedClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, det element värde som anges i indataparametern _claimToExtract_ . |

I följande exempel extraherade anspråks omvandlingen `emailAddress`-elementet från JSON-data: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Inmatade anspråk:
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "någon"}
- Indataparameter:
    - **claimToExtract**: EmailAddress
- Utgående anspråk:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Hämta en lista med angivna element från JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta anspråk. |
| InputParameter | errorOnMissingClaims | boolesk | Anger om ett fel ska genereras om en av anspråken saknas. |
| InputParameter | includeEmptyClaims | sträng | Ange om tomma anspråk ska tas med. |
| InputParameter | jsonSourceKeyName | sträng | Element nyckel namn |
| InputParameter | jsonSourceValueName | sträng | Element värde namn |
| OutputClaim | Samling | sträng, int, Boolean och datetime |Lista över anspråk att extrahera. Namnet på anspråket ska vara lika med det som anges i _jsonSourceClaim_ -indatamängden. |

I följande exempel extraherar anspråks omvandlingen följande anspråk: email (sträng), displayName (sträng), membershipNum (int), Active (Boolean) och födelse datum (datetime) från JSON-data.

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

- Inmatade anspråk:
  - **jsonSourceClaim**: [{"Key": "e-post", "value": "someone@example.com"}, {"Key": "DisplayName", "värde": "någon"}, {"Key": "membershipNum", "värde": 6353399}, {"Key": "Active", "värde": true}, {"Key": "födelse datum", "värde": "1980-09-23T00:00:00Z"}]
- Indataparametrar:
    - **errorOnMissingClaims**: falskt
    - **includeEmptyClaims**: falskt
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Utgående anspråk:
  - **e-post**: "someone@example.com"
  - **DisplayName**: "någon"
  - **membershipNum**: 6353399
  - **aktiv**: true
  - **födelse datum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Hämtar ett angivet numeriskt (långt) element från JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta anspråket. |
| InputParameter | claimToExtract | sträng | Namnet på det JSON-element som ska extraheras. |
| OutputClaim | extractedClaim | lång | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, elementets värde som anges i indataparametrarna för _claimToExtract_ . |

I följande exempel extraherar anspråks omvandlingen `id`-elementet från JSON-data.

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

- Inmatade anspråk:
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "någon", "ID": 6353399}
- Indataparametrar
    - **claimToExtract**: ID
- Utgående anspråk:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Hämtar det första elementet från en JSON-datamatris.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta objektet från JSON-matrisen. |
| OutputClaim | extractedClaim | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, det första elementet i JSON-matrisen. |

I följande exempel extraherar anspråks omvandlingen det första elementet (e-postadress) från JSON-matrisen `["someone@example.com", "Someone", 6353399]`.

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

- Inmatade anspråk:
  - **inputJsonClaim**: ["someone@example.com", "någon", 6353399]
- Utgående anspråk:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konverterar XML-data till JSON-format.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | sträng | ClaimTypes som används av anspråks omvandlingen för att konvertera data från XML till JSON-format. |
| OutputClaim | json | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, data i JSON-format. |

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

I följande exempel konverterar anspråks omvandlingen följande XML-data till JSON-format.

#### <a name="example"></a>Exempel
Indatamängds anspråk:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Utgående anspråk:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

