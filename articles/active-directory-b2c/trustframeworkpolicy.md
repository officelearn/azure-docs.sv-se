---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft-dokument
description: Ange elementet TrustFrameworkPolicy i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186394"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar element i principen, till exempel anspråksschema, anspråksomvandlingar, innehållsdefinitioner, anspråksleverantörer, tekniska profiler, användarfärd och orchestration-steg. Varje principfil definieras inom **trustframeworkPolicy-elementet** på den översta nivån i en principfil.

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


**TrustFrameworkPolicy-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Schemaversionen som ska användas för att köra principen. Värdet måste vara`0.3.0.0` |
| TenantObjectId | Inga | Den unika objektidentifieraren för Azure Active Directory B2C-klienten (Azure AD B2C). |
| TenantId | Ja | Den unika identifieraren för klienten som principen tillhör. |
| PolicyId | Ja | Den unika identifieraren för principen. Denna identifierare måste föregås av *B2C_1A_* |
| OffentligpolicyUri | Ja | URI för principen, som är en kombination av klient-ID och princip-ID. |
| DeploymentMode | Inga | Möjliga värden: `Production` `Development`, eller . `Production` används som standard. Använd den här egenskapen för att felsöka din princip. Mer information finns i [Samla in loggar](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Inga | Slutpunkten som används när **DeploymentMode** `Development`är inställd på . Värdet måste `urn:journeyrecorder:applicationinsights`vara . Mer information finns i [Samla in loggar](troubleshoot-with-application-insights.md). |


I följande exempel visas hur du anger **elementet TrustFrameworkPolicy:**

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

## <a name="inheritance-model"></a>Arvsmodell

Dessa typer av principfiler används vanligtvis i en användarfärd:

- En **basfil** som innehåller de flesta definitionerna. Om du vill hjälpa till med felsökning och långsiktigt underhåll av dina principer rekommenderar vi att du gör ett minsta antal ändringar i filen.
- En **fil i tillägg** som innehåller de unika konfigurationsändringarna för din klientorganisation. Den här principfilen härleds från basfilen. Använd den här filen om du vill lägga till nya funktioner eller åsidosätta befintliga funktioner. Använd till exempel den här filen för att federera med nya identitetsleverantörer.
- En **RP-fil (Relying Party)** som är den enda uppgiftsfokuserade filen som anropas direkt av det förlitande partprogrammet, till exempel webb-, mobil- eller skrivbordsprogram. Varje unik uppgift som registrering eller inloggning, återställning av lösenord eller profilredigering kräver en egen RP-principfil. Den här principfilen härleds från filen Tillägg.

Ett program som är beroende av den som är beroende anropar RP-principfilen för att köra en viss uppgift. Om du till exempel vill initiera inloggningsflödet. Identity Experience Framework i Azure AD B2C lägger till alla element först från basfilen och sedan från filen Tillägg och slutligen från RP-principfilen för att montera den aktuella principen som gäller. Element av samma typ och namn i RP-filen åsidosätter dessa element i tilläggen och tillägg åsidosätter base. Följande diagram visar förhållandet mellan principfilerna och de förlitande part-programmen.

![Diagram som visar arvsmodellen för förtroenderamsprincip](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Arvsmodellen är följande:

- Den överordnade principen och den underordnade principen är av samma schema.
- Den underordnade principen på valfri nivå kan ärva från den överordnade principen och utöka den genom att lägga till nya element.
- Det finns ingen gräns för antalet nivåer.

Mer information finns i [Komma igång med anpassade principer](custom-policy-get-started.md).

## <a name="base-policy"></a>Baspolicy

Om du vill ärva en princip från en annan princip måste ett **BasePolicy-element** deklareras under elementet **TrustFrameworkPolicy** i principfilen. **Elementet BasePolicy** är en referens till den basprincip som principen härleds från.

**Elementet BasePolicy** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifieraren för din Azure AD B2C-klient. |
| PolicyId | 1:1 | Identifieraren för den överordnade principen. |


I följande exempel visas hur du anger en basprincip. Denna **B2C_1A_TrustFrameworkExtensions** politik härleds från **B2C_1A_TrustFrameworkBase** politik.

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

## <a name="policy-execution"></a>Körning av principen

Ett förlitande partprogram, till exempel ett webb-, mobil- eller skrivbordsprogram, [anropar principen för den förlitande parten (RP).](relyingparty.md) RP-principfilen kör en viss uppgift, till exempel logga in, återställa ett lösenord eller redigera en profil. RP-principen konfigurerar listan över anspråk som det förlitande partprogrammet tar emot som en del av den token som utfärdas. Flera program kan använda samma princip. Alla program får samma token med anspråk och användaren går igenom samma användarresa. Ett enda program kan använda flera principer.

I principfilen för RP anger du elementet **DefaultUserJourney,** som pekar på [UserJourney](userjourneys.md). Användarfärden definieras vanligtvis i principen Bas eller Tillägg.

B2C_1A_signup_signin politik:

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

En användarresa definierar affärslogiken för vad en användare går igenom. Varje användarfärd är en uppsättning orchestration steg som utför en serie åtgärder, i följd när det gäller autentisering och informationsinsamling.

Principfilen **SocialAndLocalAccounts** i [startpaketet](custom-policy-get-started.md#custom-policy-starter-pack) innehåller användarresorna SignUpOrSignIn, ProfileEdit, PasswordReset. Du kan lägga till fler användarresor för andra scenarier, till exempel ändra en e-postadress eller länka och ta bort länken till ett socialt konto.

Orchestration-stegen kan anropa en [teknisk profil](technicalprofiles.md). En teknisk profil ger ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter. En teknisk profil kan till exempel utföra dessa åtgärder bland annat:

- Återge en användarupplevelse.
- Tillåt användare att logga in med sociala konton eller ett företagskonto, till exempel Facebook, Microsoft-konto, Google, Salesforce eller någon annan identitetsleverantör.
- Konfigurera telefonverifiering för MFA.
- Läs och skriv data till och från ett Azure AD B2C-identitetsarkiv.
- Anropa en anpassad Restful API-tjänst.

![Diagram som visar körningsflödet för principen](./media/trustframeworkpolicy/custom-policy-execution.png)

 Elementet **TrustFrameworkPolicy** innehåller följande element:

- BasePolicy enligt ovan
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
