---
title: ClaimsProviders - Azure Active Directory B2C | Microsoft-dokument
description: Ange elementet ClaimsProvider i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189777"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anspråksprovider innehåller en uppsättning [tekniska profiler](technicalprofiles.md). Varje anspråksprovider måste ha en eller flera tekniska profiler som avgör slutpunkterna och protokollen som behövs för att kommunicera med anspråksprovidern. En anspråksprovider kan ha flera tekniska profiler. Flera tekniska profiler kan till exempel definieras eftersom anspråksprovidern stöder flera protokoll, olika slutpunkter med olika funktioner eller släpper olika anspråk på olika säkerhetsnivåer. Det kan vara acceptabelt att släppa känsliga anspråk i en användarresa, men inte i en annan.

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

Elementet **ClaimsProviders** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| FordringarProvider | 1:n | En ackrediterad anspråksleverantör som kan utnyttjas i olika användarresor. |

## <a name="claimsprovider"></a>FordringarProvider

Elementet **ClaimsProvider** innehåller följande underordnade element:

| Element | Händelser | Beskrivning |
| ------- | ---------- | ----------- |
| Domain | 0:1 | En sträng som innehåller domännamnet för anspråksprovidern. Om din skadeleverantör till exempel innehåller Den tekniska profilen för Facebook är domännamnet Facebook.com. Detta domännamn används för alla tekniska profiler som definieras i anspråksprovidern om det inte åsidosätts av den tekniska profilen. Domännamnet kan också refereras i en **domain_hint**. Mer information finns **i avsnittet Omdirigera inloggning till en social provider i** Konfigurera direkt inloggning med Azure Active Directory [B2C](direct-signin.md). |
| DisplayName | 1:1 | En sträng som innehåller namnet på anspråksprovidern. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | En uppsättning tekniska profiler som stöds av anspråksleverantören |

**ClaimsProvider** organiserar hur dina tekniska profiler relaterar till anspråksleverantören. I följande exempel visas Azure Active Directory-anspråksprovidern med tekniska Azure Active Directory-profiler:

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

I följande exempel visas Facebook-anspråksleverantören med den tekniska profilen **Facebook-OAUTH.**

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
