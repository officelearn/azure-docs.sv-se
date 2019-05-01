---
title: Definiera en Azure Active Directory-tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en Azure Active Directory-tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 433307791201e3799b3bc9e54aec765d9fbeb4af
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718699"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en Azure Active Directory-tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för hantering för Azure Active Directory-användare. Den här artikeln beskriver specifika för den tekniska profilen för att interagera med en anspråksprovider som stöder detta standardiserade protokoll.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `Proprietary`. Den **hanteraren** attributet måste innehålla det fullständigt kvalificerade namnet för protokollet hanteraren sammansättningen `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Tekniska profiler för alla Azure AD innehåller den **AAD-gemensamma** tekniska profilen. Följande tekniska profiler inte ange protokollet eftersom protokollet som har konfigurerats i den **AAD-gemensamma** tekniska profil:

- **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingAlternativeSecurityId-NoError** – Leta upp upp ett socialt konto i katalogen.
- **AAD-UserWriteUsingAlternativeSecurityId** -skapa ett nytt sociala konto.
- **AAD-UserReadUsingEmailAddress** – Leta upp upp ett lokalt konto i katalogen. 
- **AAD-UserWriteUsingLogonEmail** -skapa ett nytt lokalt konto.
- **AAD-UserWritePasswordUsingObjectId** – uppdatera ett lösenord för ett lokalt konto.
- **AAD-UserWriteProfileUsingObjectId** – uppdatera en användarprofil för en lokal eller sociala kontot.
- **AAD-UserReadUsingObjectId** -läsa en profil för en lokal eller sociala kontot.
- **AAD-UserWritePhoneNumberUsingObjectId** -skriva telefonnummer för MFA för en lokal eller sociala konto

I följande exempel visas den **AAD-gemensamma** tekniska profil:

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

## <a name="input-claims"></a>Inkommande anspråk

Följande tekniska profiler innehåller **InputClaims** för sociala och lokala konton:

- Socialt konto tekniska profiler **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserWriteUsingAlternativeSecurityId** innehåller den **AlternativeSecurityId** anspråk. Det här anspråket innehåller användaridentifierare sociala kontot.
- Lokalt konto tekniska profiler **AAD-UserReadUsingEmailAddress** och **AAD-UserWriteUsingLogonEmail** innehåller den **e-post** anspråk. Det här anspråket innehåller namnet logga in på det lokala kontot.
- Enhetlig (lokal och sociala) tekniska profiler **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**, och **AAD-UserWritePhoneNumberUsingObjectId** innehåller den **objectId** anspråk. Den unika identifieraren för ett konto.

Den **InputClaimsTransformations** element kan innehålla en uppsättning **InputClaimsTransformation** element som används för att ändra de inkommande anspråken eller skapa nya.

## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** elementet innehåller en lista över anspråk som returneras av den tekniska profilen för Azure AD. Du kan behöva matchar namnet på anspråk som definierats i din princip att namnet som definierats i Azure Active Directory. Du kan även inkludera anspråk som inte returneras av Azure Active Directory, så länge som du ställer in den `DefaultValue` attribut.

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

Till exempel den **AAD-UserWriteUsingLogonEmail** tekniska profilen skapar ett lokalt konto och returnerar följande anspråk:

- **objectId**, vilket är identifierare för det nya kontot
- **ny användare**, vilket anger om användaren är ny
- **authenticationSource**, som anger autentisering till `localAccountAuthentication`
- **userPrincipalName**, vilket är användarens huvudnamn på det nya kontot
- **signInNames.emailAddress**, vilket är namnet på kontot loggar in, liknar den **e-post** inkommande anspråk

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

Den **PersistedClaims** elementet innehåller alla värden som ska sparas av Azure AD med möjliga mappningsinformation mellan en Anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principen och attributet Azure AD Namn. 

Den **AAD-UserWriteUsingLogonEmail** tekniska profil, vilket skapar nya lokalt konto, kvarstår efter anspråk:

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

Anspråkets namn är namnet på attributet Azure AD, såvida inte den **PartnerClaimType** attributet anges, som innehåller namnet på Azure AD-attributet.

## <a name="requirements-of-an-operation"></a>Krav för en åtgärd

- Det måste finnas exakt ett **InputClaim** element i uppsättningen anspråk för alla Azure AD tekniska profiler. 
- Om åtgärden `Write` eller `DeleteClaims`, och det måste också finnas i en **PersistedClaims** element.
- Värdet för den **userPrincipalName** anspråk måste vara i formatet `user@tenant.onmicrosoft.com`.
- Den **displayName** anspråk är obligatoriskt och kan inte vara en tom sträng.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD teknisk provideråtgärder

### <a name="read"></a>Läsa

Den **Läs** åtgärden läser data om ett enda användarkonto. Om du vill läsa användardata, måste du ange en nyckel som ett inkommande anspråk som **objectId**, **userPrincipalName**, **signInNames** (valfri typ, användarnamnet och e-postbaserad konto) eller **alternativeSecurityId**.  

Följande tekniska profil läser data om ett användarkonto med hjälp av användarens objekt-ID:

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

Den **skriva** åtgärden skapar eller uppdaterar ett enda användarkonto. Om du vill skriva ett användarkonto, måste du ange en nyckel som ett inkommande anspråk som **objectId**, **userPrincipalName**, **signInNames.emailAddress**, eller  **alternativeSecurityId**.  

Följande tekniska profil skapas nya socialt konto:

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

Den **DeleteClaims** åtgärden tar bort information från en angiven lista i anspråk. Om du vill ta bort information från anspråk måste du ange en nyckel som ett inkommande anspråk som **objectId**, **userPrincipalName**, **signInNames.emailAddress** eller **alternativeSecurityId**.  

Följande tekniska profilen tar bort anspråk:

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

Den **DeleteClaimsPrincipal** ett enda användarkonto tas bort från katalogen. Om du vill ta bort ett användarkonto, måste du ange en nyckel som ett inkommande anspråk som **objectId**, **userPrincipalName**, **signInNames.emailAddress** eller  **alternativeSecurityId**.  

Följande tekniska profilen tar bort ett användarkonto från katalogen med hjälp av användarens huvudnamn:

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

Följande tekniska profilen tar bort en social användarkonto med **alternativeSecurityId**:

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
| Åtgärd | Ja | Åtgärden som ska utföras. Möjliga värden: `Read`, `Write`, `DeleteClaims`, eller `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nej | Returnerar ett fel om användarobjektet inte finns i katalogen. Möjliga värden: `true` eller `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Nej | Om ett fel är att höjas (se Attributbeskrivning RaiseErrorIfClaimsPrincipalDoesNotExist), ange ska visas för användaren om användarobjektet inte finns. Värdet kan vara [lokaliserade](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nej | Returnerar ett fel om användarobjektet finns redan. Möjliga värden: `true` eller `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Nej | Om ett fel är att höjas (se RaiseErrorIfClaimsPrincipalAlreadyExists Attributbeskrivning), ange ska visas för användaren om användarobjektet finns redan. Värdet kan vara [lokaliserade](localization.md).| 
| ApplicationObjectId | Nej | Program-objektidentifierare för tilläggsattribut. Värde: ObjectId för ett program. Mer information finns i [Använd anpassade attribut i en anpassad profil redigera principen](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Nej | Klient-ID för att få åtkomst till klientorganisationen som en tredje part. Mer information finns i [Använd anpassade attribut i en anpassad profil Redigera princip](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 














