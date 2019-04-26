---
title: Definiera en självkontrollerad tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en självkontrollerad tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: dcc94daeb19174b85fface05222f8842e9544adf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419500"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en självkontrollerad tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

All interaktion i Azure Active Directory (Azure AD) B2C där användaren förväntas som indata är lokal verifieringsvillkor tekniska profiler. Till exempel en registreringssidan, en inloggningssida eller ett lösenord för lösenordsåterställning.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `Proprietary`. Den **hanteraren** attributet måste innehålla det fullständigt kvalificerade namnet på protokoll hanteraren sammansättningen som används av Azure AD B2C för lokal verifieringsvillkor: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en självkontrollerad tekniska profil för e-post registrering:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Inkommande anspråk

Du kan använda i en självkontrollerad tekniska profilen den **InputClaims** och **InputClaimsTransformations** element att fylla värdet för de anspråk som visas på sidan självkontrollerad (utdata anspråk). Till exempel i profilprincip redigera användarresa först läser användarprofilen från Azure AD B2C-katalogtjänsten sedan självkontrollerad tekniska profilen anger de inkommande anspråken med de data som lagras i användarens profil. De här anspråken som samlas in från användarens profil och sedan presenteras för den användare som kan redigera befintliga data.

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


## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** elementet innehåller en lista över anspråk lämnas för att samla in data från användaren. Använd de inkommande anspråken som beskrevs för att fylla utgående anspråk med vissa värden. Elementet kan också innehålla ett standardvärde. Ordningen på anspråk i **OutputClaims** styr den ordning som att Azure AD B2C återger anspråk på skärmen. Den **DefaultValue** attributet aktiveras endast om anspråket har aldrig angetts innan. Men om den har ställts in innan i ett föregående orchestration-steg, även om användaren lämnar värdet tomt, standardvärdet börja inte gälla. Om du vill framtvinga användningen av ett standardvärde, ange den **AlwaysUseDefaultValue** attributet `true`. Om du vill tvinga användaren att ange ett värde för en specifik utdata-anspråket, ange den **krävs** attributet för den **OutputClaims** elementet mot `true`.

Den **ClaimType** elementet i den **OutputClaims** samling måste ange den **UserInputType** element till någon användare ange typ som stöds av Azure AD B2C, till exempel `TextBox`eller `DropdownSingleSelect`. Eller **OutputClaim** elementet måste ställa in en **DefaultValue**.  

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

