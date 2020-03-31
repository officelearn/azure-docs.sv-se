---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Ange contentdefinitions-elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051499"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan anpassa utseendet och känslan hos alla [självsäkra tekniska profiler.](self-asserted-technical-profile.md) Azure Active Directory B2C (Azure AD B2C) kör kod i kundens webbläsare och använder en modern metod som kallas CORS (Cross-Origin Resource Sharing).

Om du vill anpassa användargränssnittet anger du en URL i **ContentDefinition-elementet** med anpassat HTML-innehåll. I den självsäkra tekniska profilen eller **OrchestrationStep**pekar du på den innehållsdefinitionsidentifieraren. Innehållsdefinitionen kan innehålla ett **LocalizedResourcesReferences-element** som anger en lista över lokaliserade resurser som ska läsas in. Azure AD B2C sammanfogar användargränssnittets element med HTML-innehåll som läses in från din URL och visar sedan sidan för användaren.

**ContentDefinitions-elementet** innehåller url:er till HTML5-mallar som kan användas i en användarresa. HTML5-sidans URI används för ett angivet användargränssnittssteg. Till exempel inloggnings- eller registrerings-, lösenordsåterställnings- eller felsidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Du kan skapa nya innehållsdefinitioner efter dina behov. Det här elementet kan innehålla en lokaliserad resursreferens till den lokaliseringsidentifierare som anges i [lokaliseringselementet.](localization.md)

I följande exempel visas innehållsdefinitionsidentifieraren och definitionen av lokaliserade resurser:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadata för den självpåttade tekniska profilen **LocalAccountSignUpWithLogonEmail** innehåller innehållsdefinitionsidentifieraren **ContentDefinitionReferenceId** inställd på`api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>InnehållDefinition

**ContentDefinition-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en innehållsdefinition. Värdet är ett som anges i avsnittet **Innehållsdefinitions-ID** senare på den här sidan. |

ContentDefinition-elementet innehåller följande element: **ContentDefinition**

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| LoadUri (läs) | 1:1 | En sträng som innehåller URL:en för HTML5-sidan för innehållsdefinitionen. |
| ÅtervinningUri | 1:1 | En sträng som innehåller URL:en på HTML-sidan för att visa ett fel som relaterar till innehållsdefinitionen. Värdet används för närvarande `~/common/default_page_error.html`inte och måste vara . |
| DataUri (datauri) | 1:1 | En sträng som innehåller den relativa URL:en för en HTML-fil som ger användaren erfarenhet att anropa för steget. |
| Metadata | 0:1 | En samling nyckel-/värdepar som innehåller de metadata som används av innehållsdefinitionen. |
| LokaliseradeResourcesReferences | 0:1 | En samling lokaliserade resurser refererar till. Använd det här elementet om du vill anpassa lokaliseringen av ett användargränssnitt och anspråksattribut. |

### <a name="datauri"></a>DataUri (datauri)

**DataUri-elementet** används för att ange sididentifieraren. Azure AD B2C använder sididentifieraren för att läsa in och initiera gränssnittselement och JavaScript på klientsidan. Värdets format är `urn:com:microsoft:aad:b2c:elements:page-name:version`. I följande tabell visas de sididentifierare som du kan använda.

| Sididentifierare | Beskrivning |
| ----- | ----------- |
| `globalexception` | Visar en felsida när ett undantag eller ett fel påträffas. |
| `providerselection`, `idpselection` | Visar en lista över de identitetsleverantörer som användarna kan välja mellan under inloggningen.  |
| `unifiedssp` | Visar ett formulär för inloggning med ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Det här värdet ger också "keep me sign-in functionality" och "Glömt ditt lösenord?" Länk. |
| `unifiedssd` | Visar ett formulär för inloggning med ett lokalt konto som baseras på en e-postadress eller ett användarnamn. |
| `multifactor` | Verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| `selfasserted` | Visar ett formulär för att samla in data från en användare. Gör det till exempel möjligt för användare att skapa eller uppdatera sin profil. |

### <a name="select-a-page-layout"></a>Välja en sidlayout

Du kan aktivera [JavaScript-kod på klientsidan](javascript-samples.md) genom att `contract` infoga mellan `elements` och sidtypen. Till exempel `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

[Versionsdelen](page-layout.md) `DataUri` av anger paketet med innehåll som innehåller HTML, CSS och JavaScript för användargränssnittselementen i principen. Om du tänker aktivera JavaScript-kod på klientsidan måste de element som du baserar JavaScript på vara oföränderliga. Om de inte är oföränderliga kan eventuella ändringar orsaka oväntat beteende på dina användarsidor. Om du vill förhindra dessa problem genomdvingar du användningen av en sidlayout och anger en sidlayoutversion. Om du gör det säkerställs att alla innehållsdefinitioner som du har baserat ditt JavaScript på är oföränderliga. Även om du inte tänker aktivera JavaScript måste du fortfarande ange sidlayoutversionen för dina sidor.

