---
title: JSON anspråk omvandlingsexempel för anpassade principer
titleSuffix: Azure AD B2C
description: JSON hävdar omvandlingsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756753"
---
# <a name="json-claims-transformations"></a>JSON hävdar omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder JSON-anspråksomvandlingarna av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenereraJson

Använd antingen anspråksvärden eller konstanter för att generera en JSON-sträng. Bansträngen efter punkt notation används för att ange var data ska infogas i en JSON-sträng. När du har delat upp med punkter tolkas alla heltal som index för en JSON-matris och icke-heltal tolkas som index för ett JSON-objekt.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Sträng efter punkt notation | sträng | JsonPath för JSON där anspråksvärdet infogas i. |
| InputParameter | Sträng efter punkt notation | sträng | JsonPath för JSON där det konstanta strängvärdet infogas i. |
| OutputClaim | outputClaim | sträng | Den genererade JSON-strängen. |

Följande exempel genererar en JSON-sträng baserat på anspråksvärdet för "e-post" och "otp" samt konstanta strängar.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

Följande anspråksomvandling utdataar ett JSON-stränganspråk som kommer att vara brödtexten i begäran som skickas till SendGrid (en tredjeparts-e-postleverantör). JSON-objektets struktur definieras av ID:erna i punktatering av InputParameters och TransformationClaimTypes för InputClaims. Tal i punkt notationen antyder matriser. Värdena kommer från InputClaims värden och InputParameters "Value"-egenskaper.

- Ingående fordringar:
  - **e-post**, omvandling anspråk typ **personaliseringar.0.to.0.email**: "someone@example.com"
  - **otp**, omvandling anspråk typ **personalizations.0.dynamic_template_data.otp** "346349"
- Indataparameter:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **från.email**:service@contoso.com" "
  - **personalizations.0.subject** "Contoso konto e-verifieringskod"
- Anspråk på utflöde:
  - **requestBody**: JSON-värde

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>FåClaimFromJson

Hämta ett angivet element från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångJson | sträng | De ClaimTypes som används av anspråksomvandlingen för att hämta objektet. |
| InputParameter | claimToExtract | sträng | namnet på det JSON-element som skall extraheras. |
| OutputClaim | extraherasClaim | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats, det elementvärde som anges i parametern _claimToExtract_ input. |

I följande exempel extraherade anspråksomvandlingen elementet `emailAddress` från JSON-data:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Ingående anspråk:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Indataparameter:
    - **claimToExtract**: emailAddress
- Utdataanspråk:
  - **extraherasClaim:**someone@example.com


## <a name="getclaimsfromjsonarray"></a>FåClaimsFromJsonArray

Hämta en lista över angivna element från Json-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonKällaClaim | sträng | De ClaimTypes som används av anspråksomvandlingen för att hämta anspråken. |
| InputParameter | errorOnMissingClaims | boolean | Anger om ett fel ska utlösas om en av anspråken saknas. |
| InputParameter | inkluderaEmptyClaims | sträng | Ange om tomma anspråk ska inkluderas. |
| InputParameter | jsonSourceKeyName (jsonSourceKeyName) | sträng | Namn på elementnyckel |
| InputParameter | jsonSourceValueName | sträng | Namn på elementvärde |
| OutputClaim | Samling | sträng, int, boolesk och datetime |Lista över anspråk att extrahera. Namnet på anspråket ska vara lika med det som anges i indataanspråket _jsonSourceClaim._ |

I följande exempel extraherar anspråksomvandlingen följande anspråk: e-post (sträng), displayName (sträng), membershipNum (int), aktiv (boolesk) och födelsedatum (datetime) från JSON-data.

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

- Ingående anspråk:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Indataparametrar:
    - **errorOnMissingClaims**: falskt
    - **includeEmptyClaims**: falskt
    - **jsonSourceKeyName**: nyckel
    - **jsonSourceValueName**: värde
- Utdataanspråk:
  - **e-post**: "someone@example.com"
  - **displayName**: "Någon"
  - **medlemskapNummer**: 6353399
  - **aktiv**: sant
  - **födelsedatum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>FåNumericClaimFromJson

Hämtar ett angivet numeriskt (långt) element från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångJson | sträng | De ClaimTypes som används av anspråksomvandlingen för att hämta anspråket. |
| InputParameter | claimToExtract | sträng | Namnet på JSON-elementet som ska extraheras. |
| OutputClaim | extraherasClaim | long | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats, elementets värde som anges i indataparametrarna _för anspråktoExtract._ |

I följande exempel extraherar anspråksomvandlingen elementet `id` från JSON-data.

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

- Ingående anspråk:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Indataparametrar
    - **claimToExtract**: id
- Utdataanspråk:
    - **utdragEnKlam**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFrånJson

Hämtar det första elementet från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångJson | sträng | De ClaimTypes som används av anspråksomvandlingen för att hämta objektet från JSON-data. |
| OutputClaim | key | sträng | Den första elementnyckeln i JSON. |
| OutputClaim | värde | sträng | Det första elementvärdet i JSON. |

I följande exempel extraherar anspråksomvandlingen det första elementet (förnamn) från JSON-data.

```XML
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Utdataanspråk:
  - **nyckel**: givenName
  - **värde**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFrånJsonArray

Hämtar det första elementet från en JSON-datamatris.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångJsonClaim | sträng | De ClaimTypes som används av anspråksomvandlingen för att hämta objektet från JSON-matrisen. |
| OutputClaim | extraherasClaim | sträng | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats, det första elementet i JSON-matrisen. |

I följande exempel extraherar anspråksomvandlingen det första elementet `["someone@example.com", "Someone", 6353399]`(e-postadress) från JSON-matrisen .

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

- Ingående anspråk:
  - **inputJsonClaim**:someone@example.com[" ", "Någon", 6353399]
- Utdataanspråk:
  - **extraherasClaim:**someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konverterar XML-data till JSON-format.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | sträng | De ClaimTypes som används av anspråksomvandlingen för att konvertera data från XML till JSON-format. |
| OutputClaim | json | sträng | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats, data i JSON-format. |

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

I följande exempel konverterar anspråksomvandlingen följande XML-data till JSON-format.

#### <a name="example"></a>Exempel
Ingående anspråk:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Anspråk på utflöde:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


