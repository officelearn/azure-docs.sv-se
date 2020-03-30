---
title: Aktivera engångsbefrisörsverifiering
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett engångslösenord (OTP) scenario med hjälp av Azure AD B2C anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332770"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en teknisk engångsnyckel för lösenord i en anpassad azure AD B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att hantera generering och verifiering av ett engångslösenord. Använd en teknisk profil för att generera en kod och verifiera koden senare.

Den tekniska profilen för engångslösenord kan också returnera ett felmeddelande under kodverifiering. Utforma integreringen med engångslösenordet med hjälp av en **teknisk valideringsprofil**. En teknisk profil för validering anropar den tekniska profilen för engångslösenord för att verifiera en kod. Den tekniska profilen för validering validerar data som tillhandahålls av användaren innan användarens färd fortsätter. Med den tekniska profilen för validering visas ett felmeddelande på en sida som själv har bekräftats.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för engångslösenord:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generera kod

Det första läget för den här tekniska profilen är att generera en kod. Nedan finns de alternativ som kan konfigureras för det här läget.

### <a name="input-claims"></a>Ingående anspråk

Elementet **InputClaims** innehåller en lista över anspråk som krävs för att skicka till engångslösenordsprotokollleverantören. Du kan också mappa namnet på ditt anspråk till namnet som definieras nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Identifierare | Ja | Identifieraren för att identifiera användaren som behöver verifiera koden senare. Det används ofta som identifierare för målet där koden levereras till, till exempel e-postadress eller telefonnummer. |

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan de skickas till lösenordsprotokollleverantören.

### <a name="output-claims"></a>Utgående fordringar

**Elementet OutputClaims** innehåller en lista över anspråk som genereras av engångslösenordsprotokollprovidern. Du kan också mappa namnet på ditt anspråk till namnet som definieras nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| otpGenerated (otpGenerated) | Ja | Den genererade koden vars session hanteras av Azure AD B2C. |

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

### <a name="metadata"></a>Metadata

Följande inställningar kan användas för att konfigurera kodgenereringsläge:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Inga | Tid i sekunder tills koden upphör att gälla. Minimum: `60`; Högsta `1200`tillåtna belopp: ; Standard: `600`. |
| KodLängd | Inga | Kodens längd. Standardvärdet är `6`. |
| Teckenuppsättning | Inga | Teckenuppsättningen för koden, formaterad för användning i ett reguljärt uttryck. Till exempel `a-z0-9A-Z`. Standardvärdet är `0-9`. Teckenuppsättningen måste innehålla minst 10 olika tecken i den angivna uppsättningen. |
| NumRetryAttempts | Inga | Antalet verifieringsförsök innan koden anses ogiltig. Standardvärdet är `5`. |
| Åtgärd | Ja | Den åtgärd som ska utföras. Möjligt värde: `GenerateCode`. |
| Återanvändasamkod | Inga | Om en dubblettkod ska anges i stället för att generera en ny kod när given kod inte har upphört att gälla och fortfarande är giltig. Standardvärdet är `false`. |

### <a name="example"></a>Exempel

Följande exempel `TechnicalProfile` används för att generera en kod:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifiera kod

Det andra läget för den här tekniska profilen är att verifiera en kod. Nedan finns de alternativ som kan konfigureras för det här läget.

### <a name="input-claims"></a>Ingående anspråk

Elementet **InputClaims** innehåller en lista över anspråk som krävs för att skicka till engångslösenordsprotokollleverantören. Du kan också mappa namnet på ditt anspråk till namnet som definieras nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Identifierare | Ja | Identifieraren för att identifiera den användare som tidigare har genererat en kod. Det används ofta som identifierare för målet där koden levereras till, till exempel e-postadress eller telefonnummer. |
| otpToVerify (8)otpToVerify otpTo | Ja | Verifieringskoden som tillhandahålls av användaren. |

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan de skickas till lösenordsprotokollleverantören.

### <a name="output-claims"></a>Utgående fordringar

Det finns inga utdataanspråk som tillhandahålls vid kodverifiering av den här protokollleverantören.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

### <a name="metadata"></a>Metadata

Följande inställningar kan användas för att koda verifieringsläget:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Den åtgärd som ska utföras. Möjligt värde: `VerifyCode`. |


### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera felmeddelandena som visas vid kodverifieringsfel. Metadata bör konfigureras i den [självsäkra](self-asserted-technical-profile.md) tekniska profilen. Felmeddelandena kan [lokaliseras](localization-string-ids.md#one-time-password-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesExist | Inga | Meddelandet som ska visas för användaren om kodverifieringssessionen har upphört att gälla. Det är antingen koden har upphört att gälla eller koden har aldrig genererats för en viss identifierare. |
| UserMessageIfMaxRetryAttempted | Inga | Meddelandet som ska visas för användaren om de har överskridit de högsta tillåtna verifieringsförsöken. |
| UserMessageIfInvalidCode | Inga | Meddelandet som ska visas för användaren om de har angett en ogiltig kod. |
|UserMessageIfSessionConflict|Inga| Meddelandet som ska visas för användaren om koden inte kan verifieras.|

### <a name="example"></a>Exempel

Följande exempel `TechnicalProfile` används för att verifiera en kod:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nästa steg

Se följande artikel om du till exempel använder en teknisk profil för engångslösenord med anpassad e-postverifiering:

- [Anpassad e-postverifiering i Azure Active Directory B2C](custom-email.md)

