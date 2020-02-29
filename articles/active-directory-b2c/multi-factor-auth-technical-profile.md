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
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184289"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en Azure MFA Technical-profil i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att verifiera ett telefonnummer med hjälp av Azure Multi-Factor Authentication (MFA). Använd den här tekniska profilen för att skapa och skicka en kod till ett telefonnummer och verifiera sedan koden.

Azure MFA Technical-profilen kan också returnera ett fel meddelande. Du kan utforma integrationen med Azure MFA genom att använda en **validerings teknisk profil**. En teknisk verifierings profil anropar tjänsten Azure MFA. Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska verifierings profilen visas ett fel meddelande på en egen kontrollerad sida.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för Azure MFA:

```XML
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
| userPrincipalName | Ja | Identifieraren för den användare som äger telefonnumret. |
| phoneNumber | Ja | Telefonnumret som en SMS-kod ska skickas till. |
| companyName | Nej |Företags namnet i SMS. Om inget namn anges används namnet på programmet. |
| språk | Nej | Språk inställningen för SMS. Om den inte anges används webbläsarens nationella inställningar. |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till Azure MFA-tjänsten.

### <a name="output-claims"></a>Utgående anspråk

Azure MFA Protocol-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure MFA Identity-providern så länge som du ställer in `DefaultValue`-attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste vara **OneWaySMS**.  |
| UserMessageIfInvalidFormat | Nej | Anpassat fel meddelande om det angivna telefonnumret inte är ett giltigt telefonnummer |
| UserMessageIfCouldntSendSms | Nej | Anpassat fel meddelande om det angivna telefonnumret inte accepterar SMS |
| UserMessageIfServerError | Nej | Anpassat fel meddelande om ett internt fel uppstått på servern |

### <a name="return-an-error-message"></a>Returnera ett fel meddelande

Som det beskrivs i [metadata](#metadata)kan du anpassa fel meddelandet som visas för användaren om olika fel inträffar. Du kan ytterligare lokalisera dessa meddelanden genom att prefixet för nationella inställningar. Några exempel:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Exempel: Skicka ett SMS

I följande exempel visas en teknisk Azure MFA-profil som används för att skicka en kod via SMS.

```XML
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
| phoneNumber| Ja | Samma telefonnummer som tidigare användes för att skicka en kod. Den används också för att hitta en fjärrstyrningssession. |
| verificationCode  | Ja | Verifierings koden som anges av användaren som ska verifieras |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan du anropar tjänsten Azure MFA.

### <a name="output-claims"></a>Utgående anspråk

Azure MFA Protocol-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure MFA Identity-providern så länge som du ställer in `DefaultValue`-attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste **Verifiera** |
| UserMessageIfInvalidFormat | Nej | Anpassat fel meddelande om det angivna telefonnumret inte är ett giltigt telefonnummer |
| UserMessageIfWrongCodeEntered | Nej | Anpassat fel meddelande om den kod som angetts för verifiering är felaktig |
| UserMessageIfMaxAllowedCodeRetryReached | Nej | Anpassat fel meddelande om användaren försökte utföra en verifierings kod för många gånger |
| UserMessageIfThrottled | Nej | Anpassat fel meddelande om användaren är begränsad |
| UserMessageIfServerError | Nej | Anpassat fel meddelande om ett internt fel uppstått på servern |

### <a name="return-an-error-message"></a>Returnera ett fel meddelande

Som det beskrivs i [metadata](#metadata)kan du anpassa fel meddelandet som visas för användaren om olika fel inträffar. Du kan ytterligare lokalisera dessa meddelanden genom att prefixet för nationella inställningar. Några exempel:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Exempel: verifiera en kod

I följande exempel visas en teknisk Azure MFA-profil som används för att verifiera koden.

```XML
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
