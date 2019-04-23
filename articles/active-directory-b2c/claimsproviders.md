---
title: ClaimsProviders - Azure Active Directory B2C | Microsoft Docs
description: Ange det ClaimsProvider elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d862d07a65c3fb28b49a82692ea575f787b9750
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008757"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anspråksprovider innehåller en uppsättning [tekniska profiler](technicalprofiles.md). Varje anspråksprovidern måste ha en eller flera tekniska profiler som bestämmer slutpunkterna och protokoll som behövs för att kommunicera med anspråksprovidern. En anspråksprovider kan ha flera tekniska profiler. Flera tekniska profiler kan exempelvis definieras eftersom anspråksleverantören har stöd för flera protokoll, olika slutpunkter med olika funktioner eller frigör olika krav på olika assurance nivåer. Det kan vara att frigöra känsliga anspråk i en användarresa, men inte i en annan.

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

Den **ClaimsProviders** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | En auktoriserad anspråksleverantören som kan användas i olika användare resor. |

## <a name="claimsprovider"></a>ClaimsProvider

Den **ClaimsProvider** elementet innehåller följande underordnade element:

| Element | Förekomster | Beskrivning |
| ------- | ---------- | ----------- |
| Domain | 0:1 | En sträng som innehåller domännamnet för den anspråk providern. Om din anspråksprovider innehåller den tekniska profilen Facebook, är domännamnet Facebook.com. Det här domännamnet används för alla tekniska profiler som definierats i anspråksleverantören såvida inte åsidosätts av den tekniska profilen. Domännamnet kan också refereras i ett **domain_hint**. Mer information finns i den **omdirigera logga in till en leverantör av sociala** delen av [konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 0:1 | En sträng som innehåller namnet på anspråksprovidern som kan visas för användarna. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | En uppsättning tekniska profiler som stöds av anspråk providern |

**ClaimsProvider** organiserar hur dina tekniska profiler som är relaterade till anspråksprovidern. I följande exempel visas anspråksprovider för Azure Active Directory med Azure Active Directory tekniska profiler:

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

I följande exempel visas Facebook anspråksprovidern med den **Facebook-OAUTH** tekniska profilen.

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