Följande utdata-anspråket har alltid värdet `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Användningsfall

Det finns fyra scenarier för utgående anspråk:

- **Samla in utdata anspråk från användaren** – när du behöver samla in information från användaren, till exempel födelsedatum, bör du lägga till anspråk till den **OutputClaims** samling. De anspråk som visas för användaren måste ange den **UserInputType**, till exempel `TextBox` eller `DropdownSingleSelect`. Om den självkontrollerad tekniska profilen innehåller en teknisk verifieringsprofil som visar samma anspråk, någon Azure AD B2C inte utgör anspråk för användaren. Om det finns inga eventuella utdata-anspråket ska presenteras för användaren, Azure AD B2C hoppar du över den tekniska profilen.
- **Ange ett standardvärde i en utdata-anspråket** – utan att samla in data från användaren eller returnera data från den tekniska profilen för verifiering. Den `LocalAccountSignUpWithLogonEmail` lokal verifieringsvillkor tekniska profilen anger den **körs SelfAsserted-indata-** anspråk som ska `true`.
- **En teknisk verifieringsprofil returnerar utdataanspråk** -dina tekniska profilen kan anropa en teknisk verifieringsprofil som returnerar vissa anspråk. Du kanske vill bubbla anspråk och returnera dem till nästa orchestration-steg på vägen för användaren. Till exempel när du loggar in med ett lokalt konto, självkontrollerad tekniska profilen med namnet `SelfAsserted-LocalAccountSignin-Email` anropar den tekniska profilen för verifiering med namnet `login-NonInteractive`. Den här tekniska profilen verifierar användarens autentiseringsuppgifter och även returnerar användarens profil. Till exempel ”userPrincipalName', 'displayName', 'givenName” och ”efternamn”.
- **Utgående anspråk via anspråkstransformering för utdata**

I följande exempel visas den `LocalAccountSignUpWithLogonEmail` lokal verifieringsvillkor tekniska profilen visar användningen av utdataanspråk och uppsättningar **körs SelfAsserted-indata-** till `true`. Den `objectId`, `authenticationSource`, `newUser` anspråk är utdata från den `AAD-UserWriteUsingLogonEmail` verifiering tekniska profilen och visas inte för användaren.

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

## <a name="persist-claims"></a>Spara anspråk

Om den **PersistedClaims** element saknas, självkontrollerad tekniska profilen inte spara data till Azure AD B2C. Ett anrop görs i stället för en teknisk verifieringsprofil som ansvarar för att spara data för. Principen för registrering använder exempelvis den `LocalAccountSignUpWithLogonEmail` lokal verifieringsvillkor tekniska profilen att samla in den nya användarprofilen. Den `LocalAccountSignUpWithLogonEmail` tekniska profilen anropar den tekniska profilen verifiering för att skapa kontot i Azure AD B2C.

## <a name="validation-technical-profiles"></a>Verifiering tekniska profiler

En teknisk verifieringsprofil används för att verifiera några eller alla utgående anspråk på den refererande tekniska profilen. De inkommande anspråken av den tekniska profilen verifiering måste visas i utdataanspråk på den självkontrollerad tekniska profilen. Den tekniska profilen verifiering verifierar indata från användaren och kan returnera ett fel för användaren. 

Den tekniska profilen verifiering kan vara alla tekniska profilen i principen, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md) tekniska profiler. I exemplet ovan den `LocalAccountSignUpWithLogonEmail` tekniska profilen verifierar att signinName inte finns i katalogen. Om inte, den tekniska profilen verifiering skapar ett lokalt konto och returnerar objectId, authenticationSource, ny användare. Den `SelfAsserted-LocalAccountSignin-Email` tekniska profil-anrop i `login-NonInteractive` tekniska profil för att verifiera användarens autentiseringsuppgifter.

Du kan också anropa en REST API-tekniska profilen med din affärslogik, ersätta inkommande anspråk eller utöka användardata genom att ytterligare integrera med företagets line-of-business-program. Mer information finns i [tekniska profil](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| setting.showContinueButton | Nej | Visar knappen Fortsätt. Möjliga värden: `true` (standard), eller `false` |
| setting.showCancelButton | Nej | Visar knappen Avbryt. Möjliga värden: `true` (standard), eller `false` |
| setting.operatingMode | Nej | Den här egenskapen styr beteendet för fältet för användarnamn, till exempel verifiering av indata- och felmeddelanden för en på inloggningssidan. Förväntade värden: `Username` eller `Email`. |
| ContentDefinitionReferenceId | Ja | Identifierare för den [innehåll definition](contentdefinitions.md) som är associerade med den här tekniska profilen. |
| EnforceEmailVerification | Nej | För registrering eller profilera redigera, tillämpar e-Postverifiering. Möjliga värden: `true` (standard), eller `false`. | 
| setting.showSignupLink | Nej | Visar knappen registrera dig. Möjliga värden: `true` (standard), eller `false` |
| setting.retryLimit | Nej | Styr hur många gånger som en användare kan försöka att tillhandahålla de data som kontrolleras mot en tekniska profil. Exempelvis kan en användare försöker registrera dig med ett konto som redan finns och håller försök tills gränsen har nåtts.
| SignUpTarget | Nej | Identifierare för registrering target exchange. När användaren klickar på knappen registrera dig, kör den angivna exchange-identifieraren i Azure AD B2C. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Den **CryptographicKeys** elementet används inte.













