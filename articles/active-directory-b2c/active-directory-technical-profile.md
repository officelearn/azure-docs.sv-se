---
title: Definiera en teknisk Azure AD-profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk Azure Active Directory-profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330375"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk Azure Active Directory-profil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för Azure Active Directory-användarhantering. I den här artikeln beskrivs detaljerna för en teknisk profil för att interagera med en anspråksprovider som stöder det här standardiserade protokollet.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`namnet på protokollhanterarsammansättningen .

Följande [anpassade principstartpaket](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD tekniska profiler inkluderar **den tekniska profilen AAD-Common.** De tekniska profilerna för Azure AD anger inte protokollet eftersom protokollet är konfigurerat i den tekniska profilen **AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingAlternativeSecurityId-NoError** - Slå upp ett socialt konto i katalogen.
- **AAD-UserWriteAnvändareAlternativeSecurityId** - Skapa ett nytt socialt konto.
- **AAD-UserReadUsingEmailAddress** - Slå upp ett lokalt konto i katalogen.
- **AAD-UserWriteUsingLogonEmail** - Skapa ett nytt lokalt konto.
- **AAD-UserWritePasswordUsingObjectId** - Uppdatera ett lösenord för ett lokalt konto.
- **AAD-UserWriteProfileUsingObjectId** - Uppdatera en användarprofil för ett lokalt eller socialt konto.
- **AAD-UserReadUsingObjectId** - Läs en användarprofil för ett lokalt eller socialt konto.
- **AAD-UserWritePhoneNumberUsingObjectId** - Skriv MFA-telefonnumret till ett lokalt eller socialt konto

I följande exempel visas den tekniska profilen **AAD-Common:**

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

## <a name="inputclaims"></a>InputClaims

Elementet InputClaims innehåller ett anspråk som används för att slå upp ett konto i katalogen eller skapa ett nytt. Det måste finnas exakt ett InputClaim-element i insamlingen av indataanspråk för alla tekniska Azure AD-profiler. Du kan behöva mappa namnet på anspråket som definierats i din princip till det namn som definierats i Azure Active Directory.

Om du vill läsa, uppdatera eller ta bort ett befintligt användarkonto är indataanspråket en nyckel som unikt identifierar kontot i Azure AD-katalogen. Till exempel **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**eller **alternativeSecurityId**. 

Om du vill skapa ett nytt användarkonto är indataanspråket en nyckel som unikt identifierar ett lokalt eller federerat konto. Lokalt konto: **signInNames.emailAddress**eller **signInNames.userName**. För ett federerat konto: **alternativeSecurityId**.

Elementet [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) kan innehålla en samling indataanaleringselement som används för att ändra indataanspråket eller generera nya.

## <a name="outputclaims"></a>OutputClaims

**Elementet OutputClaims** innehåller en lista över anspråk som returneras av den tekniska profilen för Azure AD. Du kan behöva mappa namnet på anspråket som definierats i din princip till det namn som definierats i Azure Active Directory. Du kan också inkludera anspråk som inte returneras av Azure Active `DefaultValue` Directory, så länge du anger attributet.

[Elementet OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

Den tekniska profilen **AAD-UserWriteUsingLogonEmail** skapar till exempel ett lokalt konto och returnerar följande anspråk:

- **objectId**, som är identifierare för det nya kontot
- **newUser**, som anger om användaren är ny
- **authenticationSource**, som anger autentisering till`localAccountAuthentication`
- **userPrincipalName**, som är användarens huvudnamn för det nya kontot
- **signInNames.emailAddress**, som är kontots inloggningsnamn, liknar **anspråket för e-postinmatning**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>Beständiga Åtaganden

Det **Beständiga Claims-elementet** innehåller alla värden som ska sparas av Azure AD med möjlig mappningsinformation mellan en anspråkstyp som redan definierats i avsnittet [ClaimsSchema](claimsschema.md) i principen och Azure AD-attributnamnet.

Den tekniska profilen **AAD-UserWriteUsingLogonEmail,** som skapar ett nytt lokalt konto, kvarstår efter anspråk:

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

Namnet på anspråket är namnet på Azure AD-attributet om inte **Attributet PartnerClaimType** har angetts, som innehåller Azure AD-attributnamnet.

## <a name="requirements-of-an-operation"></a>Krav för en operation

- Det måste finnas exakt ett **InputClaim-element** i anspråkspåsen för alla azure AD-tekniska profiler.
- I [artikeln användarprofilattribut](user-profile-attributes.md) beskrivs de azure AD B2C-användarprofilattribut som stöds som du kan använda i indataanspråk, utdataanspråk och beständiga anspråk. 
- Om åtgärden `Write` är `DeleteClaims`eller måste den också visas i ett **PersistedClaims-element.**
- Värdet för **anspråket userPrincipalName** måste vara `user@tenant.onmicrosoft.com`i formatet .
- **Anspråket displayName** krävs och kan inte vara en tom sträng.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD-tekniska provideråtgärder

### <a name="read"></a>Läsa

**Åtgärden Läs** läser data om ett enda användarkonto. Följande tekniska profil läser data om ett användarkonto med användarens objectId:

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

**Skrivåtgärden** skapar eller uppdaterar ett enda användarkonto. Följande tekniska profil skapar nya sociala konton:

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

### <a name="deleteclaims"></a>Ta bortkrav

**Åtgärden DeleteClaims** rensar informationen från en lista över anspråk. Följande tekniska profil tar bort anspråk:

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

Åtgärden **DeleteClaimsPrincipal** tar bort ett enda användarkonto från katalogen. Följande tekniska profil tar bort ett användarkonto från katalogen med användarens huvudnamn:

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

Följande tekniska profil tar bort ett socialt användarkonto med **alternativeSecurityId:**

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
| Åtgärd | Ja | Den åtgärd som ska utföras. Möjliga `Read`värden: `Write` `DeleteClaims`, `DeleteClaimsPrincipal`, eller . |
| RaiseErrorIfClaimsPrincipalDoesExist | Inga | Skapa ett fel om användarobjektet inte finns i katalogen. Möjliga `true` värden: `false`eller . |
| RaiseErrorIfClaimsPrincipalturnorExisterar | Inga | Skapa ett fel om användarobjektet redan finns. Möjliga `true` värden: `false`eller .|
| ApplicationObjectId | Inga | Programobjektidentifieraren för tilläggsattribut. Värde: ObjectId för ett program. Mer information finns [i Använda anpassade attribut i en anpassad profilredigeringsprincip](custom-policy-custom-attributes.md). |
| ClientId | Inga | Klientidentifieraren för åtkomst till klienten som tredje part. Mer information finns [i Använda anpassade attribut i en anpassad profilredigeringsprincip](custom-policy-custom-attributes.md) |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |

### <a name="ui-elements"></a>Element för användargränssnitt
 
Följande inställningar kan användas för att konfigurera felmeddelandet som visas vid fel. Metadata bör konfigureras i den [självsäkra](self-asserted-technical-profile.md) tekniska profilen. Felmeddelandena kan [lokaliseras](localization.md).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalturenExister | Inga | Om ett fel ska höjas (se Attributebeskrivningen RaiseErrorIfClaimsPrincipalreadyExists) anger du det meddelande som ska visas för användaren om användarobjektet redan finns. |
| UserMessageIfClaimsPrincipalDoesExist | Inga | Om ett fel ska höjas (se attributbeskrivningen RaiseErrorIfClaimsPrincipalDoesNotExist) anger du det meddelande som ska visas för användaren om användarobjektet inte finns. |


## <a name="next-steps"></a>Nästa steg

Se följande artikel, till exempel om du använder teknisk profil i Azure AD:

- [Lägga till anspråk och anpassa användarindata med hjälp av anpassade principer i Azure Active Directory B2C](custom-policy-configure-user-input.md)














