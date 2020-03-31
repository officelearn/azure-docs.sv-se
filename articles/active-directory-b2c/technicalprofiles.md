---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Ange elementet TechnicalProfiles i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264315"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ett **TechnicalProfiles-element** innehåller en uppsättning tekniska profiler som stöds av anspråksprovidern. Varje anspråksprovider måste ha en eller flera tekniska profiler som avgör slutpunkterna och protokollen som behövs för att kommunicera med anspråksprovidern. En anspråksprovider kan ha flera tekniska profiler.

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

Elementet **TechnicalProfile** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------|---------|---------|
| Id | Ja | En unik identifierare för den tekniska profilen. Den tekniska profilen kan refereras med hjälp av den här identifieraren från andra element i principfilen. Till exempel **OrchestrationSteps** och **ValidationTechnicalProfile**. |

**TechnicalProfile** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Domännamnet för den tekniska profilen. Om din tekniska profil till exempel anger Facebook-identitetsleverantören är domännamnet Facebook.com. |
| DisplayName | 1:1 | Namnet på den tekniska profilen som kan visas för användarna. |
| Beskrivning | 0:1 | Beskrivningen av den tekniska profilen som kan visas för användarna. |
| Protokoll | 0:1 | Protokollet som används för kommunikationen med den andra parten. |
| Metadata | 0:1 | En samling nyckel-/värdepar som används av protokollet för att kommunicera med slutpunkten under en transaktion. |
| InputTokenFormat | 0:1 | Formatet på indatatoken. Möjliga `JSON`värden: `JWT` `SAML11`, `SAML2`, eller . Värdet `JWT` representerar en JSON-webbtoken enligt IETF-specifikationen. Värdet `SAML11` representerar en SAML 1.1-säkerhetstoken enligt OASIS-specifikationen.  Värdet `SAML2` representerar en SÄKERHETSTOKEN FÖR SAML 2.0 enligt OASIS-specifikationen. |
| OutputTokenFormat | 0:1 | Formatet på utdatatoken. Möjliga `JSON`värden: `JWT` `SAML11`, `SAML2`, eller . |
| Kryptografiska Tangenter | 0:1 | En lista över kryptografiska nycklar som används i den tekniska profilen. |
| InputClaimsTransformationer | 0:1 | En lista över tidigare definierade referenser till anspråksomvandlingar som ska utföras innan anspråk skickas till anspråksleverantören eller den förlitande parten. |
| InputClaims | 0:1 | En lista över tidigare definierade referenser till anspråkstyper som tas som indata i den tekniska profilen. |
| Beständiga Åtaganden | 0:1 | En lista över de tidigare definierade referenser till anspråkstyper som kvarstår av anspråksprovidern som relaterar till den tekniska profilen. |
| DisplayClaims | 0:1 | En lista över de tidigare definierade hänvisningarna till anspråkstyper som presenteras av anspråksleverantören som relaterar till den [självpåstämda tekniska profilen](self-asserted-technical-profile.md). DisplayClaims-funktionen är för närvarande i **förhandsgranskning**. |
| OutputClaims | 0:1 | En lista över tidigare definierade referenser till anspråkstyper som tas ut som utdata i den tekniska profilen. |
| OutputClaimsTransformationer | 0:1 | En lista över tidigare definierade referenser till anspråksomvandlingar som ska utföras efter att anspråken har mottagits från anspråksprovidern. |
| ValideringTekniskaProfiler | 0:n | En lista över referenser till andra tekniska profiler som den tekniska profilen använder för validering. Mer information finns i [teknisk profil för validering](validation-technical-profile.md)|
| ÄmneNamingInfo | 0:1 | Styr produktionen av ämnesnamnet i token där ämnesnamnet anges separat från anspråk. Till exempel OAuth eller SAML.  |
| IncludeInSso | 0:1 |  Om användningen av den här tekniska profilen ska använda SSO-beteende (Single Sign-on) för sessionen eller i stället kräva explicit interaktion. Det här elementet är endast giltigt i självskyddade profiler som används i en teknisk valideringsprofil. Möjliga värden: `true` (standard) eller `false`. |
| InkluderaClaimsFromTechnicalProfile | 0:1 | En identifierare för en teknisk profil som du vill att alla indata- och utdataanspråk ska läggas till i den här tekniska profilen. Den refererade tekniska profilen måste definieras i samma principfil. |
| Inkludera tekniskprofil |0:1 | En identifierare för en teknisk profil som du vill att alla data ska läggas till i den här tekniska profilen. |
| UseTechnicalProfileForSessionManagement | 0:1 | En annan teknisk profil som ska användas för sessionshantering. |
|AktiveradeFöranvändareJourer| 0:1 |Styr om den tekniska profilen körs i en användarresa.  |

