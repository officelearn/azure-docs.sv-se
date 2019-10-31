---
title: Predikat och PredicateValidations-Azure Active Directory B2C | Microsoft Docs
description: Insamlings exempel för sociala konto anspråk för Azure Active Directory B2C i identitets upplevelsens Ramverks schema.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1f08589ae28b3e19d2a4fdb3e3862e127a810cc
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73099710"
---
# <a name="predicates-and-predicatevalidations"></a>Predikat och PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predikat** -och **PredicateValidations** -elementen gör att du kan utföra en verifierings process för att säkerställa att endast korrekt utformade data anges i din Azure Active Directory B2C (Azure AD B2C)-klient.

I följande diagram visas relationen mellan elementen:

![Diagram över tillåtna predikat och predikat](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikat

**Predikatet predikat** definierar en grundläggande verifiering för att kontrol lera värdet för en anspråks typ och returnerar `true` eller `false`. Verifieringen görs med hjälp av ett angivet **metod** element och en uppsättning **parameter** element som är relevanta för metoden. Ett predikat kan till exempel kontrol lera om längden på ett sträng anspråks värde ligger inom intervallet för de lägsta och högsta parametrarna som anges, eller om ett sträng anspråks värde innehåller en teckenuppsättning. **UserHelpText** -elementet ger ett fel meddelande för användare om kontrollen Miss lyckas. Värdet för **UserHelpText** -elementet kan lokaliseras med [språk anpassning](localization.md).

**Predikat** -elementet måste visas direkt efter **ClaimsSchema** -elementet i [BuildingBlocks](buildingblocks.md) -elementet.

**Predikat** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Predikatet | 1: n | En lista med predikat. |

**Predikatet predikat** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatet. Andra element kan använda den här identifieraren i principen. |
| Metod | Ja | Den typ av metod som ska användas för verifiering. Möjliga värden: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**eller **IsDateRange**. Värdet **IsLengthRange** kontrollerar om längden på ett sträng anspråks värde ligger inom intervallet för lägsta och högsta angivna parametrar. Värdet **MatchesRegex** kontrollerar om ett sträng anspråks värde matchar ett reguljärt uttryck. Värdet **IncludesCharacters** kontrollerar om ett sträng anspråks värde innehåller en teckenuppsättning. Värdet **IsDateRange** kontrollerar om ett datum anspråks värde är mellan ett intervall av minsta och högsta angivna parametrar. |

**Predikatet predikat** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Ett fel meddelande för användare om kontrollen Miss lyckas. Den här strängen kan lokaliseras med hjälp av [språk anpassning](localization.md) |
| Parametrar | 1:1 | Parametrarna för metod typen för sträng validering. |

Elementet **Parameters** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Parameter | 1: n | Parametrarna för metod typen för sträng validering. |

**Parameter** elementet innehåller följande attribut:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Id | 1:1 | Parameterns identifierare. |

I följande exempel visas en `IsLengthRange`-metod med parametrarna `Minimum` och `Maximum` som anger längd intervallet för strängen:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `MatchesRegex`-metod med parametern `RegularExpression` som anger ett reguljärt uttryck:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `IncludesCharacters`-metod med parametern `CharacterSet` som anger en uppsättning tecken:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

I följande exempel visas en `IsDateRange`-metod med parametrarna `Minimum` och `Maximum` som anger datum intervallet med formatet `yyyy-MM-dd` och `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Även om predikatet definierar verifieringen för att kontrol lera mot en anspråks typ, kan **PredicateValidations** gruppera en uppsättning predikat som utgör en verifiering av användarindata som kan tillämpas på en anspråks typ. Varje **PredicateValidation** -element innehåller en uppsättning **PredicateGroup** -element som innehåller en uppsättning **PredicateReference** -element som pekar på ett **predikat**. För att kunna skicka verifieringen bör värdet för anspråket klara alla tester av alla predikat under alla **PredicateGroup** med deras uppsättning **PredicateReference** -element.

**PredicateValidations** -elementet måste visas direkt efter **predikat** -elementet i [BuildingBlocks](buildingblocks.md) -elementet.

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

**PredicateValidations** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | En lista över predikat-verifiering. |

**PredicateValidation** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för verifiering av predikat. Elementet **claimType** kan använda den här identifieraren i principen. |

**PredicateValidation** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | En lista över predikaa grupper. |

**PredicateGroups** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | En lista med predikat. |

**PredicateGroup** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för gruppen predikat.  |

**PredicateGroup** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  En beskrivning av predikatet som kan vara till hjälp för användare att veta vilket värde de ska skriva. |
| PredicateReferences | 1: n | En lista över predika-referenser. |

**PredicateReferences** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MatchAtLeast | Nej | Anger att värdet måste matcha minst det antal predikat som ska godkännas. |

**PredicateReferences** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | En referens till ett predikat. |

**PredicateReference** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för verifiering av predikat.  |


## <a name="configure-password-complexity"></a>Konfigurera lösen ords komplexitet

Med **predikat** och **PredicateValidationsInput** kan du kontrol lera komplexitets kraven för lösen ord som tillhandahålls av en användare när du skapar ett konto. Som standard använder Azure AD B2C starka lösen ord. Azure AD B2C också stöd för konfigurations alternativ för att kontrol lera komplexiteten för lösen ord som kunder kan använda. Du kan definiera lösen ords komplexitet genom att använda dessa predikaa element:

- **IsLengthBetween8And64** som använder metoden `IsLengthRange` verifierar att lösen ordet måste innehålla mellan 8 och 64 tecken.
- **Gemener** med metoden `IncludesCharacters` kontrollerar att lösen ordet innehåller en gemen bokstav.
- **Versaler** med hjälp av metoden `IncludesCharacters` verifierar att lösen ordet innehåller en versal bokstav.
- **Tal** med hjälp av metoden `IncludesCharacters` verifierar att lösen ordet innehåller en siffra.
- **Symbol** med hjälp av metoden `IncludesCharacters` verifierar att lösen ordet innehåller ett av flera symbol tecken.
- **PIN-kod** med hjälp av metoden `MatchesRegex` verifierar att lösen ordet endast innehåller siffror.
- **AllowedAADCharacters** med hjälp av metoden `MatchesRegex` verifierar att lösen ordet endast ogiltigt Character angavs.
- **DisallowedWhitespace** med hjälp av metoden `MatchesRegex` verifierar att lösen ordet inte börjar eller slutar med ett blank stegs tecken.

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
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
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

När du har definierat de grundläggande valideringarna kan du kombinera dem tillsammans och skapa en uppsättning lösen ords principer som du kan använda i principen:

- **SimplePassword** verifierar DisallowedWhitespace, AllowedAADCharacters och IsLengthBetween8And64
- **StrongPassword** verifierar DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Den sista gruppen `CharacterClasses` kör ytterligare en uppsättning predikat med `MatchAtLeast` inställd på 3. Användarens lösen ord måste vara mellan 8 och 16 tecken och tre av följande tecken: gemener, versaler, siffror och symboler.
- **CustomPassword** validerar endast DisallowedWhitespace, AllowedAADCharacters. Användaren kan alltså ange alla lösen ord med valfri längd, förutsatt att tecknen är giltiga.

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

I anspråks typen lägger du till **PredicateValidationReference** -elementet och anger identifieraren som ett av predikat-valideringarna, till exempel SimplePassword, StrongPassword eller CustomPassword.

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

Följande visar hur elementen ordnas när Azure AD B2C visar fel meddelandet:

![Diagram över predikat och PredicateGroup lösen ord komplexitets exempel](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurera ett datum intervall

Med **predikat** och **PredicateValidations** -element kan du styra de lägsta och högsta datum värdena för **UserInputType** med hjälp av en `DateTimeDropdown`. Det gör du genom att skapa ett **predikat** med metoden `IsDateRange` och ange de lägsta och högsta parametrarna.

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

Lägg till en **PredicateValidation** med en referens till `DateRange` predikatet.

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

I anspråks typen lägger du till **PredicateValidationReference** -element och anger identifieraren som `CustomDateRange`.

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
