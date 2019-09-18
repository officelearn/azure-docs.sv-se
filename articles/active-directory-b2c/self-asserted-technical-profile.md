---
title: Definiera en egen kontrollerad teknisk profil i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en egen kontrollerad teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4fec742766cebeb5b1d82655e09af77a888c375c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063679"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en egen kontrollerad teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alla interaktioner i Azure Active Directory B2C (Azure AD B2C) där användaren förväntas tillhandahålla indata är självkontrollerade tekniska profiler. Till exempel en registrerings sida, inloggnings sida eller lösen ords återställnings sida.

## <a name="protocol"></a>Protocol

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterare som används av Azure AD B2C, för självkontrollerad:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en egen kontrollerad teknisk profil för e-postregistrering:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Inmatade anspråk

I en självkontrollerad teknisk profil kan du använda elementen **InputClaims** och **InputClaimsTransformations** för att fylla i värdet för de anspråk som visas på den självkontrollerade sidan (utgående anspråk). I Redigera profil principen läser användar resan först användar profilen från Azure AD B2C katalog tjänsten. sedan anger den självkontrollerade tekniska profilen indata-anspråk med de användar data som lagras i användar profilen. Dessa anspråk samlas in från användar profilen och visas sedan för den användare som sedan kan redigera befintliga data.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som ska visas för insamling av data från användaren. Om du vill fylla i utgående anspråk med vissa värden använder du de anspråk för indata som tidigare beskrevs. Elementet kan också innehålla ett standardvärde. Ordningen på anspråken i **OutputClaims** styr i vilken ordning som Azure AD B2C återger anspråk på skärmen. Attributet **DefaultValue** börjar gälla endast om anspråket aldrig har angetts tidigare. Men om den har ställts in före i ett tidigare Orchestration-steg, även om användaren lämnar värdet tomt, träder inte standardvärdet i bruk. Om du vill framtvinga användningen av ett standardvärde anger du attributet **AlwaysUseDefaultValue** till `true`. Om du vill tvinga användaren att ange ett värde för ett bestämt utgående anspråk anger du det **obligatoriska** attributet för **OutputClaims** -elementet `true`till.

Elementet **claimType** i **OutputClaims** -samlingen måste ange **UserInputType** -elementet till alla typer `TextBox` av användarindata som stöds av Azure AD B2C, till exempel eller. `DropdownSingleSelect` Eller **OutputClaim** -elementet måste ange ett **Standardvärde**.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

Följande utgående anspråk är alltid inställt `live.com`på:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Användningsfall

Det finns fyra scenarier för utgående anspråk:

- **Samla in de utgående anspråken från användaren** – när du behöver samla in information från användaren, till exempel födelse datum, bör du lägga till anspråket i **OutputClaims** -samlingen. De anspråk som visas för användaren måste ange **UserInputType**, till exempel `TextBox` eller. `DropdownSingleSelect` Om den självkontrollerade tekniska profilen innehåller en validerings teknisk profil som matar ut samma anspråk, visar Azure AD B2C inte anspråket för användaren. Om det inte finns några utgående anspråk att visa för användaren, hoppar Azure AD B2C över den tekniska profilen.
- **Ange ett standardvärde i ett utgående anspråk** – utan att samla in data från användaren eller returnera data från den tekniska verifierings profilen. Den `LocalAccountSignUpWithLogonEmail` självkontrollerade tekniska profilen anger **SelfAsserted-ingångs** anspråk till `true`.
- **En teknisk verifierings profil returnerar utgående anspråk** – din tekniska profil kan anropa en teknisk validerings profil som returnerar vissa anspråk. Du kanske vill bubbla upp anspråken och returnera dem till nästa steg för att dirigera i användar resan. När du till exempel loggar in med ett lokalt konto `SelfAsserted-LocalAccountSignin-Email` anropar den självkontrollerade tekniska profilen den tekniska verifierings profilen med namnet. `login-NonInteractive` Den här tekniska profilen verifierar användarens autentiseringsuppgifter och returnerar även användar profilen. Som userPrincipalName, displayName, givenName och efter namn.
- **Spara anspråk via transformering av utgående anspråk**

I följande exempel visar den `LocalAccountSignUpWithLogonEmail` självkontrollerade tekniska profilen användningen av utgående anspråk och anger **SelfAsserted-indata** till. `true` , Är anspråk utdata från den`AAD-UserWriteUsingLogonEmail` tekniska profilen för verifiering och visas inte för användaren. `newUser` `authenticationSource` `objectId`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Kvarhåll anspråk

Om **PersistedClaims** -elementet saknas bevarar den självkontrollerade tekniska profilen inte data till Azure AD B2C. I stället görs ett anrop till en teknisk verifierings profil som ansvarar för att spara data. Exempelvis använder registrerings principen den `LocalAccountSignUpWithLogonEmail` självkontrollerade tekniska profilen för att samla in den nya användar profilen. Den `LocalAccountSignUpWithLogonEmail` tekniska profilen anropar verifieringens tekniska profil för att skapa kontot i Azure AD B2C.

## <a name="validation-technical-profiles"></a>Verifiera tekniska profiler

En teknisk validerings profil används för att verifiera vissa eller alla utgående anspråk för den refererande tekniska profilen. De ingående anspråken för verifieringen av den tekniska profilen måste visas i utgående anspråk för den självkontrollerade tekniska profilen. Den tekniska verifierings profilen verifierar indata från användaren och kan returnera ett fel till användaren.

Den tekniska profilen för verifiering kan vara vilken teknisk profil som helst i principen, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md) tekniska profiler. I föregående exempel verifierar den `LocalAccountSignUpWithLogonEmail` tekniska profilen att signinName inte finns i katalogen. Annars skapar verifieringen av den tekniska profilen ett lokalt konto och returnerar objectId, authenticationSource, newUser. Den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen `login-NonInteractive` anropar verifieringens tekniska profil för att verifiera användarens autentiseringsuppgifter.

Du kan också anropa en REST API teknisk profil med din affärs logik, skriva över indata-anspråk eller utöka användar data genom att ytterligare integrera med affärs program. Mer information finns i [verifiering teknisk profil](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| setting.showContinueButton | Nej | Visar knappen Fortsätt. Möjliga värden: `true` (standard) eller`false` |
| setting.showCancelButton | Nej | Visar knappen Avbryt. Möjliga värden: `true` (standard) eller`false` |
| setting.operatingMode | Nej | För en inloggnings sida styr den här egenskapen beteendet för fältet username, till exempel indatatyps verifiering och fel meddelanden. Förväntade värden: `Username` eller `Email`. |
| ContentDefinitionReferenceId | Ja | Identifieraren för den [innehålls definition](contentdefinitions.md) som är associerad med den här tekniska profilen. |
| EnforceEmailVerification | Nej | För registrering eller profil redigering tvingas e-postverifiering. Möjliga värden: `true` (standard) eller. `false` |
| anger. showSignupLink | Nej | Visar registrerings knappen. Möjliga värden: `true` (standard) eller`false` |
| anger. retryLimit | Nej | Styr antalet gånger som en användare kan försöka tillhandahålla data som kontrol leras mot en teknisk verifierings profil. En användare kan till exempel Logga in med ett konto som redan finns och fortsätter tills gränsen har uppnåtts.
| SignUpTarget | Nej | ID för registrerings målets Exchange. När användaren klickar på registrerings knappen Azure AD B2C kör den angivna Exchange-identifieraren. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet används inte.













