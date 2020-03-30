---
title: Definiera en självförsäkrad teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en självförsäkrad teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332512"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en självbekrände teknisk profil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alla interaktioner i Azure Active Directory B2C (Azure AD B2C) där användaren förväntas tillhandahålla indata är självpåförda tekniska profiler. Till exempel en registreringssida, inloggningssida eller sida för återställning av lösenord.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C för självbehärskning:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en självpåförd teknisk profil för registrering via e-post:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Ingående anspråk

I en självpåförd teknisk profil kan du använda elementen **InputClaims** och **InputClaimsTransformations** för att fylla i värdet på de anspråk som visas på den självpåsäkrade sidan (visningsanspråk). I redigeringsprofilprincipen läser användarens färd först användarprofilen från Azure AD B2C-katalogtjänsten och anger sedan den självpåtterade tekniska profilen indataanspråken med användardata som lagras i användarprofilen. Dessa anspråk samlas in från användarprofilen och presenteras sedan för användaren som sedan kan redigera befintliga data.

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

## <a name="display-claims"></a>Visa anspråk

Funktionen för visningsanspråk är för närvarande **förhandsgranskning**.

**DisplayClaims-elementet** innehåller en lista över anspråk som ska visas på skärmen för att samla in data från användaren. Om du vill fylla i värdena för visningsanspråk använder du de indataanspråk som tidigare beskrevs. Elementet kan också innehålla ett standardvärde.

Ordningen på anspråken i **DisplayClaims** anger i vilken ordning Azure AD B2C återger anspråken på skärmen. Om du vill tvinga användaren att ange ett värde för ett visst `true`anspråk anger du **attributet Required för** **DisplayClaim-elementet** på .

