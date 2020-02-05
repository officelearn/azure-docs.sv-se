---
title: ClaimsProviders – Azure Active Directory B2C | Microsoft Docs
description: Ange ClaimsProvider-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bda94344a22f39daf104e7b95e33723187c4aa1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983018"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anspråks leverantör innehåller en uppsättning [tekniska profiler](technicalprofiles.md). Varje anspråks leverantör måste ha en eller flera tekniska profiler som avgör slut punkterna och de protokoll som behövs för att kommunicera med anspråks leverantören. En anspråks leverantör kan ha flera tekniska profiler. Till exempel kan flera tekniska profiler definieras eftersom anspråks leverantören stöder flera protokoll, olika slut punkter med olika funktioner eller släpper olika anspråk på olika garanti nivåer. Det kan vara acceptabelt att släppa känsliga anspråk i en användar resa, men inte i en annan.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | En ackrediterad anspråks leverantör som kan utnyttjas i olika användar resor. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** -elementet innehåller följande underordnade element:

| Element | Förekomster | Beskrivning |
| ------- | ---------- | ----------- |
| Domain | 0:1 | En sträng som innehåller domän namnet för anspråks leverantören. Om din anspråks leverantör till exempel innehåller den tekniska Facebook-profilen för Facebook, är domän namnet Facebook.com. Det här domän namnet används för alla tekniska profiler som definierats i anspråks leverantören, såvida de inte åsidosätts av den tekniska profilen. Domän namnet kan också refereras till i en **domain_hint**. Mer information finns i avsnittet **omdirigera inloggning till en socialt leverantör** i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | En sträng som innehåller namnet på anspråks leverantören. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | En uppsättning tekniska profiler som stöds av anspråks leverantören |

**ClaimsProvider** ordnar hur dina tekniska profiler är relaterade till anspråks leverantören. I följande exempel visas providern Azure Active Directory anspråk med Azure Active Directory tekniska profiler:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

I följande exempel visas Facebook-anspråks leverantören med den tekniska profilen för **Facebook-OAuth** .

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
