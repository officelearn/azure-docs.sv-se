---
title: Predikat och predikatvalidationer
titleSuffix: Azure AD B2C
description: Förhindra att felaktiga data läggs till i din Azure AD B2C-klient med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 887c9432f04cce775e045bb6da83f0af4a4a4bce
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396896"
---
# <a name="predicates-and-predicatevalidations"></a>Predikat och predikatvalidationer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med elementen **Predikater** och **Predikatvalidationer** kan du utföra en valideringsprocess för att säkerställa att endast korrekt utformade data anges i din Azure Active Directory B2C-klient (Azure AD B2C).

Följande diagram visar förhållandet mellan elementen:

![Diagram som visar predikat- och predikatvaliderrelation](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikat

Det **predikatelementet** definierar en grundläggande validering för att `true` kontrollera `false`värdet för en anspråkstyp och returnerar eller . Valideringen görs med hjälp av ett angivet **metodelement** och en uppsättning **parameterelement** som är relevanta för metoden. Ett predikat kan till exempel kontrollera om längden på ett stränganspråksvärde ligger inom det angivna minimi- och maximiparametrarna eller om ett stränganspråksvärde innehåller en teckenuppsättning. **Elementet UserHelpText** innehåller ett felmeddelande för användare om kontrollen misslyckas. Värdet för **UserHelpText-elementet** kan lokaliseras med hjälp av [språkanpassning](localization.md).

Elementet **Predikat** måste visas direkt efter elementet **ClaimsSchema** i elementet [BuildingBlocks.](buildingblocks.md)

**Predikatelementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Predikat | 1:n | En lista över predikat. |

**Predikatelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatet. Andra element kan använda den här identifieraren i principen. |
| Metod | Ja | Den metodtyp som ska användas för validering. Möjliga värden: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)eller [IsDateRange](#isdaterange).  |
| Hjälptext | Inga | Ett felmeddelande för användare om kontrollen misslyckas. Den här strängen kan lokaliseras med hjälp av [språkanpassningen](localization.md) |

**Predikatelementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Föråldrad) Ett felmeddelande för användare om kontrollen misslyckas. |
| Parametrar | 1:1 | Parametrarna för metodtypen för strängvalideringen. |

Elementet **Parametrar** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Parameter | 1:n | Parametrarna för metodtypen för strängvalideringen. |

**Parameterelementet** innehåller följande attribut:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Id | 1:1 | Parameterns identifierare. |

### <a name="predicate-methods"></a>Predikatmetoder

#### <a name="islengthrange"></a>IsLengthRange (olikartade)

Metoden IsLengthRange kontrollerar om längden på ett stränganspråksvärde ligger inom intervallet för de minsta och högsta parametrar som angetts. Predikatelementet stöder följande parametrar:

| Parameter | Krävs | Beskrivning |
| ------- | ----------- | ----------- |
| Maximal | Ja | Det maximala antalet tecken som kan anges. |
| Minimum | Ja | Det minsta antalet tecken som måste anges. |


I följande exempel visas en IsLengthRange-metod med parametrarna `Minimum` och `Maximum` som anger strängens längdintervall:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatcherRegex

Metoden MatchesRegex kontrollerar om ett stränganspråksvärde matchar ett reguljärt uttryck. Predikatelementet stöder följande parametrar:

| Parameter | Krävs | Beskrivning |
| ------- | ----------- | ----------- |
| RegelbundnaExpression | Ja | Det reguljära uttrycksmönstret som ska matchas. |

I följande exempel `MatchesRegex` visas en `RegularExpression` metod med parametern som anger ett reguljärt uttryck:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>InkluderarTecken

Metoden IncludesCharacters kontrollerar om ett stränganspråksvärde innehåller en teckenuppsättning. Predikatelementet stöder följande parametrar:

| Parameter | Krävs | Beskrivning |
| ------- | ----------- | ----------- |
| Teckenuppsättning | Ja | Den uppsättning tecken som kan anges. Gemener, `A-Z`versaler, siffror `0-9`eller en lista med symboler, `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`till exempel . `a-z` |

I följande exempel `IncludesCharacters` visas en `CharacterSet` metod med parametern som anger teckenuppsättningen:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

Metoden IsDateRange kontrollerar om ett datumanspråksvärde ligger mellan ett intervall med angivna minimi- och maximiparametrar. Predikatelementet stöder följande parametrar:

| Parameter | Krävs | Beskrivning |
| ------- | ----------- | ----------- |
| Maximal | Ja | Det största möjliga datum som kan anges. Formatet på datumet följer `yyyy-mm-dd` konventionen, `Today`eller . |
| Minimum | Ja | Minsta möjliga datum som kan anges. Formatet på datumet följer `yyyy-mm-dd` konventionen, `Today`eller .|

I följande exempel `IsDateRange` visas en `Minimum` `Maximum` metod med parametrarna och `yyyy-mm-dd` som `Today`anger datumintervallet med formatet och .

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredikatValidationer

