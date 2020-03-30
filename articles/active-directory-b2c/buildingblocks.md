---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Ange elementet BuildingBlocks i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189879"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Elementet BuildingBlocks** läggs till i elementet [TrustFrameworkPolicy.](trustframeworkpolicy.md)

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

**Elementet BuildingBlocks** innehåller följande element som måste anges i den ordning som definierats:

- [ClaimsSchema](claimsschema.md) - Definierar de anspråkstyper som kan refereras som en del av principen. Anspråksschemat är den plats där du deklarerar dina anspråkstyper. En anspråkstyp liknar en variabel på många programmatiska språk. Du kan använda anspråkstypen för att samla in data från användaren av ditt program, ta emot anspråk från leverantörer av social identitet, skicka och ta emot data från ett anpassat REST API eller lagra interna data som används av din anpassade princip.

- [Predikat och predikatvalidationerInput](predicates.md) - Gör att du kan utföra en valideringsprocess för att säkerställa att endast korrekt formade data anges i ett anspråk.

- [ClaimsTransformations](claimstransformations.md) - Innehåller en lista över anspråksomvandlingar som kan användas i din princip.  En anspråksomvandling konverterar ett anspråk till ett annat. I anspråksomvandlingen anger du en transformeringsmetod, till exempel:
  - Ändra fallet med ett stränganspråk till det angivna. Till exempel ändra en sträng från gemener till versaler.
  - Jämföra två anspråk och returnera ett anspråk med sant som anger att anspråken matchar, annars falska.
  - Skapa ett stränganspråk från den angivna parametern i principen.
  - Skapa en slumpmässig sträng med hjälp av slumptalsgeneratorn.
  - Formatera ett anspråk enligt den angivna formatsträngen. Den här omvandlingen `String.Format` använder C#-metoden.

- InputValidation - Med det här elementet kan du utföra booleska aggregeringar som liknar *och* och *eller*.

- [ContentDefinitions](contentdefinitions.md) - Innehåller webbadresser för HTML5-mallar som ska användas i din användarfärd. I en anpassad princip definierar en innehållsdefinition HTML5-sidans URI som används för ett angivet steg i användarfärden. Till exempel inloggnings- eller registrerings-, lösenordsåterställnings- eller felsidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Du kan också skapa nya innehållsdefinitioner efter dina behov. Det här elementet kan innehålla en lokal resursreferens med hjälp av ett lokaliserings-ID.

- [Lokalisering](localization.md) - Gör att du kan stödja flera språk. Med lokaliseringsstödet i principer kan du ställa in listan över språk som stöds i en princip och välja ett standardspråk. Språkspecifika strängar och samlingar stöds också.

- [DisplayControls](display-controls.md) - Definierar de kontroller som ska visas på en sida. Displaykontroller har speciella funktioner och interagerar med avancerade valideringstekniska profiler. Visningskontrollerna förhandsgranskas för närvarande **.**
