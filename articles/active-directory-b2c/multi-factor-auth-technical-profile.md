---
title: Tekniska profiler för Azure MFA i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad princip referens för Azure Multi-Factor Authentication (MFA) tekniska profiler i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e1b5d6787a41e59511267dd9e7f9048a0431e26a
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204123"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en Azure MFA Technical-profil i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att verifiera ett telefonnummer med hjälp av Azure Multi-Factor Authentication (MFA). Använd den här tekniska profilen för att skapa och skicka en kod till ett telefonnummer och verifiera sedan koden. Azure MFA Technical-profilen kan också returnera ett fel meddelande.  Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska profilen för validering visas ett fel meddelande på en egen kontrollerad sida.

Den här tekniska profilen:

- Tillhandahåller inte ett gränssnitt för att interagera med användaren. I stället anropas användar gränssnittet från en [självkontrollerad](self-asserted-technical-profile.md) teknisk profil, eller en [visnings kontroll](display-controls.md) som en [teknisk validerings profil](validation-technical-profile.md).
- Använder Azure MFA-tjänsten för att skapa och skicka en kod till ett telefonnummer och sedan verifiera koden.  
- Verifierar ett telefonnummer via textmeddelanden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för Azure MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Skicka SMS

Det första läget i den här tekniska profilen är att generera en kod och skicka den. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i den tekniska profilen för MFA.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| userPrincipalName | Yes | Identifieraren för den användare som äger telefonnumret. |
| phoneNumber | Yes | Telefonnumret som en SMS-kod ska skickas till. |
| companyName | No |Företags namnet i SMS. Om inget namn anges används namnet på programmet. |
| locale | No | Språk inställningen för SMS. Om den inte anges används webbläsarens nationella inställningar. |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till Azure MFA-tjänsten.

### <a name="output-claims"></a>Utgående anspråk

Azure MFA Protocol-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock inkludera anspråk som inte returneras av Azure MFA Identity-providern så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Yes | Måste vara **OneWaySMS**.  |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid sändning av SMS-fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | No | Användar fel meddelande om det angivna telefonnumret inte accepterar SMS. |
| UserMessageIfInvalidFormat | No | Användar fel meddelande om det angivna telefonnumret inte är ett giltigt telefonnummer. |
| UserMessageIfServerError | No | Användar fel meddelande om ett internt fel inträffar på servern. |
| UserMessageIfThrottled| No | Användar fel meddelande om en begäran har begränsats.|

### <a name="example-send-an-sms"></a>Exempel: Skicka ett SMS

I följande exempel visas en teknisk Azure MFA-profil som används för att skicka en kod via SMS.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifiera kod

Det andra läget i den här tekniska profilen är att verifiera en kod. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i den tekniska profilen för MFA.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Yes | Samma telefonnummer som tidigare användes för att skicka en kod. Den används också för att hitta en fjärrstyrningssession. |
| verificationCode  | Yes | Verifierings koden som anges av användaren som ska verifieras |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan du anropar tjänsten Azure MFA.

### <a name="output-claims"></a>Utgående anspråk

Azure MFA Protocol-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock inkludera anspråk som inte returneras av Azure MFA Identity-providern så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Yes | Måste **Verifiera** |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid kod verifierings fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Användar fel meddelande om användaren försökte utföra en verifierings kod för många gånger. |
| UserMessageIfServerError | No | Användar fel meddelande om ett internt fel inträffar på servern. |
| UserMessageIfThrottled| No | Användar fel meddelande om begäran är begränsad.|
| UserMessageIfWrongCodeEntered| No| Användar fel meddelande om den kod som angetts för verifiering är felaktig.|

### <a name="example-verify-a-code"></a>Exempel: verifiera en kod

I följande exempel visas en teknisk Azure MFA-profil som används för att verifiera koden.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
