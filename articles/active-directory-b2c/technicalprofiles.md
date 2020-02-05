---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Ange TechnicalProfiles-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad6b84323ac49713506bc61bd0051421e0234a94
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982289"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ett **TechnicalProfiles** -element innehåller en uppsättning tekniska profiler som stöds av anspråks leverantören. Varje anspråks leverantör måste ha en eller flera tekniska profiler som avgör slut punkterna och de protokoll som behövs för att kommunicera med anspråks leverantören. En anspråks leverantör kan ha flera tekniska profiler.

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

**TechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------|---------|---------|
| Id | Ja | En unik identifierare för den tekniska profilen. Den tekniska profilen kan refereras till med hjälp av den här identifieraren från andra element i princip filen. Till exempel **OrchestrationSteps** och **ValidationTechnicalProfile**. |

**TechnicalProfile** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Domän namnet för den tekniska profilen. Om din tekniska profil exempelvis anger Facebook Identity Provider, är domän namnet Facebook.com. |
| DisplayName | 1:1 | Namnet på den tekniska profil som kan visas för användarna. |
| Beskrivning | 0:1 | Beskrivningen av den tekniska profil som kan visas för användarna. |
| Protokoll | 0:1 | Protokollet som används för kommunikation med den andra parten. |
| Metadata | 0:1 | En samling nyckel/värde-par som används av protokollet för att kommunicera med slut punkten i en transaktions kurs. |
| InputTokenFormat | 0:1 | Formatet för Indataporten. Möjliga värden: `JSON`, `JWT`, `SAML11`eller `SAML2`. `JWT`-värdet representerar en JSON Web Token enligt IETF-specifikationen. `SAML11` svärdet representerar en SAML 1,1-säkerhetstoken enligt specifikationen OASIS.  `SAML2` svärdet representerar en SAML 2,0-säkerhetstoken enligt specifikationen OASIS. |
| OutputTokenFormat | 0:1 | Formatet för utdatabufferten. Möjliga värden: `JSON`, `JWT`, `SAML11`eller `SAML2`. |
| CryptographicKeys | 0:1 | En lista över kryptografiska nycklar som används i den tekniska profilen. |
| InputClaimsTransformations | 0:1 | En lista med tidigare definierade referenser till anspråks omvandlingar som ska utföras innan anspråk skickas till anspråks leverantören eller den förlitande parten. |
| InputClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som tas in i den tekniska profilen. |
| PersistedClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som är beständiga av anspråks leverantören som relaterar till den tekniska profilen. |
| DisplayClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som presenteras av anspråks leverantören som relaterar till den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md). Funktionen DisplayClaims är för närvarande en för **hands version**. |
| OutputClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som tas ut i den tekniska profilen. |
| OutputClaimsTransformations | 0:1 | En lista med tidigare definierade referenser till anspråks omvandlingar som ska utföras när anspråken tas emot från anspråks leverantören. |
| ValidationTechnicalProfiles | 0: n | En lista med referenser till andra tekniska profiler som den tekniska profilen använder i validerings syfte. Mer information finns i [verifiering teknisk profil](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Styr produktionen av ämnes namnet i tokens där ämnes namnet anges separat från anspråk. Till exempel OAuth eller SAML.  |
| IncludeInSso | 0:1 |  Om användningen av den här tekniska profilen ska använda enkel inloggning (SSO) för sessionen eller i stället kräva explicit interaktion. Det här elementet är endast giltigt i SelfAsserted-profiler som används i en teknisk validerings profil. Möjliga värden: `true` (standard) eller `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | En identifierare för en teknisk profil från vilken du vill att alla indata-och utgående anspråk ska läggas till i den här tekniska profilen. Den tekniska profilen som refereras till måste definieras i samma princip fil. |
| IncludeTechnicalProfile |0:1 | En identifierare för en teknisk profil från vilken du vill att alla data ska läggas till i den här tekniska profilen. Den tekniska profilen som refereras måste finnas i samma princip fil. |
| UseTechnicalProfileForSessionManagement | 0:1 | En annan teknisk profil som ska användas för hantering av sessioner. |
|EnabledForUserJourneys| 0:1 |Kontrollerar om den tekniska profilen körs i en användar resa.  |

## <a name="protocol"></a>Protokoll

**Protokoll** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C som används som en del av den tekniska profilen. Möjliga värden: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary`, `session management`, `self-asserted`eller `None`. |
| Protokollhanteraren | Inga | När protokoll namnet är inställt på `Proprietary`anger du det fullständigt kvalificerade namnet på den sammansättning som används av Azure AD B2C för att fastställa protokoll hanteraren. |

## <a name="metadata"></a>Metadata

Ett **metadataelement** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Metadata som relaterar till den tekniska profilen. Varje typ av teknisk profil har en annan uppsättning metadata-objekt. Mer information finns i avsnittet tekniska profil typer. |

### <a name="item"></a>Objekt

**Objekt** elementet i **metadata** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Nyckeln metadata. Se varje teknisk profil typ för listan med metadataobjekt. |

## <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Nyckel | 1: n | En kryptografisk nyckel som används i den här tekniska profilen. |

### <a name="key"></a>Nyckel

**Nyckel** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Inga | En unik identifierare för ett visst nyckel par som refereras från andra element i princip filen. |
| StorageReferenceId | Ja | En identifierare för lagrings nyckel behållare som refereras från andra element i princip filen. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifieraren för en anspråks omvandling som ska köras innan anspråk skickas till anspråksprovidern eller den förlitande parten. En anspråks omvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller skapa nya. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråks omvandling som redan har definierats i princip filen eller den överordnade princip filen. |

## <a name="inputclaims"></a>InputClaims

**InputClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | En förväntad typ av Indatatyp. |

### <a name="inputclaim"></a>InputClaim

**InputClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om det anspråk som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om attributet PartnerClaimType inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

## <a name="displayclaims"></a>DisplayClaims

**DisplayClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | En förväntad typ av Indatatyp. |

Funktionen DislayClaims är för närvarande en för **hands version**.

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Inga | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| DisplayControlReferenceId | Inga | Identifieraren för en [visnings kontroll](display-controls.md) som redan har definierats i ClaimsSchema-avsnittet i princip filen eller den överordnade princip filen. |
| Krävs | Inga | Anger om visnings kravet krävs. |

**DisplayClaim** kräver att du anger antingen en `ClaimTypeReferenceId` eller `DisplayControlReferenceId`.

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Anspråks typen som ska sparas. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om det anspråk som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om attributet PartnerClaimType inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | En förväntad typ av utgående anspråk. |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om det anspråk som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
|AlwaysUseDefaultValue |Inga |Tvinga användningen av standardvärdet.  |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om attributet PartnerClaimType inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifierare för anspråks omvandlingar som ska köras innan anspråk skickas till anspråksprovidern eller den förlitande parten. En anspråks omvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller skapa nya. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråks omvandling som redan har definierats i princip filen eller den överordnade princip filen. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifierarna för de tekniska profiler som används validerar några eller alla utgående anspråk för den refererande tekniska profilen. Alla indata-anspråk för den refererade tekniska profilen måste visas i de utgående anspråken för den refererande tekniska profilen. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType | Ja | En identifierare för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

**ClaimsProviderSelections** i en användar resa definierar listan över val alternativ för anspråks leverantör och deras ordning. Med det **EnabledForUserJourneys** -element som du filtrerar, vilken anspråks leverantör som är tillgänglig för användaren. **EnabledForUserJourneys** -elementet innehåller ett av följande värden:

- Kör **alltid**den tekniska profilen.
- Hoppa **aldrig**över den tekniska profilen.
- **OnClaimsExistence** körs bara när ett visst anspråk som anges i den tekniska profilen finns.
- **OnItemExistenceInStringCollectionClaim**, kör bara när ett objekt finns i ett anspråk för en sträng samling.
- **OnItemAbsenceInStringCollectionClaim** körs bara när det inte finns något objekt i ett anspråks krav för en sträng samling.

Om du använder **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** eller **OnItemAbsenceInStringCollectionClaim**måste du ange följande metadata: **ClaimTypeOnWhichToEnable** anger det anspråks typ som ska utvärderas, **ClaimValueOnWhichToEnable** anger det värde som ska jämföras.

Följande tekniska profil körs endast om **identityprovider** -sträng samlingen innehåller värdet för `facebook.com`:

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
