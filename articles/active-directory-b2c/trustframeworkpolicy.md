---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Ange det TrustFrameworkPolicy elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d54d4eccc89313a8e109d1598078cdb1cc5d7a14
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836737"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar element i principen, till exempel anspråk schemat, anspråksomvandlingar, innehållsdefinitioner, Anspråksproviders, tekniska profiler, användarresa och orchestration-steg. Varje principfilen definieras i den översta **TrustFrameworkPolicy** element i en fil. 

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


Den **TrustFrameworkPolicy** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Schemaversion som ska användas för att köra principen. Värdet måste vara `0.3.0.0` |
| TenantObjectId | Nej | Unikt objekt-ID för Azure Active Directory (Azure AD) B2C-klient. |
| TenantId | Ja | Den unika identifieraren för den klient som den här principen tillhör. |
| PolicyId | Ja | Den unika identifieraren för principen. Den här identifieraren måste föregås av *B2C_1A_* |
| PublicPolicyUri | Ja | URI för principen som är kombination av klient-ID och princip-ID. |
| DeploymentMode | Nej | Möjliga värden: `Production`, `Debugging`, eller `Development`. `Production` används som standard. Använd den här egenskapen för att felsöka din princip. Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Nej | Den slutpunkt som används när **DeploymentMode** är inställd på `Development`. Värdet måste vara `urn:journeyrecorder:applicationinsights`. Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md). |


I följande exempel visas hur du anger den **TrustFrameworkPolicy** element:

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

Dessa typer av principfiler används vanligtvis i en användarresa:

- En **Base** -fil som innehåller de flesta av definitionerna. För att hjälpa till med felsökning och långsiktig underhåll av dina principer, rekommenderar vi att du gör ett minsta antal ändringar i den här filen.
- En **tillägg** filen som innehåller de unika konfigurationsändringarna för din klient. Den här principfil härleds från bas-filen. Använd den här filen för att lägga till nya funktioner eller åsidosätta befintliga funktioner. Till exempel använda den här filen för att federera med nya Identitetsproviders.
- En **förlitande part (RP)** -fil som enskild uppgift designmiljöer-fil som anropas direkt av förlitande part-programmet, till exempel dina webb-, Mobil- eller skrivbordsprogram program. Varje unik aktivitet, till exempel återställning av lösenord för registrering eller inloggning, eller profilredigering, kräver en egen princip RP-fil. Den här principfil härleds från filen tillägg. 

Ett förlitande partsprogram anropar RP principfil för att köra en viss uppgift. Till exempel för flödet för inloggning. Identitetsramverk i Azure AD B2C lägger till alla element först från bas-filen och sedan från filen tillägg och slutligen från RP principfil sätta ihop den aktuella principen gäller. Delar av samma typ och namn i RP-filen åsidosätter de olika delarna i tillägg och tillägg åsidosättningar Base. Följande diagram visar relationen mellan principfilerna och förlitande parters program.

![Arvsmodell](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Arv modellen är följande:

- Den överordnade och underordnade är av samma schema.
- Underordnade principen på alla nivåer kan ärva från den överordnade principen och utöka det genom att lägga till nya element.
- Det finns ingen gräns för hur många nivåer.

Mer information finns i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Basprincipen

Att ärva en princip från en annan princip, en **BasePolicy** elementet måste deklareras den **TrustFrameworkPolicy** elementet i principfilen. Den **BasePolicy** element är en referens till basprincipen som den här principen härleds.  

Den **BasePolicy** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifierare för din Azure AD B2C-klient. |
| PolicyId | 1:1 | Identifierare för den överordnade principen. |


I följande exempel visas hur du anger en grundläggande princip. Detta **B2C_1A_TrustFrameworkExtensions** princip härleds från den **B2C_1A_TrustFrameworkBase** princip. 

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

## <a name="policy-execution"></a>Principkörning

Ett förlitande partsprogram, till exempel ett webb-, Mobil- eller skrivbordsprogram program, anropar den [förlitande part (RP) principen](relyingparty.md). Principfilen som RP utför en viss uppgift, till exempel inloggning, återställa ett lösenord eller redigering av profil. Princip för RP konfigurerar listan över förlitande part-programmet tar emot anspråk som en del av den token som utfärdas. Flera program kan använda samma princip. Alla program får samma token med anspråk och användaren går igenom samma användarresa. Ett program kan använda flera principer.

I filen RP-princip som du anger den **DefaultUserJourney** element som pekar på den [UserJourney](userjourneys.md). Användarresan definieras vanligtvis i principen Base eller tillägg.

B2C_1A_signup_signin princip:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase eller B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

En användarresa definierar vad en användare går igenom affärslogik. Varje användarresan är en uppsättning orchestration-steg som utför en rad åtgärder, i följd i information om autentisering och samling. 

Den **SocialAndLocalAccounts** principfil i den [startpaket](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) innehåller SignUpOrSignIn, ProfileEdit, PasswordReset användaren resor. Du kan lägga till fler användare utbildning för en annan scenarier, till exempel ändra en e-postadress, länka och ta bort länken ett socialt konto eller återställa ett lösenord. 

Orchestration-steg kan anropa en [tekniska profilen](technicalprofiles.md). Tekniska profilen ger ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter. Tekniska profilen kan exempelvis utföra följande åtgärder bland annat:

- Rendera en användarupplevelse.
- Tillåt användare att logga in med social eller en enterprise-konto, till exempel Facebook, Microsoft-konto, Google, Salesforce eller någon annan identitetsleverantören.
- Ställ in telefonverifiering för MFA.
- Läsa och skriva data till och från en Azure AD B2C-identitetsarkiv.
- Anropa en anpassad Restful-API-tjänst.

![Principkörning](./media/trustframeworkpolicy/custom-policy-execution.png)

 Den **TrustFrameworkPolicy** elementet innehåller följande element:

- BasePolicy som anges ovan
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)

