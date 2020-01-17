---
title: ContentDefinitions - Azure Active Directory B2C | Microsoft Docs
description: Ange ContentDefinitions-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ce564767fe9664604687d8cbaced58507e6b8b3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119660"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan anpassa utseendet på en [egen, kontrollerad teknisk profil](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) kör kod i kundens webbläsare och använder en modern metod som kallas CORS (Cross-Origin Resource Sharing).

Om du vill anpassa användar gränssnittet anger du en URL i **ContentDefinition** -elementet med anpassat HTML-innehåll. I den självkontrollerade tekniska profilen eller **OrchestrationStep**pekar du på den innehålls definitions identifieraren. Innehålls definitionen kan innehålla ett **LocalizedResourcesReferences** -element som anger en lista över lokaliserade resurser som ska läsas in. Azure AD B2C sammanfogar användargränssnittets element med HTML-innehåll som läses in från din URL och visar sedan sidan för användaren.

**ContentDefinitions** -elementet innehåller URL: er till HTML5-mallar som kan användas i en användar resa. HTML5-sidans URI används för ett angivet användar gränssnitts steg. Till exempel inloggnings-eller registrerings-, lösen ords återställning eller fel sidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Du kan skapa nya innehålls definitioner efter dina behov. Det här elementet kan innehålla en lokaliserad resurs referens till lokaliserings identifieraren som anges i [lokaliserings](localization.md) elementet.

I följande exempel visas innehålls definitions identifieraren och definitionen av lokaliserade resurser:

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

Metadata för den **LocalAccountSignUpWithLogonEmail** självkontrollerade tekniska profilen innehåller ID för innehålls definitions **ContentDefinitionReferenceId** som är inställt på `api.localaccountsignup`

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


## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en innehålls definition. Värdet är ett angivet i avsnittet **innehålls Definitions-ID** senare på den här sidan. |

**ContentDefinition** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | En sträng som innehåller webb adressen till HTML5-sidan för innehålls definitionen. |
| RecoveryUri | 0:1 | En sträng som innehåller URL: en för HTML-sidan för att visa ett fel som rör innehålls definitionen. |
| DataUri | 1:1 | En sträng som innehåller den relativa URL: en för en HTML-fil som ger användar upplevelsen för att anropa steget. |
| Metadata | 1:1 | En samling nyckel/värde-par som innehåller de metadata som används av innehålls definitionen. |
| LocalizedResourcesReferences | 0:1 | En samling med lokaliserade resurs referenser. Använd det här elementet för att anpassa lokaliseringen av ett användar gränssnitt och ett anspråks attribut. |

### <a name="datauri"></a>DataUri

**DataUri** -elementet används för att ange sid-ID. Azure AD B2C använder sid identifieraren för att läsa in och initiera GRÄNSSNITTs element och Java Script på klient sidan. Värdets format är `urn:com:microsoft:aad:b2c:elements:page-name:version`.  I följande tabell visas de sid identifierare som du kan använda.

| Värde |   Beskrivning |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Visar en felsida när ett undantag eller ett fel påträffas. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Visar en lista över identitets leverantörer som användarna kan välja bland under inloggningen. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Visar ett formulär för att logga in med ett lokalt konto baserat på en e-postadress eller ett användar namn. Det här värdet ger även "Behåll mig inloggnings funktioner" och "glömt ditt lösen ord?" Operationsföljdslänkkod. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Visar ett formulär för att logga in med ett lokalt konto baserat på en e-postadress eller ett användar namn. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Visar ett formulär som gör det möjligt för användare att skapa eller uppdatera sin profil. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | En lista med lokaliserade resurs referenser för innehålls definitionen. |

**LocalizedResourcesReferences** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Språk | Ja | En sträng som innehåller ett språk som stöds för principen per RFC 5646-taggar för att identifiera språk. |
| LocalizedResourcesReferenceId | Ja | Identifieraren för **LocalizedResources** -elementet. |

I följande exempel visas en innehålls definition för registrering eller inloggning:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

I följande exempel visas en registrerings-eller inloggnings innehålls definition med en referens till lokalisering av engelska, franska och spanska:

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

Information om hur du lägger till lokaliserings stöd i dina innehålls definitioner finns i [lokalisering](localization.md).

## <a name="content-definition-ids"></a>ID för innehålls definition

ID-attributet för **ContentDefinition** -elementet anger vilken typ av sida som relaterar till innehålls definitionen. Elementet definierar den kontext som en anpassad HTML5/CSS-mall ska använda. I följande tabell beskrivs de olika innehålls Definitions-ID: n som identifieras av identitets miljö ramverket och de sidtyper som är relaterade till dem. Du kan skapa egna innehålls definitioner med ett godtyckligt ID.

| ID | Standardmall | Beskrivning |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida** – visar en felsida när ett undantag eller ett fel påträffas. |
| **API. idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sidan Val av identitetsprovider** – visar en lista över identitets leverantörer som användarna kan välja bland under inloggningen. Alternativen är vanligt vis företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Val av identitetsprovider för registrering** – visar en lista över identitets leverantörer som användarna kan välja bland vid registreringen. Alternativen är vanligt vis företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sidan glömt lösen ord** – visar ett formulär som användarna måste utföra för att initiera en lösen ords återställning. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Inloggnings sida för lokalt konto** – visar ett formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla text rutorna text rutor och lösen ord. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sida för lokalt konto** – visar ett formulär för att registrera ett lokalt konto baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, t. ex. en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. |
| **API. phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multi-Factor Authentication** – verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sida för socialt konto** – visar ett formulär som användarna måste slutföra när de registrerar sig genom att använda ett befintligt konto från en social identitetsprovider. Den här sidan liknar inloggnings sidan för det föregående sociala kontot, förutom fälten för lösen ords inmatning. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan profil uppdatering** – visar ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| **api.signuporsignin** | [Unified. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrering eller inloggnings sida** – hanterar användarens registrerings-och inloggnings process. Användare kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton. |

