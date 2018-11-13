---
title: TechnicalProfiles | Microsoft Docs
description: Ange det TechnicalProfiles elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 45ea2a28b4b2fb3d55d7ae949152e6f51b5d3162
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566541"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En **TechnicalProfiles** elementet innehåller en uppsättning tekniska profiler som stöds av anspråk providern. Varje anspråksprovidern måste ha en eller flera tekniska profiler som bestämmer slutpunkterna och protokoll som behövs för att kommunicera med anspråksprovidern. En anspråksprovider kan ha flera tekniska profiler.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Den **TechnicalProfile** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------|---------|---------|
| Id | Ja | En unik identifierare för den tekniska profilen. Den tekniska profilen kan refereras med följande identifierare från andra element i policyfilen. Till exempel **OrchestrationSteps** och **ValidationTechnicalProfile**. |

Den **TechnicalProfile** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Domännamnet för den tekniska profilen. Om dina tekniska profilen anger identitetsprovider Facebook, är domännamnet Facebook.com. |
| DisplayName | 0:1 | Namnet på den tekniska profilen som kan visas för användarna. |
| Beskrivning | 0:1 | Beskrivning av den tekniska profilen som kan visas för användarna. |
| Protokoll | 0:1 | Det protokoll som används för kommunikation med den andra parten. |
| Metadata | 0:1 | En samling nyckel/värde-par som används av protokollet för att kommunicera med slutpunkten under en transaktion. |
| InputTokenFormat | 0:1 | Formatet för den inkommande token. Möjliga värden: `JSON`, `JWT`, `SAML11`, eller `SAML2`. Den `JWT` värde representerar en JSON Web Token enligt IETF-specifikationen. Den `SAML11` värde representerar en säkerhetstoken för SAML 1.1 enligt OASIS-specifikationen.  Den `SAML2` värde representerar en SAML 2.0-säkerhetstoken enligt OASIS-specifikationen. |
| OutputTokenFormat | 0:1 | Formatet för utdata-token. Möjliga värden: `JSON`, `JWT`, `SAML11`, eller `SAML2`. |
| CryptographicKeys | 0:1 | En lista över kryptografiska nycklar som används i den tekniska profilen. |
| InputClaimsTransformations | 0:1 | En lista över tidigare definierad referenser till anspråksomvandlingar som ska köras innan några anspråk skickas till anspråksleverantören eller den förlitande parten. |
| InputClaims | 0:1 | En lista över tidigare definierad referenser till anspråkstyper som används som indata i den tekniska profilen. |
| PersistedClaims | 0:1 | En lista över tidigare definierad referenser till anspråkstyper som sparas av anspråksleverantören som är kopplat till den tekniska profilen. |
| OutputClaims | 0:1 | En lista över tidigare definierad referenser till anspråkstyper som vidtas som utdata i den tekniska profilen. |
| OutputClaimsTransformations | 0:1 | En lista över tidigare definierad referenser till anspråksomvandlingar som ska köras när anspråk som tas emot från anspråksleverantören. |
| ValidationTechnicalProfiles | 0: n | En lista över referenser till andra tekniska profiler som den tekniska profilen används för verifiering. Mer information finns i [tekniska profil](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Styr produktionen av ämnesnamnet i tokens där ämnesnamnet anges separat från anspråk. Till exempel OAuth eller SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | En identifierare för en tekniska profilen som du vill att alla inkommande och utgående anspråk som ska läggas till den här tekniska profilen. Den refererade tekniska profilen måste definieras i samma principfil. | 
| IncludeTechnicalProfile |0:1 | En identifierare för en tekniska profilen som du vill att alla data som ska läggas till den här tekniska profilen. Den refererade tekniska profilen måste finnas i samma principfil. |
| UseTechnicalProfileForSessionManagement | 0:1 | En annan tekniska profil som ska användas för sessionshantering. |
|EnabledForUserJourneys| 0:1 |Kontroller om den tekniska profilen körs i en användarresa.  |

### <a name="protocol"></a>Protokoll

Den **protokollet** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C som används som en del av den tekniska profilen. Möjliga värden: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted`, eller `None`. |
| Hanterare | Nej | När protokollnamnet är inställd på `Proprietary`, ange det fullständigt kvalificerade namnet på sammansättningen som används av Azure AD B2C för att fastställa protokollhanteraren. |

### <a name="metadata"></a>Metadata

En **Metadata** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | De metadata som relaterar till den tekniska profilen. Varje typ av tekniska profilen har en annan uppsättning metadata-objekt. Se avsnittet tekniska profil-typer för mer information. |

#### <a name="item"></a>Objekt

Den **objekt** elementet i den **Metadata** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Metadatanyckel. Se varje typ av tekniska profilen måste lista över metadata. |

### <a name="cryptographickeys"></a>CryptographicKeys

Den **CryptographicKeys** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Nyckel | 1: n | En kryptografisk nyckel som används i den här tekniska profilen. |

#### <a name="key"></a>Nyckel

Den **nyckel** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Nej | En unik identifierare för en viss nyckel som refereras till från andra element i policyfilen. |
| StorageReferenceId | Ja | En identifierare för en behållare för lagring som refereras till från andra element i policyfilen. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

Den **InputClaimsTransformations** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifierare för en omvandling av anspråk som ska köras innan några anspråk skickas till anspråksleverantören eller den förlitande parten. En anspråkstransformering kan användas för att ändra befintliga ClaimsSchema anspråk eller skapa nya. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Den **InputClaimsTransformation** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en anspråkstransformering som redan har definierats i principfil eller överordnade principfil. |

### <a name="inputclaims"></a>InputClaims

Den **InputClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Förväntade indata Anspråkstypen. |

#### <a name="inputclaim"></a>InputClaim 

Den **InputClaim** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifierare för en Anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfil eller överordnade principfil. |
| Standardvärde | Nej | Ett standardvärde för att skapa ett anspråk om anspråket indikeras av ClaimTypeReferenceId finns inte, så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Nej | Identifierare för den externa partner att den angivna principen Anspråkstypen Anspråkstypen mappas till. Om attributet PartnerClaimType inte anges är den angivna typen av anspråk mappad till Anspråkstypen partner med samma namn. Använd den här egenskapen när din anspråk namn skiljer sig från den andra parten. Det första anspråkets namnet är till exempel givenName, medan partnern som använder ett anspråk med namnet ”förnamn”. |

### <a name="persistedclaims"></a>PersistedClaims

Den **PersistedClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Anspråkstyp ska sparas. |

#### <a name="persistedclaim"></a>PersistedClaim 

Den **PersistedClaim** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifierare för en Anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfil eller överordnade principfil. |
| Standardvärde | Nej | Ett standardvärde för att skapa ett anspråk om anspråket indikeras av ClaimTypeReferenceId finns inte, så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Nej | Identifierare för den externa partner att den angivna principen Anspråkstypen Anspråkstypen mappas till. Om attributet PartnerClaimType inte anges är den angivna typen av anspråk mappad till Anspråkstypen partner med samma namn. Använd den här egenskapen när din anspråk namn skiljer sig från den andra parten. Det första anspråkets namnet är till exempel givenName, medan partnern som använder ett anspråk med namnet ”förnamn”. |

### <a name="outputclaims"></a>OutputClaims

Den **OutputClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| outputClaim | 1: n | Förväntad utdata Anspråkstypen. |

#### <a name="outputclaim"></a>outputClaim 

Den **OutputClaim** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifierare för en Anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfil eller överordnade principfil. |
| Standardvärde | Nej | Ett standardvärde för att skapa ett anspråk om anspråket indikeras av ClaimTypeReferenceId finns inte, så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
|AlwaysUseDefaultValue |Nej |Framtvinga användningen av standardvärdet.  |
| PartnerClaimType | Nej | Identifierare för den externa partner att den angivna principen Anspråkstypen Anspråkstypen mappas till. Om attributet PartnerClaimType inte anges är den angivna typen av anspråk mappad till Anspråkstypen partner med samma namn. Använd den här egenskapen när din anspråk namn skiljer sig från den andra parten. Det första anspråkets namnet är till exempel givenName, medan partnern som använder ett anspråk med namnet ”förnamn”. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Den **OutputClaimsTransformations** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifierare för anspråksomvandlingar som ska köras innan några anspråk skickas till anspråksleverantören eller den förlitande parten. En anspråkstransformering kan användas för att ändra befintliga ClaimsSchema anspråk eller skapa nya. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Den **OutputClaimsTransformation** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en anspråkstransformering som redan har definierats i principfil eller överordnade principfil. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Den **ValidationTechnicalProfiles** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifierare för tekniska profiler som används för att verifiera några eller alla utgående anspråk på den refererande tekniska profilen. Alla inkommande anspråk för den refererade tekniska profilen måste visas i utdataanspråk för den refererande tekniska profilen. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Den **ValidationTechnicalProfile** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en tekniska profilen som redan har definierats i principfil eller överordnade principfil. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

Den **SubjectNamingInfo** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType | Ja | En identifierare för en Anspråkstyp som redan har definierats i ClaimsSchema-avsnittet i principen. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Den **IncludeTechnicalProfile** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en tekniska profilen som redan har definierats i principfil eller överordnade principfil. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Den **UseTechnicalProfileForSessionManagement** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en tekniska profilen som redan har definierats i principfil eller överordnade principfil. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
Den **ClaimsProviderSelections** i en användare resa definierar en lista med alternativ för val av anspråk providern och deras inbördes ordning. Med den **EnabledForUserJourneys** element som du filtrera vilka anspråksprovidern är tillgängligt för användaren. Den **EnabledForUserJourneys** elementet innehåller något av följande värden:

- **Alltid**, köra den tekniska profilen.
- **Aldrig**, hoppa över den tekniska profilen. 
- **OnClaimsExistence** köra endast när ett visst krav som anges i den tekniska profilen finns. 
- **OnItemExistenceInStringCollectionClaim**, köra endast när det finns ett objekt i ett anspråk för insamling av strängen. 
- **OnItemAbsenceInStringCollectionClaim** köra endast när ett objekt finns inte i ett anspråk för insamling av strängen.

Med hjälp av **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** eller **OnItemAbsenceInStringCollectionClaim**, måste du ange följande metadata: **ClaimTypeOnWhichToEnable** anger Anspråkstyp som ska utvärderas, **ClaimValueOnWhichToEnable** anger det värde som ska jämföras.

Följande tekniska profil körs bara om den **identityProviders** sträng samlingen innehåller värdet för `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
        <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
        <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>        
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>  
```