## <a name="protocol"></a>Protokoll

**Protokollelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C och som används som en del av den tekniska profilen. `OAuth1`Möjliga värden: `OAuth2` `SAML2`, `OpenIdConnect` `Proprietary`, `None`, , , eller . |
| Hanterare | Inga | När protokollnamnet är `Proprietary`inställt på anger du det fullständigt kvalificerade namnet på sammansättningen som används av Azure AD B2C för att bestämma protokollhanteraren. |

## <a name="metadata"></a>Metadata

Ett **metadataelement** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0:n | Metadata som relaterar till den tekniska profilen. Varje typ av teknisk profil har olika metadataobjekt. Mer information finns i avsnittet tekniska profiltyper. |

### <a name="item"></a>Objekt

Elementet **Objekt** i **elementet Metadata** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Metadatanyckeln. Se varje teknisk profiltyp för listan över metadataobjekt. |

## <a name="cryptographickeys"></a>Kryptografiska Tangenter

Elementet **CryptographicKeys** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Nyckel | 1:n | En kryptografisk nyckel som används i den här tekniska profilen. |

### <a name="key"></a>Nyckel

**Nyckelelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Inga | En unik identifierare för ett visst nyckelpar som refereras från andra element i principfilen. |
| StorageReferenceId | Ja | En identifer av en lagringsnyckelbehållare som refereras från andra element i principfilen. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformationer

Elementet **InputClaimsTransformations** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | Identifieraren för en anspråksomvandling som ska utföras innan anspråk skickas till anspråksleverantören eller den förlitande parten. En anspråksomvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller generera nya. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**Elementet InputClaimsTransformation** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråksomvandling som redan definierats i principfilen eller den överordnade principfilen. |

## <a name="inputclaims"></a>InputClaims

**Elementet InputClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | En förväntad indataanspråkstyp. |

### <a name="inputclaim"></a>InputClaim

**Elementet InputClaim** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfilen eller den överordnade principfilen. |
| Standardvärde | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om anspråket som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Inga | Identifieraren för anspråkstypen för den externa partnern som den angivna principanspråkstypen mappar till. Om Attributet PartnerClaimType inte har angetts mappas den angivna principanspråkstypen till partneranspråkstypen med samma namn. Använd den här egenskapen när namnet på anspråkstypen skiljer sig från den andra parten. Namnet på det första anspråket är till exempel "givenName", medan partnern använder ett anspråk med namnet "first_name". |

## <a name="displayclaims"></a>DisplayClaims

**Elementet DisplayClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | En förväntad indataanspråkstyp. |

Funktionen DislayClaims är för närvarande i **förhandsversion**.

### <a name="displayclaim"></a>DisplayClaim

**Elementet DisplayClaim** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Inga | Identifieraren för en anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfilen eller den överordnade principfilen. |
| DisplayControlReferenceId | Inga | Identifieraren för en [visningskontroll](display-controls.md) som redan har definierats i avsnittet ClaimsSchema i principfilen eller den överordnade principfilen. |
| Krävs | Inga | Anger om visningsanspråket krävs. |

**DisplayClaim** kräver att du `ClaimTypeReferenceId` anger `DisplayControlReferenceId`antingen en eller .

### <a name="persistedclaims"></a>Beständiga Åtaganden

Elementet **PersistedClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| BeständigtClaim | 1:n | Anspråkstypen som ska bevaras. |

### <a name="persistedclaim"></a>BeständigtClaim

