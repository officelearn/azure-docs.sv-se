---
title: Definiera en egen kontrollerad teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en egen kontrollerad teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a16fb1184de5b545b3ef527b1a66ffb7b68d1ef4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197926"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en egen kontrollerad teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alla interaktioner i Azure Active Directory B2C (Azure AD B2C) där användaren förväntas tillhandahålla indata är självkontrollerade tekniska profiler. Till exempel en registrerings sida, inloggnings sida eller lösen ords återställnings sida.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterare som används av Azure AD B2C för självkontrollerad: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en egen kontrollerad teknisk profil för e-postregistrering:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Inmatade anspråk

I en självkontrollerad teknisk profil kan du använda elementen **InputClaims** och **InputClaimsTransformations** för att fylla i värdet för de anspråk som visas på den självkontrollerade sidan (Visa anspråk). I Redigera profil principen läser användar resan först användar profilen från Azure AD B2C katalog tjänsten. sedan anger den självkontrollerade tekniska profilen indata-anspråk med de användar data som lagras i användar profilen. Dessa anspråk samlas in från användar profilen och visas sedan för den användare som sedan kan redigera befintliga data.

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

Funktionen Visa anspråk är för närvarande en för **hands version**.

**DisplayClaims** -elementet innehåller en lista över anspråk som ska visas på skärmen för insamling av data från användaren. Om du vill fylla i värden för utgående anspråk använder du de anspråk som tidigare beskrivits. Elementet kan också innehålla ett standardvärde.

Ordningen på anspråken i **DisplayClaims** anger i vilken ordning som Azure AD B2C återger anspråk på skärmen. Om du vill tvinga användaren att ange ett värde för ett bestämt anspråk anger du det **obligatoriska** attributet för **DisplayClaim** -elementet till `true`.

