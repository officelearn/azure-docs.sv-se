---
title: ContentDefinitions - Azure Active Directory B2C | Microsoft Docs
description: Ange det ContentDefinitions elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1fa5688b5895ed7418161234f6af06fecace631c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849387"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan anpassa utseendet på någon [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md). Azure Active Directory (Azure AD) B2C körs koden i din kunds webbläsare och använder en modern lösning som kallas Cross-Origin Resource Sharing (CORS). 

Om du vill anpassa användargränssnittet måste du ange en URL i den **ContentDefinition** element med anpassade HTML-innehåll. I den tekniska profilen självkontrollerad eller **OrchestrationStep**, du pekar på den innehållsdefinition identifieraren. Innehållsdefinitionen kan innehålla en **LocalizedResourcesReferences** element som specificerar en lista med lokaliserade resurser för att läsa in. Azure AD B2C sammanfogar användargränssnittselement med HTML-innehåll som har lästs in från din URL och visar sidan för användaren.

Den **ContentDefinitions** elementet innehåller URL: er till HTML5-mallar som kan användas i en användarresa. Sidan HTML5 URI: N används för en angiven användare gränssnittet steg. Till exempel, logga in eller registrera dig, lösenordsåterställning eller felsidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-fil. Du kan skapa nya innehållsdefinitioner efter dina behov. Det här elementet kan innehålla en referens för lokaliserade resurser, till lokalisering identifierare anges i den [lokalisering](localization.md) element.

I följande exempel visas innehållsdefinition-identifierare och definitionen av lokaliserade resurser:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadata för den **LocalAccountSignUpWithLogonEmail** lokal verifieringsvillkor tekniska profilen innehåller identifieraren innehållsdefinition **ContentDefinitionReferenceId** inställd `api.localaccountsignup`

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

Den **ContentDefinition** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en innehållsdefinition. Värdet är som har angetts i den **innehåll definition ID: N** längre fram i den här sidan. |

Den **ContentDefinition** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | En sträng som innehåller Webbadressen till sidan HTML5 för innehållsdefinitionen. |
| RecoveryUri | 0:1 | En sträng som innehåller URL: en för HTML-sidan för att visa ett fel som rör innehållsdefinitionen. | 
| DataUri | 1:1 | En sträng som innehåller den relativa URL: en för en HTML-fil som innehåller användarupplevelsen att anropa för steget. |  
| Metadata | 1:1 | En samling nyckel/värde-par som innehåller metadata som används av innehållsdefinitionen. | 
| LocalizedResourcesReferences | 0:1 | En samling med lokaliserade resurser referenser. Anpassa lokaliseringen av ett användarattribut för gränssnittet och anspråk med hjälp av det här elementet. |

### <a name="datauri"></a>DataUri

Den **DataUri** elementet används för att ange ID för sidan. Azure AD B2C använder sidan-identifierare för att läsa in och initiera UI-element och JavaScript på klientsidan. Formatet för värdet är `urn:com:microsoft:aad:b2c:elements:page-name:version`.  I följande tabell visas sidan identifierare som du kan använda.

| Värde |   Beskrivning |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Visar en felsida öppnades när ett undantag eller ett fel påträffas. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Visar en lista över identitetsleverantörer som användare kan välja bland under inloggning. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Visar ett formulär för att logga in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Det här värdet innehåller också den ”Håll mig inloggning funktioner” och ”har glömt ditt lösenord”? länk. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Visar ett formulär för att logga in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Visar ett formulär där användare kan skapa eller uppdatera sina profiler. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Den **LocalizedResourcesReferences** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | En lista över referenser för lokaliserad resurs för innehållsdefinitionen. | 

Den **LocalizedResourcesReferences** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Språk | Ja | En sträng som innehåller ett språk som stöds för principen per RFC 5646 - taggar för identifiering av språk. |
| LocalizedResourcesReferenceId | Ja | Identifierare för den **LocalizedResources** element. |

I följande exempel visas en registrering eller inloggning innehållsdefinition:

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

I följande exempel visas en registrering eller inloggning innehållsdefinitionen vid en referens till lokalisering för engelska, franska och spanska:

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

Läs hur du lägger till lokaliseringsstöd innehåll definitionerna i [lokalisering](localization.md).

## <a name="content-definition-ids"></a>Innehållsdefinition ID: N

Attributet ID för den **ContentDefinition** elementet anger vilken typ av sida som relaterar till innehållsdefinitionen. Elementet definierar den kontext som en anpassad mall för HTML5/CSS ska tillämpa. I följande tabell beskriver uppsättningen innehållsdefinition ID: N som kan identifieras av den Identitetsramverk och vilka typer av sidan som är relaterade till dem. Du kan skapa egna innehållsdefinitioner med ett godtyckligt ID.

| ID | Standardmall | Beskrivning | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida** – visar sidan ett fel när ett undantag eller ett fel påträffas. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sida för val av identitet** – visar en lista över identitetsleverantörer som användare kan välja bland under inloggning. Alternativen är vanligtvis enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitets-provider-markeringen för registrering** – visar en lista över identitetsleverantörer som användare kan välja bland under registreringen. Alternativen är vanligtvis enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sida för glömt lösenord** – visar ett formulär som användare måste slutföra för att initiera en lösenordsåterställning. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Lokalt konto på inloggningssidan** – visar ett formulär för att logga in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla ett textinmatningsrutan och lösenordsruta. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för lokalt konto** – visar ett formulär för att registrera dig för ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika indatakontroller, till exempel: indata i en text box, en lösenordsruta, en alternativknapp, flervals-listrutorna och markera kryssrutorna. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multifaktorautentiseringssidan** -verifierar telefonnummer, med hjälp av text eller röst, under registrering eller inloggning. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för socialt konto** – visar ett formulär som användare måste slutföra när de registrerar sig med hjälp av ett befintligt konto från en social identitetsprovider. Den här sidan liknar det föregående sociala Kontoregistrering, förutom inmatningsfält för lösenord. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Uppdatera profilsida** – visar ett formulär som användarna kan nå för att uppdatera sina profiler. Den här sidan liknar den sociala Kontoregistrering, förutom inmatningsfält för lösenord. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Sida för enhetlig registrering eller inloggning** -hanterar processen för användare registrera dig och logga in. Användare kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton. |
 
