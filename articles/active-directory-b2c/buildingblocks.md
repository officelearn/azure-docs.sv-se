---
title: BuildingBlocks - Azure Active Directory B2C | Microsoft Docs
description: Ange det BuildingBlocks elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3075c047dd4ff42d0578a6824d95a0f4b822292a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848554"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **BuildingBlocks** element har lagts till i den [TrustFrameworkPolicy](trustframeworkpolicy.md) element.

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
 </BuildingBlocks>
```

Den **BuildingBlocks** elementet innehåller följande element som måste anges i den ordning som definierats:

- [ClaimsSchema](claimsschema.md) -definierar vilka anspråkstyper som kan referera till som en del av principen. Anspråk schemat är den plats där du deklarera dina anspråkstyper. En Anspråkstyp liknar en variabel i många programmässiga språk. Du kan använda Anspråkstypen att samla in data från användare av ditt program, ta emot anspråk från sociala identitetsleverantörer, skicka och ta emot data från en anpassad REST-API eller lagra interna data som används av en egen princip. 

- [Predikat och PredicateValidationsInput](predicates.md) – kan du utföra en verifieringsprocessen för att säkerställa att endast korrekt strukturerad data har angetts till ett anspråk.
 
- [ClaimsTransformations](claimstransformations.md) -innehåller en lista över anspråksomvandlingar som kan användas i din princip.  En anspråkstransformering konverterar ett anspråk till en annan. I anspråkstransformering anger vilken transformering-metod som: 
    - Ändra skiftläge för ett sträng-anspråk till den som angetts. Till exempel ändrar en sträng från gemener till versaler.
    - Jämföra två anspråk och returnera ett anspråk med SANT som du anger som anspråken matchar, annars false.
    - Skapa ett sträng-anspråk från den angivna parametern i principen.
    - Skapa en slumpmässig sträng med slumpmässig talgeneratorns startvärden.
    - Formatera ett anspråk enligt den angivna Formatsträngen. Den här omvandlingen använder C# `String.Format` metod.

- [ContentDefinitions](contentdefinitions.md) -innehåller URL: er för HTML5 mallar att använda i din användarresan. I en anpassad princip definierar en innehållsdefinition sidan HTML5 URI som används för ett specifikt steg på vägen för användaren. Till exempel, logga in eller registrera dig, lösenordsåterställning eller felsidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-fil. Eller så kan du skapa nya innehållsdefinitioner efter dina behov. Det här elementet kan innehålla en referens för lokaliserade resurser med hjälp av ett lokalisering-ID.

- [Lokalisering](localization.md) – gör att du kan ha stöd för flera språk. Lokaliseringsstöd i principer kan du konfigurera listan över språk som stöds i en princip och välja ett standardspråk. Språkspecifika strängar och samlingar stöds också.