**ClaimType-elementet** i **DisplayClaims-samlingen** måste ange **UserInputType-elementet** till alla indatatyper för användare som stöds av Azure AD B2C. Exempel: `TextBox` eller `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Lägga till en referens till en DisplayControl

I samlingen visningsanspråk kan du inkludera en referens till en [DisplayControl](display-controls.md) som du har skapat. En bildskärmskontroll är ett användargränssnittselement som har särskilda funktioner och interagerar med Azure AD B2C-backend-tjänsten. Det gör det möjligt för användaren att utföra åtgärder på sidan som anropar en teknisk profil för validering i serverdelen. Verifiera till exempel en e-postadress, ett telefonnummer eller ett kundlojalitetsnummer.

Följande exempel `TechnicalProfile` illustrerar användningen av visningsanspråk med visningskontroller.

* Det första visningsanspråket `emailVerificationControl` refererar till visningskontrollen, som samlar in och verifierar e-postadressen.
* Det femte visningsanspråket `phoneVerificationControl` hänvisar till visningskontrollen, som samlar in och verifierar ett telefonnummer.
* De andra visningsanspråken är ClaimTypes som ska samlas in från användaren.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Som nämnts kan ett visningsanspråk med en referens till en bildskärmskontroll köra sin egen validering, till exempel verifiera e-postadressen. Dessutom stöder den självpåsträns sida med hjälp av en teknisk valideringsprofil för att validera hela sidan, inklusive alla användarindata (anspråkstyper eller visningskontroller), innan du går vidare till nästa orchestration-steg.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Kombinera användningen av visningsanspråk och utdataanspråk noggrant

Om du anger ett eller flera **DisplayClaim-element** i en självpåförd teknisk profil måste du använda ett DisplayClaim för *varje* anspråk som du vill visa på skärmen och samla in från användaren. Inga utdataanspråk visas av en självpåförd teknisk profil som innehåller minst ett visningsanspråk.

Tänk på följande exempel `age` där ett anspråk definieras som ett **utdataanspråk** i en basprincip. Innan du lägger till bildskärmsanspråk i `age` den självpåsterade tekniska profilen visas anspråket på skärmen för datainsamling från användaren:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Om en lövprincip som ärver `officeNumber` basen senare anger som ett visningsanspråk: **display**

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Påståendet `age` i baspolicyn presenteras inte längre på skärmen för användaren - det är effektivt "dold". Om du `age` vill visa anspråket och samla in `age` åldersvärdet från användaren måste du lägga till ett **DisplayClaim**.

## <a name="output-claims"></a>Utgående fordringar

Elementet **OutputClaims** innehåller en lista över anspråk som ska returneras till nästa orchestration-steg. **Attributet DefaultValue** börjar gälla endast om anspråket aldrig har angetts. Om det angavs i ett tidigare orchestration-steg börjar standardvärdet inte gälla även om användaren lämnar värdet tomt. Om du vill tvinga fram användning av ett standardvärde ställer `true`du in attributet **AlwaysUseDefaultValue** på .

Av säkerhetsskäl är ett`UserInputType` lösenordsanspråksvärde (inställt på `Password`) endast tillgängligt för den självpåsterade tekniska profilens tekniska profiler för validering. Du kan inte använda lösenordsanspråk i nästa orchestration-steg. 

> [!NOTE]
> I tidigare versioner av Identity Experience Framework (IEF) användes utdataanspråk för att samla in data från användaren. Om du vill samla in data från användaren använder du en **DisplayClaims-samling** i stället.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

### <a name="when-you-should-use-output-claims"></a>När du ska använda utdataanspråk

I en självpåstående teknisk profil returnerar insamlingen av utdataanspråk anspråk till nästa orchestration-steg.

Använd utdataanspråk när:

- **Anspråk utdata efter omvandling av utdataanspråk**.
- **Ange ett standardvärde i ett utdataanspråk** utan att samla in data från användaren eller returnera data från den tekniska profilen för validering. Den `LocalAccountSignUpWithLogonEmail` självpåsterade tekniska profilen anger anspråket **utförd självskyddad inmatning** till `true`.
- **En teknisk valideringsprofil returnerar utdataanspråken** – Din tekniska profil kan anropa en teknisk profil för validering som returnerar vissa anspråk. Du kanske vill bubbla upp anspråk och returnera dem till nästa orchestration steg i användaren resan. När du till exempel loggar in med ett lokalt konto `SelfAsserted-LocalAccountSignin-Email` anropar den `login-NonInteractive`självsäkra tekniska profilen som heter den tekniska profilen för validering med namnet . Den här tekniska profilen validerar användarautentiseringsuppgifterna och returnerar även användarprofilen. Såsom "userPrincipalName", "displayName", "givenName" och "surName".
- **En displaykontroll returnerar utdataanspråken** - Din tekniska profil kan ha en referens till en [bildskärmskontroll](display-controls.md). Visningskontrollen returnerar vissa anspråk, till exempel den verifierade e-postadressen. Du kanske vill bubbla upp anspråk och returnera dem till nästa orchestration steg i användaren resan. Visningskontrollfunktionen är för närvarande i **förhandsgranskning**.

Följande exempel visar användningen av en självpåförd teknisk profil som använder både visningsanspråk och utdataanspråk.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Kvarstår anspråk

Elementet PersistedClaims används inte. Den självsäkra tekniska profilen bevarar inte data till Azure AD B2C. I stället görs ett samtal till en teknisk profil för validering som ansvarar för att data bevaras. Registreringsprincipen använder till exempel den `LocalAccountSignUpWithLogonEmail` självsäkra tekniska profilen för att samla in den nya användarprofilen. Den `LocalAccountSignUpWithLogonEmail` tekniska profilen anropar den tekniska profilen för validering för att skapa kontot i Azure AD B2C.

## <a name="validation-technical-profiles"></a>Tekniska profiler för validering

En teknisk valideringsprofil används för att validera vissa eller alla utdataanspråk för den refererande tekniska profilen. Indataanspråken för den tekniska profilen för validering måste finnas i den självpåstådda tekniska profilens påståenden. Den tekniska profilen för validering validerar användarens indata och kan returnera ett fel till användaren.

Den tekniska profilen för validering kan vara vilken teknisk profil som helst i principen, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en teknisk REST [API-profil.](restful-technical-profile.md) I föregående exempel `LocalAccountSignUpWithLogonEmail` verifierar den tekniska profilen att inloggningsnamnet inte finns i katalogen. Om inte, skapar den tekniska profilen för validering ett lokalt konto och returnerar objectId, authenticationSource, newUser. Den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen `login-NonInteractive` anropar den tekniska profilen för validering för att verifiera användarautentiseringsuppgifterna.

Du kan också anropa en teknisk REST API-profil med din affärslogik, skriva över indataanspråk eller berika användardata genom att ytterligare integrera med företagets affärsprogram. Mer information finns i [Teknisk valideringsprofil](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Inga | För en inloggningssida styr den här egenskapen beteendet för användarnamnsfältet, till exempel indataverifiering och felmeddelanden. Förväntade `Username` värden: `Email`eller .  |
| TillåtGenerationOfClaimsWithNullValues| Inga| Tillåt att generera ett anspråk med null-värde. I ett ärende markerar användaren till exempel ingen kryssruta.|
| InnehållDefinitionReferenceId | Ja | Identifieraren för [innehållsdefinitionen som](contentdefinitions.md) är associerad med den här tekniska profilen. |
| EnforceEmailVerification | Inga | För registrering eller profilredigering, upprätthåller e-postverifiering. Möjliga värden: `true` (standard) eller `false`. |
| setting.retryLimit | Inga | Styr hur många gånger en användare kan försöka tillhandahålla data som kontrolleras mot en teknisk profil för validering. En användare försöker till exempel registrera sig med ett konto som redan finns och försöker tills gränsen har nåtts.
| Registreringsresmål <sup>1</sup>| Inga | Exchange-identifierare för registreringsmål. När användaren klickar på registreringsknappen kör Azure AD B2C den angivna exchange-identifieraren. |
| setting.showCancelButton | Inga | Visar avbruten-knappen. Möjliga värden: `true` (standard) eller`false` |
| setting.showFortsättaButton | Inga | Visar fortsättningsknappen. Möjliga värden: `true` (standard) eller`false` |
| setting.showSignupLink <sup>2</sup>| Inga | Visar registreringsknappen. Möjliga värden: `true` (standard) eller`false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| Inga| Visar länken för glömt lösenord. Möjliga värden: `AfterInput` (standard) länken visas längst ned på `None` sidan, eller tar bort länken för glömt lösenord.|
| setting.enableRememberMe <sup>2</sup>| Inga| Visar kryssrutan [Håll mig inloggad.](custom-policy-keep-me-signed-in.md) Möjliga värden: `true` `false` , eller (standard). |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |

Obs!
1. Tillgänglig för innehållsdefinition `unifiedssp` [DataUri](contentdefinitions.md#datauri) typ av , eller `unifiedssd`.
1. Tillgänglig för innehållsdefinition `unifiedssp` [DataUri](contentdefinitions.md#datauri) typ av , eller `unifiedssd`. [Sidlayout version](page-layout.md) 1.1.0 och högre.

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** används inte.