Medan predikaten definierar valideringen för att kontrollera mot en anspråkstyp, grupperar **predikatvalidationerna** en uppsättning predikat för att bilda en verifiering av indata från användaren som kan tillämpas på en anspråkstyp. Varje **predikatvalidationselement** innehåller en uppsättning **Predikatgruppelement** som innehåller en uppsättning **predikatreferenceelement** som pekar på ett **predikat**. För att klara valideringen bör värdet på anspråket klara alla tester av alla predikat under alla **predikatgruppen** med sin uppsättning **predikatreferenceelement.**

Elementet **Predikatvalidationer** måste visas direkt efter elementet **Predikater** i elementet [BuildingBlocks.](buildingblocks.md)

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

**Elementet Predikatvalidationer** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| PredikatValidation | 1:n | En lista över predikatvalidering. |

**Det predikatvalidationselement** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatvalidering. **Elementet ClaimType** kan använda den här identifieraren i principen. |

**Det predikatvalidationselement** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Predikatgrupper | 1:n | En lista över predikatgrupper. |

**Elementet Predikatgrupper** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Predikatgrupp | 1:n | En lista över predikat. |

**Det predikatgruppselementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatgruppen.  |

**Elementet Predikatgrupp** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  En beskrivning av predikatet som kan vara till hjälp för användarna att veta vilket värde de ska skriva. |
| PredikatReferences | 1:n | En lista med predikatreferenser. |

Elementet **Predikatreferences** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MatchAtLaast | Inga | Anger att värdet måste matcha minst så många predikatdefinitioner för att indata ska accepteras. Om inget anges måste värdet matcha alla predikatdefinitioner. |

Elementet **Predikater** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Predikatreferens | 1:n | En referens till ett predikat. |

**Elementet Predikatreferens innehåller** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för predikatvalidering.  |


## <a name="configure-password-complexity"></a>Konfigurera lösenordskomplexitet

Med **predikat** och **predikatvalidationerInput** kan du styra komplexitetskraven för lösenord som tillhandahålls av en användare när du skapar ett konto. Som standard använder Azure AD B2C starka lösenord. Azure AD B2C stöder också konfigurationsalternativ för att styra komplexiteten i lösenord som kunder kan använda. Du kan definiera lösenordskomplexitet genom att använda dessa predikatelement:

- **IsLengthBetween8And64** med `IsLengthRange` hjälp av metoden, validerar att lösenordet måste vara mellan 8 och 64 tecken.
- **Gemener** `IncludesCharacters` med metoden, verifierar att lösenordet innehåller en gemener.
- **Versaler** med `IncludesCharacters` metoden, verifierar att lösenordet innehåller en versal.
- **Tal** med `IncludesCharacters` metoden, verifierar att lösenordet innehåller en siffra.
- **Symbol** med `IncludesCharacters` metoden, verifierar att lösenordet innehåller ett av flera symboltecken.
- **PIN-kod** med metoden, `MatchesRegex` verifierar att lösenordet endast innehåller siffror.
- **AllowedAADCharacters** med `MatchesRegex` metoden, verifierar att lösenordet endast ogiltigt tecken angavs.
- **Tillåts inteVitrymd** `MatchesRegex` med metoden, verifierar att lösenordet inte börjar eller slutar med ett blanksteg tecken.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

När du har definierat de grundläggande verifieringarna kan du kombinera dem tillsammans och skapa en uppsättning lösenordsprinciper som du kan använda i principen:

- **SimplePassword** validerar det otillåtnavitrymden, allowedAADCharacters och IsLengthBetween8And64
- **StrongPassword** validerar Det otillåtnavitrymden, AllowedAADCharacters, IsLengthBetween8And64. Den sista `CharacterClasses` gruppen kör ytterligare en uppsättning `MatchAtLeast` predikat med inställd på 3. Användarlösenordet måste vara mellan 8 och 16 tecken och tre av följande tecken: Gemener, Versaler, Tal eller Symbol.
- **CustomPassword** validerar endast OtillåtnaVitrymden, AllowedAADCharacters. Så kan användaren ange valfritt lösenord med valfri längd, så länge tecknen är giltiga.

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

I din anspråkstyp lägger du till **elementet PredicateValidationReference** och anger identifieraren som en av predikatvalideringarna, till exempel SimplePassword, StrongPassword eller CustomPassword.

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

Följande visar hur elementen är ordnade när Azure AD B2C visar felmeddelandet:

![Diagram över lösenordskomplexitetskomplexitet i predikat- och predikatgrupp](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurera ett datumintervall

Med elementen **Predikater** och **Predikatvalidationer** kan du styra lägsta och högsta datumvärden `DateTimeDropdown`för **UserInputType** med hjälp av en . För att göra detta, skapa `IsDateRange` ett **predikat** med metoden och ange de minsta och högsta parametrarna.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Lägg till en **predicateValidation** `DateRange` med en referens till predikatet.

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

I anspråkstypen lägger du till **elementet PredicateValidationReference** och anger identifieraren som `CustomDateRange`.

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

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar lösenordskomplexitet med hjälp av anpassade principer i Azure Active Directory B2C](custom-policy-password-complexity.md) med predikatvalningar.