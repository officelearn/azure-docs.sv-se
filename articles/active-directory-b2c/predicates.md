---
title: Predikat och PredicateValidations – Azure Active Directory B2C | Microsoft Docs
description: Socialt konto anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: eff8e2cedd6f9388d811002a622dbcdcd0e7ed6c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714163"
---
# <a name="predicates-and-predicatevalidations"></a>Predikat och PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **predikat** och **PredicateValidations** element kan du utföra en verifieringsprocessen för att säkerställa att endast korrekt strukturerad data har angetts i din Azure Active Directory (Azure AD) B2C-klient .  

Följande diagram visar relationen mellan element:  

![Predikat](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikat  

Den **predikat** elementet definierar en grundläggande verifiering för att kontrollera värdet för en Anspråkstyp och returnerar `true` eller `false`. Verifieringen är klar med hjälp av en angiven **metoden** element och en uppsättning **parametern** element som är relevanta för metoden. Ett predikat kan exempelvis kontrollera om längden på ett strängvärde för anspråk är inom intervallet för lägsta och högsta parametrar har angetts eller om ett strängvärde för anspråk innehåller en teckenuppsättning. Den **UserHelpText** elementet innehåller ett felmeddelande visas för användarna om den inte. Värdet för **UserHelpText** element kan lokaliseras med [språkanpassning](localization.md).

Den **predikat** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Predikat | 1:n | En lista över predikat. | 

Den **predikat** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatet. Andra element kan använda den här identifieraren i principen. |
| Metod | Ja | Metodtyp för validering. Möjliga värden: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**, eller **IsDateRange**. Den **IsLengthRange** värde kontrollerar om längden på ett strängvärde för anspråk är inom intervallet för lägsta och högsta parametrar har angetts. Den **MatchesRegex** värde kontrollerar om ett sträng-anspråksvärde matchar ett reguljärt uttryck. Den **IncludesCharacters** värde kontrollerar om ett strängvärde för anspråk innehåller en teckenuppsättning. Den **IsDateRange** värde kontrollerar om ett datumvärde anspråk mellan olika lägsta och högsta parametrar har angetts. |

Den **predikat** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Ett felmeddelande visas för användare om den inte. Den här strängen kan lokaliseras med hjälp av den [språkanpassning](localization.md) |
| Parametrar | 1:1 | Parametrar för metodtyp av verifieringen av strängen. | 

Den **parametrar** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Parameter | 1:n | Parametrar för metodtyp av verifieringen av strängen. | 

Den **parametern** elementet innehåller följande attribut:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identifierare för parametern. |

I följande exempel visas en `IsLengthRange` metod med parametrar `Minimum` och `Maximum` som anger intervallet längden på strängen:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `MatchesRegex` metod med parametern `RegularExpression` som anger ett reguljärt uttryck:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `IncludesCharacters` metod med parametern `CharacterSet` som anger teckenuppsättningen:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `IsDateRange` metod med parametrar `Minimum` och `Maximum` som anger intervallet med ett format för `yyyy-MM-dd` och `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

Medan predikat definiera verifieringen ska kontrollera mot en Anspråkstyp den **PredicateValidations** gruppera en uppsättning predikat för att bilda en verifiering av indata användare som kan tillämpas på en anspråkstyp. Varje **PredicateValidation** elementet innehåller en uppsättning **PredicateGroup** element som innehåller en uppsättning **PredicateReference** element som pekar på en **Predikat**. Om du vill skicka verifieringen anspråkets värde ska ha genomgått alla tester för alla predikat i alla de **PredicateGroup** med en uppsättning **PredicateReference** element.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Den **PredicateValidations** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | En lista över predikat verifiering. | 

Den **PredicateValidation** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatet verifieringen. Den **ClaimType** element kan använda den här identifieraren i principen. |

Den **PredicateValidation** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | En lista över predikat grupper. | 

Den **PredicateGroups** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | En lista över predikat. | 

Den **PredicateGroup** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för gruppen predikat.  |

Den **PredicateGroup** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  En beskrivning av predikat som kan vara till hjälp för användare att veta vilket värde som de ska ange. | 
| PredicateReferences | 1:n | En lista över predikatreferenser. | 

Den **PredicateReferences** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MatchAtLeast | Nej | Anger att värdet matchar minst många predikat definitioner för indata ska godkännas. |

Den **PredicateReferences** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | En referens till ett predikat. | 

Den **PredicateReference** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatet verifieringen.  |


## <a name="configure-password-complexity"></a>Konfigurera lösenordskomplexitet

Med **predikat** och **PredicateValidationsInput** du kan styra komplexitetskrav för lösenord har angetts av en användare när du skapar ett konto. Som standard använder Azure AD B2C starka lösenord. Azure AD-B2C stöder också alternativ för att styra komplexitet och deras lösenord som kunder kan använda. Du kan definiera lösenordens komplexitet genom att använda dessa predikat element: 

- **IsLengthBetween8And64** med hjälp av den `IsLengthRange` metod, verifierar att lösenordet måste innehålla mellan 8 och 64 tecken.
- **Gemener** med hjälp av den `IncludesCharacters` metod, verifierar att lösenordet innehåller en gemen bokstav.
- **Versaler** med hjälp av den `IncludesCharacters` metod, verifierar att lösenordet innehåller en versal.
- **Antal** med hjälp av den `IncludesCharacters` metod, verifierar att lösenordet innehåller en siffra.
- **Symbol** med hjälp av den `IncludesCharacters` metod, verifierar att lösenord innehåller något av följande symboler `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN-kod** med hjälp av den `MatchesRegex` metod, verifierar att lösenordet innehåller enbart siffror.
- **AllowedAADCharacters** med hjälp av den `MatchesRegex` metod, verifierar att det endast ogiltigt tecknet i lösenord har angetts.
- **DisallowedWhitespace** med hjälp av den `MatchesRegex` metod, verifierar att lösenordet inte börja eller sluta med ett blankstegstecken.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

När du definierar de grundläggande kontroller kan du kombinera dem tillsammans och skapa en uppsättning principer för lösenord som du kan använda i din princip:

- **SimplePassword** verifierar DisallowedWhitespace, AllowedAADCharacters och IsLengthBetween8And64
- **StrongPassword** verifierar DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Den sista gruppen `CharacterClasses` kör ytterligare en uppsättning predikat med `MatchAtLeast` inställd på 3. Användarens lösenord måste vara mellan 8 och 16 tecken och tre av följande tecken: Gemener, versaler, tal eller Symbol.
- **CustomPassword** verifierar endast DisallowedWhitespace AllowedAADCharacters. Därför kan användaren ange alla lösenord med en längd som tecknen är giltiga.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Lägg till i din Anspråkstyp den **PredicateValidationReference** element och ange identifieraren som en av de predikat kontroller, till exempel SimplePassword, StrongPassword eller CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Nedan visas hur elementen är ordnade när Azure AD B2C visas ett felmeddelande:

![Predikatet process](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurera ett datumintervall

Med den **predikat** och **PredicateValidations** element som du kan kontrollera lägsta och högsta datumvärdena i de **UserInputType** med hjälp av en `DateTimeDropdown`. Om du vill göra detta måste du skapa en **predikat** med den `IsDateRange` metoden och ange lägsta och högsta parametrar.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Lägg till en **PredicateValidation** med en referens till den `DateRange` predikat.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Lägg till i din Anspråkstyp **PredicateValidationReference** element och ange identifieraren som `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
