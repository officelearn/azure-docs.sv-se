---
title: Tekniska profiler i Azure MFA i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad principreferens för tekniska profiler för Azure Multi-Factor Authentication (MFA) i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332809"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en teknisk Azure MFA-profil i en anpassad azure AD B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att verifiera ett telefonnummer med hjälp av Azure Multi-Factor Authentication (MFA). Använd den här tekniska profilen för att generera och skicka en kod till ett telefonnummer och sedan verifiera koden. Den tekniska profilen för Azure MFA kan också returnera ett felmeddelande.  Den tekniska profilen för validering validerar data som tillhandahålls av användaren innan användarens färd fortsätter. Med den tekniska profilen för validering visas ett felmeddelande på en självpåförd sida.

Denna tekniska profil:

- Innehåller inget gränssnitt för att interagera med användaren. I stället anropas användargränssnittet från en [självberäns](self-asserted-technical-profile.md) teknisk profil, eller en [visningskontroll](display-controls.md) som en [teknisk profil för validering](validation-technical-profile.md).
- Använder Azure MFA-tjänsten för att generera och skicka en kod till ett telefonnummer och verifierar sedan koden.  
- Validerar ett telefonnummer via textmeddelanden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C:

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

### <a name="input-claims"></a>Ingående anspråk

Elementet **InputClaims** innehåller en lista över anspråk som ska skickas till Azure MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i MFA:s tekniska profil.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| userPrincipalName | Ja | Identifieraren för den användare som äger telefonnumret. |
| phoneNumber | Ja | Telefonnumret för att skicka en SMS-kod till. |
| företagsNamn | Inga |Företagsnamnet i SMS:et. Om inget anges används namnet på ditt program. |
| språk | Inga | Språken i SMS. Om det inte anges används användarens webbläsarlokal. |

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan de skickas till Azure MFA-tjänsten.

### <a name="output-claims"></a>Utgående fordringar

Azure MFA-protokollprovidern **returnerar inga OutputClaims**, vilket innebär att det inte finns något behov av att ange utdataanspråk. Du kan dock inkludera anspråk som inte returneras av Azure MFA-identitetsprovidern så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste vara **OneWaySMS**.  |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera felmeddelandena som visas när SMS-fel skickas. Metadata bör konfigureras i den [självsäkra](self-asserted-technical-profile.md) tekniska profilen. Felmeddelandena kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Inga | Felmeddelande om det angivna telefonnumret inte accepterar SMS. |
| UserMessageIfInvalidFormat | Inga | Felmeddelande om det angivna telefonnumret inte är ett giltigt telefonnummer. |
| AnvändareMessageIfServerError | Inga | Felmeddelande om servern har påträffat ett internt fel. |
| UserMessageIfTrottad| Inga | Felmeddelande om en begäran har begränsats.|

### <a name="example-send-an-sms"></a>Exempel: skicka ett SMS

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

Det andra läget för den här tekniska profilen är att verifiera en kod. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Ingående anspråk

Elementet **InputClaims** innehåller en lista över anspråk som ska skickas till Azure MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i MFA:s tekniska profil.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Ja | Samma telefonnummer som tidigare användes för att skicka en kod. Det används också för att hitta en telefon verifieringssession. |
| verifieringCode  | Ja | Verifieringskoden som tillhandahålls av den användare som ska verifieras |

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan azure MFA-tjänsten anropas.

### <a name="output-claims"></a>Utgående fordringar

Azure MFA-protokollprovidern **returnerar inga OutputClaims**, vilket innebär att det inte finns något behov av att ange utdataanspråk. Du kan dock inkludera anspråk som inte returneras av Azure MFA-identitetsprovidern så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste **verifieras** |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera felmeddelandena som visas vid kodverifieringsfel. Metadata bör konfigureras i den [självsäkra](self-asserted-technical-profile.md) tekniska profilen. Felmeddelandena kan [lokaliseras](localization-string-ids.md#azure-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Inga | Felmeddelande om användaren har försökt en verifieringskod för många gånger. |
| AnvändareMessageIfServerError | Inga | Felmeddelande om servern har påträffat ett internt fel. |
| UserMessageIfTrottad| Inga | Felmeddelande om begäran begränsas.|
| UserMessageIfWrongCode Angivet| Inga| Felmeddelande om koden som angetts för verifiering är felaktig.|

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
