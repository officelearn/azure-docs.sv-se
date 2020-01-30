---
title: Definiera en teknisk Azure AD-profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en Azure Active Directory teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e27288f95f07c481ab98a112ed9f02a34046600a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835801"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en Azure Active Directory teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för Azure Active Directory användar hantering. I den här artikeln beskrivs de olika kraven för en teknisk profil för att interagera med en anspråks leverantör som stöder detta standardiserade protokoll.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet för protokoll hanterarens sammansättning `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Alla tekniska profiler för Azure AD inkluderar **AAD-vanliga** tekniska profiler. Följande tekniska profiler anger inte protokollet eftersom protokollet har kon figurer ATS i **AAD-common** Technical Profile:

- **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingAlternativeSecurityId-noerror** – slå upp ett socialt konto i katalogen.
- **AAD-UserWriteUsingAlternativeSecurityId** – skapa ett nytt socialt konto.
- **AAD-UserReadUsingEmailAddress** – leta upp ett lokalt konto i katalogen.
- **AAD-UserWriteUsingLogonEmail** – skapa ett nytt lokalt konto.
- **AAD-UserWritePasswordUsingObjectId** – uppdatera ett lösen ord för ett lokalt konto.
- **AAD-UserWriteProfileUsingObjectId** – uppdatera en användar profil för ett lokalt eller socialt konto.
- **AAD-UserReadUsingObjectId** – Läs en användar profil för ett lokalt eller socialt konto.
- **AAD-UserWritePhoneNumberUsingObjectId** – Skriv MFA-telefonnumret för ett lokalt eller socialt konto

I följande exempel visas **AAD – vanlig** teknisk profil:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Inmatade anspråk

Följande tekniska profiler innehåller **InputClaims** för sociala och lokala konton:

- De tekniska profilerna för sociala konton **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserWriteUsingAlternativeSecurityId** innehåller **AlternativeSecurityId** -anspråket. Detta påstående innehåller användar identifieraren för sociala kontot.
- De lokala kontots tekniska profiler **AAD-UserReadUsingEmailAddress** och **AAD-UserWriteUsingLogonEmail** innehåller **e-** postanspråket. Detta anspråk innehåller inloggnings namnet för det lokala kontot.
- De enhetliga (lokala och sociala) tekniska profilerna **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**och **AAD-UserWritePhoneNumberUsingObjectId** innehåller **ObjectID** -anspråket. Unikt ID för ett konto.

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller skapa nya.

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av den tekniska Azure AD-profilen. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i Azure Active Directory. Du kan också inkludera anspråk som inte returneras av Azure Active Directory, förutsatt att du anger attributet `DefaultValue`.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

Till exempel skapar den tekniska profilen **AAD-UserWriteUsingLogonEmail** ett lokalt konto och returnerar följande anspråk:

- **ObjectID**, som är identifierare för det nya kontot
- **newUser**, som anger om användaren är ny
- **authenticationSource**, som anger autentiseringen till `localAccountAuthentication`
- **userPrincipalName**, som är User Principal Name för det nya kontot
- **signInNames. EmailAddress**, som är kontots inloggnings namn, på liknande sätt som **e-** postanspråket

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** -elementet innehåller alla värden som ska sparas av Azure AD med möjlig mappnings information mellan en anspråks typ som redan har definierats i ClaimsSchema-avsnittet i principen och namnet på Azure AD-attributnamnet.

Den tekniska profilen **AAD-UserWriteUsingLogonEmail** , som skapar ett nytt lokalt konto, behåller följande anspråk:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Namnet på anspråket är namnet på Azure AD-attributet om inte attributet **PartnerClaimType** anges, som innehåller namnet på Azure AD-attributet.

## <a name="requirements-of-an-operation"></a>Krav för en åtgärd

- Det måste finnas exakt ett **InputClaim** -element i anspråks säcken för alla Azure AD-tekniska profiler.
- Om åtgärden är `Write` eller `DeleteClaims`måste den också visas i ett **PersistedClaims** -element.
- Värdet för **userPrincipalName** -anspråket måste vara i formatet `user@tenant.onmicrosoft.com`.
- Detta **DisplayName** -anspråk krävs och kan inte vara en tom sträng.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD Technical Provider-åtgärder

### <a name="read"></a>Läsa

**Läs** åtgärden läser data om ett enda användar konto. Om du vill läsa användar data måste du ange en nyckel som ett indata-anspråk, till exempel **ObjectID**, **userPrincipalName**, **signInNames** (alla typer, användar namn och e-postbaserat konto) eller **alternativeSecurityId**.

Följande tekniska profil läser data om ett användar konto med hjälp av användarens objectId:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Skriva

**Skriv** åtgärden skapar eller uppdaterar ett enskilt användar konto. Om du vill skriva ett användar konto måste du ange en nyckel som ett indata-anspråk, till exempel **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress**eller **alternativeSecurityId**.

Följande tekniska profil skapar ett nytt socialt konto:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** -åtgärden rensar informationen från en angiven lista över anspråk. Om du vill ta bort information från anspråk måste du ange en nyckel som ett indata-anspråk, till exempel **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress** eller **alternativeSecurityId**.

Följande tekniska profil tar bort anspråk:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Åtgärden **DeleteClaimsPrincipal** tar bort ett enskilt användar konto från katalogen. Om du vill ta bort ett användar konto måste du ange en nyckel som ett indata-anspråk, till exempel **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress** eller **alternativeSecurityId**.

Följande tekniska profil tar bort ett användar konto från katalogen med hjälp av User Principal Name:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Följande tekniska profil tar bort ett socialt användar konto med **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Åtgärden som ska utföras. Möjliga värden: `Read`, `Write`, `DeleteClaims`eller `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Inga | Generera ett fel om objektet användare inte finns i katalogen. Möjliga värden: `true` eller `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Inga | Om ett fel ska höjas (se beskrivningen av RaiseErrorIfClaimsPrincipalDoesNotExist) anger du det meddelande som ska visas för användaren om användar objekt inte finns. Värdet kan [lokaliseras](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Inga | Generera ett fel om det redan finns ett användar objekt. Möjliga värden: `true` eller `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Inga | Om ett fel ska höjas (se RaiseErrorIfClaimsPrincipalAlreadyExists-Attribute Description) anger du det meddelande som ska visas för användaren om användar objekt redan finns. Värdet kan [lokaliseras](localization.md).|
| ApplicationObjectId | Inga | Programobjekts-ID för tilläggets attribut. Värde: ObjectId för ett program. Mer information finns i [använda anpassade attribut i en anpassad profil redigerings princip](custom-policy-custom-attributes.md). |
| ClientId | Inga | Klient identifieraren för åtkomst till klienten som en tredje part. Mer information finns i [använda anpassade attribut i en anpassad profil redigerings princip](custom-policy-custom-attributes.md) |














