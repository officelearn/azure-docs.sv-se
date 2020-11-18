---
title: Språk anpassning i Azure Active Directory B2C
description: Lär dig mer om att anpassa språk upplevelsen i dina användar flöden i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2c39d9809204328a6eba30c073b1fa81795924a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840295"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Språk anpassning i Azure Active Directory B2C

Med språk anpassning i Azure Active Directory B2C (Azure AD B2C) kan ditt användar flöde anpassa olika språk så att de passar dina kund behov. Microsoft tillhandahåller översättningarna för [36-språk](#supported-languages), men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse bara är avsedd för ett enda språk kan du anpassa valfri text på sidorna.

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning

Du kan använda språk anpassning för att välja vilka språk som ditt användar flöde är tillgängligt i. När funktionen är aktive rad kan du ange frågesträngparametern, `ui_locales` från ditt program. När du anropar Azure AD B2C översätts sidan till det språk som du har angett. Den här typen av konfiguration ger dig fullständig kontroll över språken i ditt användar flöde och ignorerar språk inställningarna i kundens webbläsare.

Du kanske inte behöver den kontroll nivån över vilka språk som din kund ser. Om du inte anger en `ui_locales` parameter, styrs kundens upplevelse av webbläsarens inställningar. Du kan fortfarande styra vilka språk som ditt användar flöde ska översättas till genom att lägga till det som ett språk som stöds. Om en kunds webbläsare är inställd på att visa ett språk som du inte vill ha stöd för, visas det språk som du valde som standard i en kultur som stöds i stället.

* **UI – lokala språk**: när du har aktiverat språk anpassning översätts ditt användar flöde till det språk som anges här.
* **Webbläsare-begärt språk**: om ingen `ui_locales` parameter har angetts översätts ditt användar flöde till det begärda språket, *om språket stöds*.
* **Standard språk för principen**: om webbläsaren inte anger något språk, eller om det anger ett språk som inte stöds, översätts användar flödet till standard språket för användar flödet.

> [!NOTE]
> Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa dina strängar](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Stöd för begärda språk för ui_locales

Principer som skapades innan den allmänna tillgängligheten för språk anpassning måste aktivera den här funktionen först. Principer och användar flöden som har skapats efter att språk anpassning har Aktiver ATS som standard.

När du aktiverar språk anpassning för ett användar flöde kan du kontrol lera språket för användar flödet genom att lägga till `ui_locales` parametern.

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj **Aktivera språk anpassning**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Välj vilka språk i ditt användar flöde som är aktiverade

Aktivera en uppsättning språk som ditt användar flöde ska översättas till när den begärs av webbläsaren utan `ui_locales` parametern.

1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
1. På sidan **språk** för användar flödet väljer du ett språk som du vill stödja.
1. I fönstret Egenskaper ändrar du **aktiverat** till **Ja**.
1. Välj **Spara** överst i fönstret Egenskaper.

>[!NOTE]
>Om en `ui_locales` parameter inte anges översätts sidan till kundens webb läsar språk endast om den är aktive rad.
>

## <a name="customize-your-strings"></a>Anpassa dina strängar

Med språk anpassning kan du anpassa en sträng i ditt användar flöde.

1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
1. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
1. Under **filer på sidan filnivå – resurser** väljer du den sida som du vill redigera.
1. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

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

```json
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

Om du vill ange en uppsättning värden för svar måste du skapa ett- `LocalizedCollections` attribut. `LocalizedCollections` är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas. Använd följande format för att lägga till `LocalizedCollections` :

```json
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

* `ElementId` är det användar-attribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name` är värdet som visas för användaren.
* `Value` är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar

1. När du har slutfört ändringarna i JSON-filen går du tillbaka till B2C-klienten.
1. Välj **användar flöden** och klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. Välj den sida där du vill ge översättningar.
1. Välj mappikonen och välj den JSON-fil som ska överföras.

Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sid gränssnittet med hjälp av språk anpassning

Det finns två sätt att lokalisera ditt HTML-innehåll. Ett sätt är att aktivera [språk anpassning](user-flow-language-customization.md). Om du aktiverar den här funktionen kan Azure AD B2C vidarebefordra OpenID Connect-parametern `ui-locales` till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är språkspecifika.

Du kan också hämta innehåll från olika platser baserat på de nationella inställningar som används. I din CORS-aktiverade slut punkt kan du ställa in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}` . Anta till exempel att det här är din anpassade sid-URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Du kan läsa in sidan i `fr` . När sidan hämtar HTML-och CSS-innehåll, hämtas de från:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägg till anpassade språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningarna för alla strängar i användar flödet. Språk-och lands koder är begränsade till dem i ISO 639-1-standarden.

1. Välj **användar flöden** i Azure AD B2C klient.
2. Klicka på det användar flöde där du vill lägga till anpassade språk och klicka sedan på **språk**.
3. Välj **Lägg till anpassat språk** överst på sidan.
4. I kontext fönstret som öppnas identifierar du vilket språk som du tillhandahåller översättningar för genom att ange en giltig språkkod.
5. För varje sida kan du ladda ned en uppsättning åsidosättningar för engelska och arbeta med översättningarna.
6. När du är klar med JSON-filerna kan du ladda upp dem för varje sida.
7. Välj **Aktivera** och ditt användar flöde kan nu visa det här språket för dina användare.
8. Spara språket.

>[!IMPORTANT]
>Du måste antingen aktivera de anpassade språken eller överföra åsidosättningar för det innan du kan spara.

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

Azure AD B2C har stöd för följande språk. Användar flödes språk tillhandahålls av Azure AD B2C. Meddelande språken för Multi-Factor Authentication (MFA) tillhandahålls av [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Språk              | Språkkod | Användarflöden         | MFA-meddelanden  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabiska                | bakre            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Bulgariska             | Bulgarien            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Katalanska               | onlinecertifikatutfärdare            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tjeckiska                 | östasiatisk            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Danska                | da            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tyska                | de            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Grekiska                 | El            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Engelska               | en            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Spanska               | es            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Estniska              | ge            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Baskiska                | gemenskaps            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Finska               | fi            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Franska                | fr            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Galiciska              | huvud            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Hebreiska                | producenten            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Hej            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kroatiska              | tim            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ungerska             | HU            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Indonesiska            | id            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Italienska               | it            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Japanska              | ja            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kazakiska                | kk            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kannada               | 5,0            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Koreanska                | Ko            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Litauiska            | lt            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Lettiska               | LV            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Marathi               | Mr            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Malajiska                 | millisekund            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Norska bokmål      | Anm            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Nederländska                 | nl            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Norska             | nej            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | PA            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Polska                | pl            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Brasilien   | pt-br         | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Portugal | pt-pt         | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Rumänska              | ro            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ryska               | ru            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Slovakiska                | sk            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Slovenska             | SL            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Serbiska (kyrillisk)    | SR-cryl-CS    | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Serbiska (latinsk)       | SR-latn-CS    | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Svenska               | sv            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tamilska                 | layout            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Telugu                | &            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Thailändska                  | i:te            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Turkiska               | TR            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ukrainska             | Storbritannien            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Vietnamesiska            | Vi            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kinesiska (förenklad)  | zh-hans       | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kinesiska (traditionell) | zh-Hant       | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