Elementet **claimType** i **DisplayClaims** -samlingen måste ange **UserInputType** -elementet till alla typer av användarindata som stöds av Azure AD B2C. Exempel: `TextBox` eller `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Lägga till en referens till en visare

I visnings anspråks samlingen kan du inkludera en referens till en [visare som du](display-controls.md) har skapat. En visnings kontroll är ett användar gränssnitts element som har särskilda funktioner och som samverkar med Azure AD B2C backend-tjänsten. Det gör att användaren kan utföra åtgärder på sidan som anropar en teknisk profil för verifiering på Server sidan. Du kan till exempel verifiera en e-postadress, telefonnummer eller kund lojalitets nummer.

I följande exempel `TechnicalProfile` illustrerar användningen av Visa anspråk med visnings kontroller.

* Det första visnings kravet gör en referens till `emailVerificationControl` visnings kontroll som samlar in och verifierar e-postadressen.
* Det femte visnings kravet gör en referens till `phoneVerificationControl` visnings kontroll som samlar in och verifierar ett telefonnummer.
* De andra visnings anspråken är ClaimTypes som ska samlas in från användaren.

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

Som nämnts kan ett visnings anspråk med en referens till en visnings kontroll köra sin egen verifiering, till exempel verifiera e-postadressen. Dessutom stöder den självkontrollerade sidan en verifierad teknisk profil för att verifiera hela sidan, inklusive indata från användaren (anspråks typer eller visnings kontroller) innan du går vidare till nästa steg i dirigeringen.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Kombinera användning av visnings anspråk och utgående anspråk noggrant

Om du anger ett eller flera **DisplayClaim** -element i en självkontrollerad teknisk profil måste du använda en DisplayClaim för *varje* anspråk som du vill visa på skärmen och samla in från användaren. Inga utgående anspråk visas av en egen kontrollerad teknisk profil som innehåller minst ett visnings anspråk.

Tänk på följande exempel där ett `age`-anspråk definieras som ett **utgående** anspråk i en grundläggande princip. Innan du lägger till visnings anspråk till den självkontrollerade tekniska profilen visas `age`-anspråk på skärmen för data insamling från användaren:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Om en princip för löv som ärver den basen sedan anger `officeNumber` som **visnings** anspråk:

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

`age`-anspråk i bas principen visas inte längre på skärmen för användaren – det är i själva verket "dolt". Om du vill visa `age`-anspråk och samla in värdet för ålder från användaren måste du lägga till ett `age` **DisplayClaim**.

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som ska returneras till nästa Orchestration-steg. Attributet **DefaultValue** börjar gälla endast om anspråket aldrig har angetts. Om den har angetts i ett tidigare Orchestration-steg börjar inte standardvärdet gälla även om användaren lämnar värdet tomt. Om du vill framtvinga användningen av ett standardvärde anger du **AlwaysUseDefaultValue** -attributet till `true`.

> [!NOTE]
> I tidigare versioner av Identity Experience Framework (IEF) användes utgående anspråk för att samla in data från användaren. Använd en **DisplayClaims** -samling i stället om du vill samla in data från användaren.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="when-you-should-use-output-claims"></a>När du ska använda utgående anspråk

I en självkontrollerad teknisk profil, returnerar utgående anspråks samlingen anspråk till nästa steg för att dirigera.

Du bör använda utgående anspråk när:

- **Anspråk är utdata genom omvandling av utgående anspråk**.
- **Ange ett standardvärde i ett utgående anspråk** utan att samla in data från användaren eller returnera data från den tekniska verifierings profilen. `LocalAccountSignUpWithLogonEmail` självkontrollerad teknisk profil ställer in det **utförda SelfAsserted-ingångs** kravet på `true`.
- **En teknisk verifierings profil returnerar utgående anspråk** – din tekniska profil kan anropa en teknisk validerings profil som returnerar vissa anspråk. Du kanske vill bubbla upp anspråken och returnera dem till nästa steg för att dirigera i användar resan. När du till exempel loggar in med ett lokalt konto får den självkontrollerade tekniska profilen med namnet `SelfAsserted-LocalAccountSignin-Email` anropar den tekniska verifierings profilen med namnet `login-NonInteractive`. Den här tekniska profilen verifierar användarens autentiseringsuppgifter och returnerar även användar profilen. Som userPrincipalName, displayName, givenName och efter namn.
- **En visnings kontroll returnerar utgående anspråk** – din tekniska profil kan ha en referens till en [visnings kontroll](display-controls.md). Visnings kontrollen returnerar vissa anspråk, till exempel den verifierade e-postadressen. Du kanske vill bubbla upp anspråken och returnera dem till nästa steg för att dirigera i användar resan. Funktionen Visa kontroll är för närvarande en för **hands version**.

I följande exempel demonstreras användningen av en självkontrollerad teknisk profil som använder både Visa anspråk och utgående anspråk.

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

## <a name="persist-claims"></a>Kvarhåll anspråk

Om **PersistedClaims** -elementet saknas bevarar den självkontrollerade tekniska profilen inte data till Azure AD B2C. I stället görs ett anrop till en teknisk verifierings profil som ansvarar för att spara data. Exempelvis använder registrerings principen `LocalAccountSignUpWithLogonEmail` självkontrollerad teknisk profil för att samla in den nya användar profilen. Den `LocalAccountSignUpWithLogonEmail` tekniska profilen anropar den tekniska verifierings profilen för att skapa kontot i Azure AD B2C.

## <a name="validation-technical-profiles"></a>Verifiera tekniska profiler

En teknisk validerings profil används för att verifiera vissa eller alla utgående anspråk för den refererande tekniska profilen. De ingående anspråken för verifieringen av den tekniska profilen måste visas i utgående anspråk för den självkontrollerade tekniska profilen. Den tekniska verifierings profilen verifierar indata från användaren och kan returnera ett fel till användaren.

Den tekniska profilen för verifiering kan vara vilken teknisk profil som helst i principen, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md) tekniska profiler. I föregående exempel verifierar `LocalAccountSignUpWithLogonEmail` tekniska profilen att signinName inte finns i katalogen. Annars skapar verifieringen av den tekniska profilen ett lokalt konto och returnerar objectId, authenticationSource, newUser. Den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen anropar den `login-NonInteractive` validerings tekniska profil för att verifiera användarens autentiseringsuppgifter.

Du kan också anropa en REST API teknisk profil med din affärs logik, skriva över indata-anspråk eller utöka användar data genom att ytterligare integrera med affärs program. Mer information finns i [verifiering teknisk profil](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| setting.operatingMode | Nej | För en inloggnings sida styr den här egenskapen beteendet för fältet username, till exempel indatatyps verifiering och fel meddelanden. Förväntat värde: `Username` eller `Email`. |
| AllowGenerationOfClaimsWithNullValues| Nej| Tillåt att ett anspråk skapas med null-värde. Till exempel, i ett fall kan användaren inte markera en kryss ruta.|
| ContentDefinitionReferenceId | Ja | Identifieraren för den [innehålls definition](contentdefinitions.md) som är associerad med den här tekniska profilen. |
| EnforceEmailVerification | Nej | För registrering eller profil redigering tvingas e-postverifiering. Möjliga värden: `true` (standard) eller `false`. |
| anger. retryLimit | Nej | Styr antalet gånger som en användare kan försöka tillhandahålla data som kontrol leras mot en teknisk verifierings profil. En användare kan till exempel Logga in med ett konto som redan finns och fortsätter tills gränsen har uppnåtts.
| SignUpTarget | Nej | ID för registrerings målets Exchange. När användaren klickar på registrerings knappen Azure AD B2C kör den angivna Exchange-identifieraren. |
| setting.showCancelButton | Nej | Visar knappen Avbryt. Möjliga värden: `true` (standard) eller `false` |
| setting.showContinueButton | Nej | Visar knappen Fortsätt. Möjliga värden: `true` (standard) eller `false` |
| anger. showSignupLink | Nej | Visar registrerings knappen. Möjliga värden: `true` (standard) eller `false` |
| anger. forgotPasswordLinkLocation| Nej| Visar länken Glömt lösen ord. Möjliga värden: `AfterInput` (standard) länken visas längst ned på sidan, eller `None` tar bort länken Glömt lösen ord.| 
| IncludeClaimResolvingInClaimsHandling  | Nej | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true`eller `false` (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true`. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet används inte.
