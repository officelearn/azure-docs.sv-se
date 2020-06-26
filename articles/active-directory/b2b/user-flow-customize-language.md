---
title: Språk anpassning i Azure AD-användar flöden
description: Lär dig mer om att anpassa språk upplevelsen i dina användar flöden.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 856338e6c5a2bac55fee57d3ea09575a1a4d14ed
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386546"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Språk anpassning i Azure Active Directory (för hands version)
|     |
| --- |
| Självbetjänings registrering är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.|
|     |

Med språk anpassning i Azure Active Directory (Azure AD) kan ditt användar flöde hantera olika språk som passar dina användares behov. Microsoft tillhandahåller översättningarna för [36-språk](#supported-languages). Även om din upplevelse bara är avsedd för ett enda språk, kan du anpassa attributnamnet på sidan attribut samling.

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning

Som standard är språk anpassning aktive rad för användare som registrerar sig för att säkerställa en konsekvent registrerings upplevelse. Du kan använda språk för att ändra de strängar som visas för användare som en del av processen för insamling av attribut under registreringen.

> [!NOTE]
> Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa dina strängar](#customize-your-strings).

## <a name="customize-your-strings"></a>Anpassa dina strängar

Med språk anpassning kan du anpassa en sträng i ditt användar flöde.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster**väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden (förhands granskning)**.
3. Välj det användar flöde som du vill aktivera för översättningar.
4. Välj **språk**.
5. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
6. **Sidan expandera attribut samling**.
7. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera dina strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan

1. Öppna JSON-filen som hämtats från tidigare instruktioner i en JSON-redigerare.
1. Hitta det element som du vill ändra. Du kan söka efter `StringId` den sträng som du söker eller leta efter det `Value` attribut som du vill ändra.
1. Uppdatera `Value` attributet med det du vill visa.
1. Ändra till för varje sträng som du vill ändra `Override` `true` .
1. Spara filen och överför ändringarna. (Du kan hitta överförings kontrollen på samma plats som du laddade ned JSON-filen.)

> [!IMPORTANT]
> Om du behöver åsidosätta en sträng, se till att ange `Override` värdet till `true` . Om värdet inte ändras ignoreras posten.

### <a name="change-extension-attributes"></a>Ändra attribut för tillägg

Om du vill ändra strängen för ett anpassat användarattribut, eller om du vill lägga till ett i JSON, är det i följande format:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Ersätt `<ExtensionAttribute>` med namnet på ditt anpassade användar-attribut.

Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Ange en lista med värden med hjälp av LocalizedCollections

Om du vill ange en uppsättning värden för svar måste du skapa ett- `LocalizedCollections` attribut. `LocalizedCollections`är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas. Använd följande format för att lägga till `LocalizedCollections` :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`är det användar-attribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name`är värdet som visas för användaren.
* `Value`är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar

1. När du har slutfört ändringarna i JSON-filen går du tillbaka till klienten.
1. Välj **användar flöden** och klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. **Sidan Välj attribut samling**.
1. Välj mappikonen och välj den JSON-fil som ska överföras.

Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Sid gränssnitt anpassnings etiketter som åsidosättningar

När du aktiverar språk anpassning behålls dina tidigare redigeringar av etiketter med anpassning av sid gränssnitt i en JSON-fil för engelska (en). Du kan fortsätta att ändra dina etiketter och andra strängar genom att överföra språk resurser i språk anpassning.

### <a name="up-to-date-translations"></a>Aktuella översättningar

Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar kontinuerligt översättningarna och ser till att de är kompatibla. Microsoft kommer att identifiera buggar och ändringar i global terminologi och göra uppdateringar som fungerar sömlöst i ditt användar flöde.

### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk

Microsoft tillhandahåller för närvarande inte stöd för språk som skrivs från höger till vänster. Du kan göra detta med hjälp av anpassade språk och använda CSS för att ändra hur strängarna visas. Om du behöver den här funktionen kan du rösta på [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Översättningar av sociala identitets leverantörer

Microsoft tillhandahåller `ui_locales` OIDC-parametern för sociala inloggningar. Men vissa sociala identitets leverantörer, inklusive Facebook och Google, följer inte dem.

### <a name="browser-behavior"></a>Webb läsar beteende

Chrome och Firefox båda förfrågningarna om deras inställda språk. Om det är ett språk som stöds visas det före standardvärdet. Microsoft Edge begär för närvarande inte ett språk och går direkt till standard språket.

## <a name="supported-languages"></a>Språk som stöds

Azure AD innehåller stöd för följande språk. Användar flödes språk tillhandahålls av Azure AD. Meddelande språken för Multi-Factor Authentication (MFA) tillhandahålls av [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

| Språk              | Språkkod | Användarflöden         | MFA-meddelanden  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabiska                | bakre            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Bulgariska             | Bulgarien            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Katalanska               | onlinecertifikatutfärdare            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Tjeckiska                 | östasiatisk            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Danska                | da            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Tyska                | de            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Grekiska                 | El            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Engelska               | en            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Spanska               | ES            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Estniska              | ge            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Baskiska                | gemenskaps            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Finska               | fi            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Franska                | fr            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Galiciska              | huvud            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Hebreiska                | producenten            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Hindi                 | Hej            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kroatiska              | tim            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Ungerska             | HU            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Indonesiska            | id            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Italienska               | it            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Japanska              | ja            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kazakiska                | kk            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kannada               | 5,0            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Koreanska                | Ko            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Litauiska            | långsiktiga            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Lettiska               | LV            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Marathi               | Mr            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Malajiska                 | millisekund            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Norska bokmål      | Anm            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Nederländska                 | nl            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Norska             | nej            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Punjabi               | PA            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Polska                | pl            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Portugisiska – Brasilien   | pt-br         | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Portugisiska – Portugal | pt-pt         | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Rumänska              | ro            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Ryska               | ru            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Slovakiska                | sk            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Slovenska             | SL            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Serbiska-kyrilliska    | SR-cryl-CS    | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Serbiska-Latin       | SR-latn-CS    | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Svenska               | sv            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Tamilska                 | layout            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Telugu                | &            | ![ja](./media/user-flow-customize-language/yes.png) | ![nej](./media/user-flow-customize-language/no.png) |
| Thailändska                  | i:te            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Turkiska               | TR            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Ukrainska             | Storbritannien            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Vietnamesiska            | Vi            | ![nej](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kinesiska – Förenklad  | zh-hans       | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kinesiska (traditionell) | zh-Hant       | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |