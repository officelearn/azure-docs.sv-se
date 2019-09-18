---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: Ange TrustFrameworkPolicy-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 673807377914aabad5b90d1ac2ecc16623870d30
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063359"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anpassad princip visas som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar element i principen, till exempel anspråks schema, anspråk omvandlingar, innehålls definitioner, anspråks leverantörer, tekniska profiler, användar resa och Orchestration-steg. Varje princip fil definieras i **TrustFrameworkPolicy** -elementet på den översta nivån i en princip fil.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Den schema version som ska användas för att köra principen. Värdet måste vara`0.3.0.0` |
| TenantObjectId | Nej | Den unika objekt identifieraren för Azure Active Directory B2C-klienten (Azure AD B2C). |
| TenantId | Ja | Den unika identifieraren för den klient som principen tillhör. |
| `PolicyId` | Ja | Den unika identifieraren för principen. Den här identifieraren måste föregås av *B2C_1A_* |
| PublicPolicyUri | Ja | En URI för principen, som är en kombination av klient-ID och princip-ID. |
| DeploymentMode | Nej | Möjliga värden: `Production`, `Debugging`, eller `Development`. `Production` används som standard. Använd den här egenskapen för att felsöka principen. Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Nej | Den slut punkt som används när **DeploymentMode** har angetts till `Development`. Värdet måste vara `urn:journeyrecorder:applicationinsights`. Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md). |


I följande exempel visas hur du anger **TrustFrameworkPolicy** -elementet:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Arvs modell

Dessa typer av principfiler används vanligt vis i en användar resa:

- En **bas** fil som innehåller de flesta av definitionerna. Om du vill ha hjälp med att felsöka och långsiktigt underhåll av dina principer, rekommenderar vi att du gör ett minsta antal ändringar i den här filen.
- En **tilläggs** fil som innehåller de unika konfigurations ändringarna för din klient. Den här princip filen är härledd från bas filen. Använd den här filen för att lägga till nya funktioner eller åsidosätta befintliga funktioner. Använd till exempel den här filen för att federera med nya identitets leverantörer.
- En **förlitande parts fil (RP)** som är den enda aktivitets fokuserade filen som anropas direkt av den förlitande parten, t. ex. dina webb-, mobil-eller Skriv bords program. Varje unik aktivitet, till exempel registrering eller inloggning, återställning av lösen ord eller profil redigering, kräver en egen RP-princip fil. Den här princip filen är härledd från tilläggs filen.

Ett förlitande part-program anropar RP-principagenten för att köra en specifik uppgift. Till exempel för att initiera inloggnings flödet. Ramverket för identitets upplevelse i Azure AD B2C lägger till alla element först från bas filen och sedan från tilläggs filen och slutligen från RP-princip filen för att sammanställa den aktuella principen. Element av samma typ och namn i RP-filen åsidosätter dessa element i tilläggen och tilläggen åsidosätter bas. I följande diagram visas relationen mellan principfiler och de förlitande part programmen.

![Diagram som visar förtroende ramverkets princip arvs modell](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Arvs modellen ser ut så här:

- Den överordnade principen och den underordnade principen är av samma schema.
- Den underordnade principen på alla nivåer kan ärva från den överordnade principen och utöka den genom att lägga till nya element.
- Det finns ingen gräns för antalet nivåer.

Mer information finns i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Grundläggande princip

Om du vill ärva en princip från en annan princip måste ett **BasePolicy** -element deklareras under **TrustFrameworkPolicy** -elementet i princip filen. **BasePolicy** -elementet är en referens till den grundläggande princip som den här principen härleds från.

**BasePolicy** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | --------|
| TenantId | 1:1 | ID för din Azure AD B2C klient. |
| `PolicyId` | 1:1 | Identifierare för den överordnade principen. |


I följande exempel visas hur du anger en bas princip. Den här **B2C_1A_TrustFrameworkExtensions** -principen härleds från **B2C_1A_TrustFrameworkBase** -principen.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Princip körning

Ett förlitande parts program, till exempel ett webb-, mobil-eller Skriv bords program, anropar principen för förlitande [part (RP)](relyingparty.md). RP-principagenten kör en speciell uppgift, som att logga in, återställa ett lösen ord eller redigera en profil. RP-principen konfigurerar listan över anspråk som programmet för förlitande part tar emot som en del av den token som utfärdas. Flera program kan använda samma princip. Alla program får samma token med anspråk, och användaren går igenom samma användar resa. Ett enda program kan använda flera principer.

I RP-princip filen anger du **DefaultUserJourney** -elementet, som pekar på [UserJourney](userjourneys.md). Användar resan definieras vanligt vis i bas-eller tilläggs principen.

B2C_1A_signup_signin-princip:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase eller B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

En användar resa definierar affärs logiken för vad en användare går igenom. Varje användar resa är en uppsättning Dirigerings steg som utför en serie åtgärder i ordningsföljd för autentiserings-och informations insamling.

**SocialAndLocalAccounts** -princip filen i startpaketet innehåller SignUpOrSignIn, ProfileEdit, PasswordReset original användar resor. [](active-directory-b2c-get-started-custom.md#custom-policy-starter-pack) Du kan lägga till fler användar resor för andra scenarier, till exempel ändra en e-postadress eller länka och ta bort länkar till ett socialt konto.

Orchestration-stegen kan anropa en [teknisk profil](technicalprofiles.md). En teknisk profil är ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter. En teknisk profil kan till exempel utföra dessa åtgärder bland andra:

- Återge en användar upplevelse.
- Tillåt att användare loggar in med sociala eller ett företags konto, till exempel Facebook, Microsoft-konto, Google, Salesforce eller någon annan identitets leverantör.
- Konfigurera telefon verifiering för MFA.
- Läsa och skriva data till och från ett Azure AD B2C identitets lager.
- Anropa en anpassad RESTful-API-tjänst.

![Diagram som visar körnings flödet för principen](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** -elementet innehåller följande element:

- BasePolicy som anges ovan
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
