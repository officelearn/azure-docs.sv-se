---
title: ClaimsTransformations – Azure Active Directory B2C | Microsoft-dokument
description: Definition av elementet ClaimsTransformations i Identity Experience Framework-schemat för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e71d521dce40f6a8ec81286fcc95dc97bf10078c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189744"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Elementet **ClaimsTransformations** innehåller en lista över anspråksomvandlingsfunktioner som kan användas i användarfärder som en del av en [anpassad princip](custom-policy-overview.md). En anspråksomvandling konverterar ett visst anspråk till en annan. I anspråksomvandlingen anger du omformningsmetoden, till exempel att lägga till ett objekt i en strängsamling eller ändra skiftläge för en sträng.

Om du vill inkludera listan över anspråksomvandlingsfunktioner som kan användas i användarens färder måste ett XML-element för anspråksöversättningar deklareras under avsnittet BuildingBlocks i principen.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**Elementet ClaimsTransformation** innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- |-------- | ----------- |
| Id |Ja | En identifierare som används för att unikt identifiera anspråksomvandlingen. Identifieraren refereras från andra XML-element i principen. |
| TransformationMethod | Ja | Transformeringsmetoden som ska användas i anspråksomvandlingen. Varje anspråksomvandling har sina egna värden. Se [referensen för anspråksomvandling](#claims-transformations-reference) för en fullständig lista över tillgängliga värden. |

## <a name="claimstransformation"></a>Skadetransformation

**Elementet ClaimsTransformation** innehåller följande element:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Händelser | Beskrivning |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | En lista över **InputClaim-element** som anger anspråkstyper som tas som indata till anspråksomvandlingen. Vart och ett av dessa element innehåller en referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema i principen. |
| Indataparametrar | 0:1 | En lista över **InputParameter-element** som tillhandahålls som indata till anspråksomvandlingen.
| OutputClaims | 0:1 | En lista över **OutputClaim-element** som anger anspråkstyper som produceras efter att ClaimsTransformation har anropats. Vart och ett av dessa element innehåller en referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

**Elementet InputClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | En förväntad indataanspråkstyp. |

#### <a name="inputclaim"></a>InputClaim

**Elementet InputClaim** innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ja | En referens till en ClaimType som redan definierats i avsnittet ClaimsSchema i principen. |
| TransformationClaimType |Ja | En identifierare som refererar till en omvandlingsanspråkstyp. Varje anspråksomvandling har sina egna värden. Se [referensen för anspråksomvandling](#claims-transformations-reference) för en fullständig lista över tillgängliga värden. |

### <a name="inputparameters"></a>Indataparametrar

**Elementet InputParameters** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | En förväntad indataparameter. |

#### <a name="inputparameter"></a>InputParameter

| Attribut | Krävs |Beskrivning |
| --------- | ----------- |----------- |
| Id | Ja | En identifierare som är en referens till en parameter för anspråksomvandlingsmetoden. Varje anspråksomvandlingsmetod har sina egna värden. Se tabellen anspråksomvandling för en fullständig lista över tillgängliga värden. |
| DataType | Ja | Typen av data för parametern, till exempel String, Boolean, Int eller DateTime enligt DataType-uppräkningen i det anpassade xml-schemat för principen. Den här typen används för att utföra aritmetiska åtgärder korrekt. Varje anspråksomvandling har sina egna värden. Se [referensen för anspråksomvandling](#claims-transformations-reference) för en fullständig lista över tillgängliga värden. |
| Värde | Ja | Ett värde som skickas ordagrant till omvandlingen. Vissa av värdena är godtyckliga, några av dem väljer du från anspråksomvandlingsmetoden. |

### <a name="outputclaims"></a>OutputClaims

**Elementet OutputClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | En alternativ typ av utdataanspråk. |

#### <a name="outputclaim"></a>OutputClaim

**Elementet OutputClaim** innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ja | En referens till en ClaimType som redan definierats i avsnittet ClaimsSchema i principen.
| TransformationClaimType | Ja | En identifierare som refererar till en omvandlingsanspråkstyp. Varje anspråksomvandling har sina egna värden. Se [referensen för anspråksomvandling](#claims-transformations-reference) för en fullständig lista över tillgängliga värden. |

Om indataanspråk och utdataanspråk är av samma typ (sträng eller boolesk) kan du använda samma indataanspråk som utdataanspråket. I det här fallet ändrar anspråksomvandlingen indataanspråket med utdatavärdet.

## <a name="example"></a>Exempel

Du kan till exempel lagra den senaste versionen av dina användarvillkor som användaren har godkänt. När du uppdaterar villkoren för tjänster kan du be användaren att acceptera den nya versionen. I följande exempel jämför **HasTOSVersionChanged-anspråksomvandlingen** värdet för **TOSVersion-anspråket** med värdet för **LastTOSAcceptedVersion-anspråket** och returnerar sedan det booleska **TOSVersionChanged-anspråket.**

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Referens för anspråksomvandlingar

Exempel på anspråksomvandlingar finns på följande referenssidor:

- [Boolean](boolean-transformations.md)
- [Datum](date-transformations.md)
- [Heltal](integer-transformations.md)
- [Json](json-transformations.md)
- [Allmänt](general-transformations.md)
- [Socialt konto](social-transformations.md)
- [Sträng](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