Elementet **PersistedClaim** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfilen eller den överordnade principfilen. |
| Standardvärde | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om anspråket som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Inga | Identifieraren för anspråkstypen för den externa partnern som den angivna principanspråkstypen mappar till. Om Attributet PartnerClaimType inte har angetts mappas den angivna principanspråkstypen till partneranspråkstypen med samma namn. Använd den här egenskapen när namnet på anspråkstypen skiljer sig från den andra parten. Namnet på det första anspråket är till exempel "givenName", medan partnern använder ett anspråk med namnet "first_name". |

## <a name="outputclaims"></a>OutputClaims

**Elementet OutputClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | En alternativ typ av utdataanspråk. |

### <a name="outputclaim"></a>OutputClaim

**Elementet OutputClaim** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfilen eller den överordnade principfilen. |
| Standardvärde | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om anspråket som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
|AlltidAnvändaDefaultValue |Inga |Tvinga användningen av standardvärdet.  |
| PartnerClaimType | Inga | Identifieraren för anspråkstypen för den externa partnern som den angivna principanspråkstypen mappar till. Om Attributet PartnerClaimType inte har angetts mappas den angivna principanspråkstypen till partneranspråkstypen med samma namn. Använd den här egenskapen när namnet på anspråkstypen skiljer sig från den andra parten. Namnet på det första anspråket är till exempel "givenName", medan partnern använder ett anspråk med namnet "first_name". |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformationer

Elementet **OutputClaimsTransformations** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Identifierare för anspråksomvandlingar som ska utföras innan några anspråk skickas till anspråksleverantören eller den förlitande parten. En anspråksomvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller generera nya. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**Elementet OutputClaimsTransformation** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråksomvandling som redan definierats i principfilen eller den överordnade principfilen. |

## <a name="validationtechnicalprofiles"></a>ValideringTekniskaProfiler

Elementet **ValidationTechnicalProfiles** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| ValideringTekniskprofil | 1:n | Identifierarna för tekniska profiler som används validerar vissa eller alla utdataanspråk för den refererande tekniska profilen. Alla ingående anspråk från den refererade tekniska profilen måste finnas i utdataanspråken för den refererande tekniska profilen. |

### <a name="validationtechnicalprofile"></a>ValideringTekniskprofil

Elementet **ValidationTechnicalProfile** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principfilen eller den överordnade principfilen. |

## <a name="subjectnaminginfo"></a>ÄmneNamingInfo

**SubjectNamingInfo** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType (Påståttstyp) | Ja | En identifierare av en anspråkstyp som redan har definierats i avsnittet ClaimsSchema i principfilen. |

## <a name="includetechnicalprofile"></a>Inkludera tekniskprofil

Elementet **IncludeTechnicalProfile** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principfilen eller den överordnade principfilen. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Elementet **UseTechnicalProfileForSessionManagement** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principfilen eller den överordnade principfilen. |

## <a name="enabledforuserjourneys"></a>AktiveradeFöranvändareJourer

**ClaimsProviderSelections** i en användarresa definierar listan över alternativ för val av anspråksprovider och deras ordning. Med elementet **EnabledForUserJourneys filtrerar** du, vilken anspråksleverantör som är tillgänglig för användaren. Elementet **EnabledForUserJourneys** innehåller något av följande värden:

- **Kör alltid**den tekniska profilen.
- **Hoppa aldrig**över den tekniska profilen.
- **OnClaimsExistence** körs endast när ett visst anspråk, som anges i den tekniska profilen finns.
- **OnItemExistenceInStringCollectionClaim**körs endast när ett objekt finns i ett stränginsamlingsanspråk.
- **OnItemAbsenceInStringCollectionClaim** körs endast när ett objekt inte finns i ett stränginsamlingsanspråk.

Med **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** eller **OnItemAbsenceInStringCollectionClaim**, krävs att du tillhandahåller följande metadata: **ClaimTypeOnWhichToEnable** anger anspråkets typ som ska utvärderas, **ClaimValueOnWhichToEnable** anger det värde som ska jämföras.

Följande tekniska profil körs endast om **strängsamlingen identityProviders** `facebook.com`innehåller värdet :

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
