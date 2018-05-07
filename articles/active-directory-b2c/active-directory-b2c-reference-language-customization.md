---
title: Anpassning av språk i Azure AD B2C | Microsoft Docs
description: Lär dig mer om hur du anpassar språk-upplevelse.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 097033b78e3e4f640e7bf4008fd970c53315d5d7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Anpassning av språk i Azure Active Directory B2C

Anpassning av språk i Azure Active Directory B2C (Azure AD B2C) gör att din princip för olika språk efter kundens behov.  Microsoft tillhandahåller översättningar för [36 språk](#supported-languages), men du kan också tillhandahålla en egen översättningar för språk. Även om din upplevelse tillhandahålls för bara ett språk kan du anpassa all text på sidorna.  

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning
Du kan använda språk anpassning för att välja vilka språk som användaren-transporten finns i. När funktionen är aktiverad, kan du ange frågesträngparametern, `ui_locales`, från ditt program. När du anropar i Azure AD B2C översätts sidan det språk som du har angett. Den här typen av konfigurationen ger fullständig kontroll över språk i dina användare resa och ignorerar språkinställningar för kundens webbläsare. 

Du kan inte ha nivån kontroll över vilka språk som kunden ser. Om du inte anger en `ui_locales` parameter, kundens upplevelse styrs av sina webbläsarinställningar.  Du kan fortfarande styra vilka språk som dina användare resa översätts till genom att lägga till som ett språk som stöds. Om en kund webbläsare är inställd på att visa ett språk som du inte vill att stödja, är det språk som du valt som standard i stöds kulturer visas i stället.

- **UI-språk angett språk**: när du har aktiverat språket anpassning användaren-transporten översätts till det språk som anges här.
- **Webbläsaren begärda språket**: Om ingen `ui_locales` angavs, användaren-transporten översätts till språket som begärs av webbläsaren *om språket stöds*.
- **Principen standardspråk**: om webbläsaren inte ange ett språk eller anger en som inte stöds, användaren transporten översätts till standardspråk för principen.

>[!NOTE]
>Om du använder anpassade användarattribut, måste du ange egna översättningar. Mer information finns i [anpassa din strängar](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Stöd för begärda språk för ui_locales 
Principer som har skapats innan allmänna tillgängligheten för anpassning av språk måste du aktivera funktionen. Principerna som skapats efter att ha språk anpassning som är aktiverad som standard. 

När du aktiverar språk anpassning på en princip du kan styra språket för transporten användare genom att lägga till den `ui_locales` parameter.
1. [Gå till sidan för B2C-funktioner på Azure portal](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Bläddra till en princip som du vill aktivera för översättningar.
3. Välj **språk anpassning**.  
4. Välj **aktivera språk anpassning**.
5. Läs informationen i dialogrutan och välj **Ja**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Välj vilka språk i användar-transporten har aktiverats 
Aktivera en uppsättning språk för dina användare vägen till översättas till begäran av webbläsaren utan den `ui_locales` parameter.
1. Kontrollera att principen har språk anpassning aktiverat från föregående anvisningarna.
2. Från den **Redigera princip** väljer **språk anpassning**.
3. Välj ett språk som du vill stödja.
4. I egenskapsfönstret ändra **aktiverad** till **Ja**.  
5. Välj **spara** längst upp i egenskapsfönstret.

>[!NOTE]
>Om en `ui_locales` parameter har inte angetts, sidan översätts till kundens webbläsarspråket endast om den är aktiverad.
>

## <a name="customize-your-strings"></a>Anpassa din strängar
Anpassning av språk kan du anpassa valfri sträng i användar-transporten.
1. Kontrollera att principen har språk anpassning aktiverat från de föregående anvisningarna.
2. Från den **Redigera princip** väljer **språk anpassning**.
3. Välj det språk som du vill anpassa.
4. Välj sidan som du vill redigera.
5. Välj **hämta standardvärden** (eller **hämta åsidosättningar** om du tidigare har redigerat språket). 

Här får du en JSON-fil som du kan använda för att börja redigera din strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan
1. Öppna JSON-fil som hämtas från föregående anvisningarna i en JSON-redigerare.
2. Hitta det element som du vill ändra.  Du kan hitta `StringId` för strängen som du letar efter eller leta efter den `Value` attribut som du vill ändra.
3. Uppdatering av `Value` attribut med vad som ska visas.
4. Varje sträng som du vill ändra, ändra `Override` till `true`.
5. Spara filen och överföra dina ändringar. (Du hittar kontrollen på samma plats som dit du hämtade JSON-filen.) 

>[!IMPORTANT]
>Om du måste åsidosätta en sträng, se till att ange den `Override` värde till `true`.  Om värdet inte är ändras ignoreras posten. 
>

### <a name="change-extension-attributes"></a>Ändra tilläggsattribut
Om du vill ändra strängen för en anpassad användarattribut, eller om du vill lägga till ett JSON, är det i följande format:
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

Ersätt `<ExtensionAttribute>` med namnet på din anpassade attribut.  

Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Ange en lista med värden genom att använda LocalizedCollections
Om du vill ange en fast lista med värden för svar, måste du skapa en `LocalizedCollections` attribut.  `LocalizedCollections` är en matris med `Name` och `Value` par. Ordningen för objekten kommer att den ordning de visas.  Att lägga till `LocalizedCollections`, Använd följande format:

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

* `ElementId` är användaren attribut som den här `LocalizedCollections` attributet är ett svar på.
* `Name` är det värde som visas för användaren.
* `Value` är det som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Ladda upp dina ändringar
1. När du har slutfört ändringarna till JSON-fil kan du gå tillbaka till din B2C-klient.
2. Från den **Redigera princip** väljer **språk anpassning**.
3. Välj det språk som du vill ge.
4. Välj sidan där du vill ange översättningar.
5. Välj mappikonen och välj JSON-fil som ska överföras.
 
Ändringarna sparas automatiskt i principen.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sidan användargränssnitt med språket anpassning

Det finns två sätt att lokalisera HTML-innehåll. Ett sätt är att aktivera [språk anpassning](active-directory-b2c-reference-language-customization.md). Den här funktionen aktiveras kan Azure AD B2C att vidarebefordra parametern öppna ID Connect `ui-locales`, till slutpunkten.  Innehållsservern kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som språkspecifika.

Alternativt kan du hämtar innehåll från olika platser som är baserade på det språk som används. Du kan ställa in en mappstruktur som värd för innehåll för specifika språk i din CORS-aktiverad slutpunkt. Du måste anropa rätt om du använder jokertecknet `{Culture:RFC5646}`.  Anta exempelvis att det här är en anpassad sida URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Du kan läsa in sidan i `fr`. När sidan hämtar HTML- och CSS innehåll, det dra från:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägg till anpassade språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningar för alla strängar i principen.  Språk och nationella koder är begränsade till dem i ISO 639-1-standarden. 

1. Från den **Redigera princip** väljer **språk anpassning**.
2. Välj **lägga till anpassade språk** från sidans överkant.
3. Identifiera vilka språk du ger översättningar för genom att ange en giltig platskoden i fönstret som öppnas för kontext.
4. Du kan hämta en uppsättning åsidosättningar för engelska och arbeta med översättningar för varje sida.
5. När du är klar med JSON-filer kan du överföra dem för varje sida.
6. Välj **aktivera**, och din princip visas nu det här språket för dina användare.
7. Spara språket.

>[!IMPORTANT]
>Du måste aktivera anpassade språk eller ladda upp åsidosättningar för den innan du kan spara.
>

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Page UI-anpassning etiketter som åsidosättningar
När du aktiverar språk anpassning beständig tidigare ändringar för etiketter med hjälp av sidan anpassningar i en JSON-fil för engelska (en). Du kan fortsätta att ändra etiketterna och andra strängar genom att överföra språkresurser i språk anpassning.
### <a name="up-to-date-translations"></a>Uppdaterade översättningar
Microsoft strävar efter att tillhandahålla den senaste översättningar för din användning. Microsoft förbättrar översättningar kontinuerligt och lagras de i kompatibilitet för dig. Microsoft kommer identifiera buggar och ändringar i globala terminologi och göra uppdateringar som kommer att fungera sömlöst i användar-transporten.
### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk
Microsoft har inte för närvarande stöd för höger-till-vänster-språk. Du kan göra detta med hjälp av anpassade språkinställningar och använder CSS för att ändra det sätt som visas.  Om du behöver den här funktionen kan du rösta [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Sociala identitet providern översättningar
Microsoft tillhandahåller den `ui_locales` OIDC parametern på sociala inloggningar. Men vissa sociala identitetsleverantörer, inklusive Facebook och Google, respektera inte dem. 
### <a name="browser-behavior"></a>Webbläsarens beteende
Chrome och Firefox både begäran för sitt set-språk. Om det är ett språk som stöds, visas den innan du standardvärdet. Gräns för närvarande begär inte ett språk och går direkt till standardspråk.

### <a name="supported-languages"></a>Språk som stöds

| Språk              | Språkkod |
|-----------------------|---------------|
| Bengali                | bn            |
| tjeckiska                 | cs            |
| Danska                | da            |
| Tyska                | de            |
| Grekiska                 | el            |
| Svenska               | en            |
| Spanska               | es            |
| Finska               | fi            |
| Franska                | fr            |
| Gujarati              | Gu            |
| Hindi                 | Hej            |
| Kroatiska              | tim            |
| ungerska             | hu            |
| Italienska               | it            |
| Japanska              | ja            |
| Kannada               | kn            |
| Koreanska                | ko            |
| Malayalam             | ml            |
| Marathi               | MR            |
| Malajiska                 | ms            |
| Norska bokmål      | nb            |
| Nederländska                 | nl            |
| Punjabi               | pa            |
| Polska                | pl            |
| Portugisiska – Brasilien   | pt-br         |
| Portugisiska – Portugal | pt-pt         |
| Rumänska              | ro            |
| Ryska               | ru            |
| Slovakiska                | Sk            |
| Svenska               | sv            |
| Tamilska                 | ta            |
| Telugu                | te            |
| Thai                  | TH            |
| Turkiska               | TR            |
| Kinesiska – förenklad  | zh-hans       |
| Kinesiska – traditionell | zh-hant       |