I följande exempel visas `selfasserted` **DataUri** av version: `1.2.0`

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrera till sidlayout

Värdets format måste innehålla `contract`ordet : _urn:com:microsoft:aad:b2c:elements:**kontrakt**:page-name:version_. Om du vill ange en sidlayout i dina anpassade principer som använder ett gammalt **DataUri-värde** använder du följande tabell för att migrera till det nya formatet.

| Gammalt DataUri-värde | Nytt DataUri-värde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metadata

Ett **metadataelement** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0:n | Metadata som relaterar till innehållsdefinitionen. |

Elementet **Objekt** i **elementet Metadata** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Metadatanyckeln.  |

#### <a name="metadata-keys"></a>Metadatanycklar

Innehållsdefinitionen stöder följande metadataobjekt:

| Nyckel | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DisplayName | Inga | En sträng som innehåller namnet på innehållsdefinitionen. |

### <a name="localizedresourcesreferences"></a>LokaliseradeResourcesReferences

Elementet **LocalizedResourcesReferences** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| LokaliseradResourcesReference | 1:n | En lista över lokaliserade resursreferenser för innehållsdefinitionen. |

Elementet **LocalizedResourcesReference** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Språk | Ja | En sträng som innehåller ett språk som stöds för principen per RFC 5646 - Taggar för att identifiera språk. |
| LokaliseradResourcesReferenceId | Ja | Identifieraren för elementet **LocalizedResources.** |

I följande exempel visas en definition av registrering eller inloggning med en hänvisning till lokalisering för engelska, franska och spanska:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Mer information om hur du lägger till lokaliseringsstöd i innehållsdefinitionerna finns i [Lokalisering](localization.md).

## <a name="content-definition-ids"></a>ID-skivor för innehållsdefinition

ContentDefinition-elementets **ContentDefinition** ID-attribut anger vilken typ av sida som relaterar till innehållsdefinitionen. Elementet definierar den kontext som en anpassad HTML5/CSS-mall ska tillämpas. I följande tabell beskrivs den uppsättning innehållsdefinitions-ID:n som känns igen av Identity Experience Framework och de sidtyper som relaterar till dem. Du kan skapa egna innehållsdefinitioner med ett godtyckligt ID.

| ID | Standardmall | Beskrivning |
| -- | ---------------- | ----------- |
| **api.error api.error api.error api.** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida** - Visar en felsida när ett undantag eller ett fel påträffas. |
| **api.idpselections api.idpselections api.idpselections api.** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Urvalssida för identitetsprovider** – visar identitetsleverantörer som användarna kan välja mellan under inloggningen. Alternativen är vanligtvis företagsidentitetsleverantörer, sociala identitetsleverantörer som Facebook och Google+ eller lokala konton. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitetsproviderval för registrering** – Visar identitetsleverantörer som användarna kan välja mellan under registreringen. Alternativen är vanligtvis företagsidentitetsleverantörer, sociala identitetsleverantörer som Facebook och Google+ eller lokala konton. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sidan Glömt lösenord** - Visar ett formulär som användarna måste fylla i för att initiera en återställning av lösenord. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Inloggningssida för lokalt konto** – Visar ett formulär för inloggning med ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla en textinmatningsruta och en inmatningsruta för lösenord. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för lokalt konto** – visar ett formulär för att registrera dig för ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika inmatningskontroller, till exempel: en textinmatningsruta, en inmatningsruta för lösenord, en alternativknapp, listrutor med enkel markeringar och kryssrutor med flera markeringar. |
| **api.phonefactor (api.phonefactor)** | [multifaktor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multifaktorautentiseringssida** – Verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för sociala konton** – Visar ett formulär som användarna måste fylla i när de registrerar sig med hjälp av ett befintligt konto från en leverantör av social identitet. Den här sidan liknar den föregående registreringssidan för sociala konton, förutom fälten för lösenordsinmatning. |
| **api.selfasserted.profileupdate api.selfasserted.profileupdate api.selfasserted.profileupdate api.** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan Profiluppdatering** - Visar ett formulär som användarna kan komma åt för att uppdatera sin profil. Den här sidan liknar registreringssidan för sociala konton, förutom fälten för lösenordsinmatning. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrerings- eller inloggningssida** - Hanterar användarens registrerings- och inloggningsprocess. Användare kan använda företagsidentitetsleverantörer, sociala identitetsleverantörer som Facebook eller Google+ eller lokala konton. |

## <a name="next-steps"></a>Nästa steg

Ett exempel på hur du anpassar användargränssnittet med hjälp av innehållsdefinitioner finns i:

[Anpassa användargränssnittet i ditt program med hjälp av en anpassad princip](custom-policy-ui-customization.md)
