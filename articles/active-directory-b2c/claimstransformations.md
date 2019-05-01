---
title: ClaimsTransformations - Azure Active Directory B2C | Microsoft Docs
description: Definition av ClaimsTransformations-elementet i den identitet upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 65b64563bf00bb519a65b6d2e0418b4f755dea2d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710829"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **ClaimsTransformations** elementet innehåller en lista över anspråk omvandling funktioner som kan användas i utbildning för användaren som en del av en [anpassad princip](active-directory-b2c-overview-custom.md). En anspråkstransformering konverterar en given anspråk till en annan. I anspråkstransformering anger du metoden transformering, till exempel att lägga till ett objekt i en sträng-samling eller ändra skiftläge i en sträng.

För att inkludera listan över anspråk omvandling funktioner som kan användas i användaren resor, måste ett ClaimsTransformations XML-element deklareras under avsnittet BuildingBlocks för principen.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Den **ClaimsTransformation** elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- |-------- | ----------- |
| Id |Ja | En identifierare som används för att unikt identifiera anspråksomvandling. Identifieraren refereras från andra XML-element i principen. |
| TransformationMethod | Ja | Transformering metoden att använda den omvandling av anspråk. Varje anspråksomvandling har en egen uppsättning egenskapsvärden. Se den [anspråk Omvandlingsreferens](#claims-transformations-reference) för en fullständig lista över de tillgängliga värdena. |

## <a name="claimstransformation"></a>ClaimsTransformation

Den **ClaimsTransformation** elementet innehåller följande element:

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


| Element | Förekomster | Beskrivning |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | En lista över **InputClaim** element som anger anspråkstyper som används som indata för anspråkstransformering. Var och en av de här elementen innehåller en referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema i principen. |
| InputParameters | 0:1 | En lista över **indataparametrar** element som anges som indata till anspråkstransformering.  
| OutputClaims | 0:1 | En lista över **OutputClaim** element som anger anspråk typer som genereras när ClaimsTransformation har anropats. Var och en av de här elementen innehåller en referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

Den **InputClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Förväntade indata Anspråkstypen. |

#### <a name="inputclaim"></a>InputClaim

Den **InputClaim** elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ja | En referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema i principen. |
| TransformationClaimType |Ja | En identifierare för att referera till en transformation Anspråkstypen. Varje anspråksomvandling har en egen uppsättning egenskapsvärden. Se den [anspråk Omvandlingsreferens](#claims-transformations-reference) för en fullständig lista över de tillgängliga värdena. |

### <a name="inputparameters"></a>InputParameters

Den **indataparametrar** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | En förväntad indataparameter. |

#### <a name="inputparameter"></a>InputParameter

| Attribut | Krävs |Beskrivning |
| --------- | ----------- |----------- |
| Id | Ja | En identifierare som är en referens till en parameter för metoden anspråk omvandling. Varje metod för omvandling av anspråk har en egen uppsättning egenskapsvärden. Finns i tabellen för omvandling av anspråk för en fullständig lista över de tillgängliga värdena. |
| Datatyp | Ja | Vilken typ av data för parametern, till exempel sträng, Boolean, Int eller datum/tid enligt uppräkningen DataType i anpassad princip för XML-schema. Den här typen används för att utföra aritmetiska åtgärder på rätt sätt. Varje anspråksomvandling har en egen uppsättning egenskapsvärden. Se den [anspråk Omvandlingsreferens](#claims-transformations-reference) för en fullständig lista över de tillgängliga värdena. |
| Värde | Ja | Ett värde som skickades ordagrant till transformeringen. Vissa av värdena är valfri, några av dem du välja från metoden anspråk omvandling. |

### <a name="outputclaims"></a>OutputClaims

Den **OutputClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Förväntad utdata Anspråkstypen. |

#### <a name="outputclaim"></a>OutputClaim 

Den **OutputClaim** elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ja | En referens till en ClaimType som redan har definierats i avsnittet ClaimsSchema i principen.
| TransformationClaimType | Ja | En identifierare för att referera till en transformation Anspråkstypen. Varje anspråksomvandling har en egen uppsättning egenskapsvärden. Se den [anspråk Omvandlingsreferens](#claims-transformations-reference) för en fullständig lista över de tillgängliga värdena. |
 
Om inkommande anspråk och utdata-anspråket är samma typ (sträng eller booleskt värde), kan du använda samma inkommande anspråk som utdata-anspråket. I det här fallet ändrar anspråkstransformering inkommande anspråk med värdet.

## <a name="example"></a>Exempel

Du kan till exempel lagra den senaste versionen av tjänster som användaren har accepterat dina villkor. När du uppdaterar villkoren i tjänster, kan du be användaren att godkänna den nya versionen. I följande exempel visas den **HasTOSVersionChanged** anspråkstransformering jämför värdet för den **TOSVersion** anspråk med värdet för den **LastTOSAcceptedVersion**anspråk och returnerar sedan resultatet **TOSVersionChanged** anspråk.

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

## <a name="claims-transformations-reference"></a>Anspråk transformationer referens

Exempel på anspråksomvandlingar finns på följande referenssidor:

- [Boolesk](boolean-transformations.md)
- [datum](date-transformations.md)
- [heltal](integer-transformations.md)
- [JSON](json-transformations.md)
- [Allmänt](general-transformations.md)
- [Socialt konto](social-transformations.md)
- [Sträng](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

