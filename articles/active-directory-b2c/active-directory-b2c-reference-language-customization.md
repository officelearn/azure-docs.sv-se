---
title: "Med hjälp av språk anpassning - Azure AD B2C | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: Med språket anpassning

>[!NOTE]
>Den här funktionen är tillgänglig som förhandsversion.
>

Språk anpassning kan din princip för olika språk efter kundens behov.  Microsoft tillhandahåller översättningar för 36 språk (se [ytterligare information](#additional-information)), men du kan också tillhandahålla en egen översättningar för språk.  Även om din upplevelse finns bara för ett språk, kan du anpassa all text på sidorna.  

## <a name="how-does-language-customization-work"></a>Hur fungerar språk anpassning?
Anpassning av språk kan du välja vilka språk som användaren-transporten finns i.  När funktionen är aktiverad, kan du ange frågesträngparametern ui_locales, från ditt program.  När du anropar i Azure AD B2C översätta vi sidan språk som du har angett.  Den här typen av konfigurationen ger fullständig kontroll över språk i dina användare resa och ignorerar språkinställningar för kundens webbläsare. Alternativt kanske du inte behöver nivån kontroll över vilka språk som kunden finns.  Om du inte anger en parameter för ui_locales beror på kundupplevelsen sina webbläsarinställningar.  Du kan fortfarande styra vilka språk som dina användare resa översätts till genom att lägga till som ett språk som stöds.  Om en kund webbläsare är inställd på att visa ett språk som du inte vill stöder, är det språk du valt som standard i stöds kulturer visas i stället.

1. **UI-språk angett språk** -när du har aktiverat språket anpassning användaren-transporten översätts till det språk som anges här
2. **Webbläsaren begärda språket** -om ingen ui-språk har angetts, det innebär att webbläsaren begärda språk, **om den ingick i språk som stöds**
3. **Principen standardspråk** -om webbläsaren inte ange ett språk eller anger en som inte stöds, det innebär att standardspråk för principen

>[!NOTE]
>Om du använder anpassade användarattribut, måste du ange egna översättningar. Se ”[anpassa din strängar](#customize-your-strings)” mer information.
>

## <a name="support-uilocales-requested-languages"></a>Stöd för ui_locales begärda språk 
Principer som har skapats innan språk anpassning allmän tillgänglighet behöver du aktivera funktionen.  Principer som skapas när du har språk anpassning som är aktiverad som standard.  Du kan nu styra språket för transporten användare genom att lägga till parametern ui_locales genom att aktivera språk anpassning på en princip.
1. [Följ dessa steg för att navigera till sidan för B2C-funktioner på Azure-portalen.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navigera till en princip som du vill aktivera för översättningar.
3. Klicka på **språk anpassning**.  
4. Klicka på **aktivera språk anpassning** överst.
5. Läs dialogrutan och klicka på ”Ja”.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Välj vilka språk i användar-transporten har aktiverats 
Aktivera en uppsättning språk för dina användare resa översättas i när ui_locales-parameter inte har angetts.
1. Kontrollera din princip har 'Språk anpassning' har aktiverat från föregående anvisningarna.
2. Från din **Redigera princip** väljer **språk anpassning**.
3. Välj ett språk som du vill stödja.
4. I egenskapsfönstret växla **aktiverad** till Ja.  
5. Klicka på **spara** längst upp i egenskapsfönstret.

>[!NOTE]
>Om en ui_locales-parameter inte har angetts, sedan översätts sidan till kundens webbläsarspråket endast om den är aktiverad
>

## <a name="customize-your-strings"></a>Anpassa din strängar
'Språk anpassning, kan du anpassa valfri sträng i användar-transporten.
1. Kontrollera din princip har 'Språk anpassning' har aktiverat från de föregående anvisningarna.
2. Från din **Redigera princip** väljer **språk anpassning**.
3. Välj det språk som du vill anpassa.
4. Välj sidan som du vill redigera.
5. Klicka på **hämta standardvärden** (eller **hämta åsidosättningar** om du tidigare har redigerat språket). 

Här får du en JSON-fil som du kan använda för att börja redigera din strängar.

### <a name="changing-any-string-on-the-page"></a>Ändra valfri sträng på sidan
1. Öppna JSON-fil som hämtas från föregående anvisningarna i en JSON-redigerare.
2. Hitta de element som du vill ändra.  Du hittar den `StringId` av den sträng som du letar efter eller leta efter den `Value` du vill ändra.
3. Uppdatering av `Value` attribut med vad som ska visas.
4. För varje sträng som du vill ändra, Kom ihåg att växla `Override` till **SANT**.
5. Spara filen och överföra dina ändringar (du hittar kontrollen på samma plats som dit du hämtade JSON-fil). 

>[!IMPORTANT]
>Om du måste åsidosätta en sträng, se till att ange den `Override` värde till `true`.  Om värdet inte är ändras ignoreras posten. 
>

### <a name="changing-extension-attributes"></a>Ändra tilläggsattribut
Om du vill ändra strängen för en anpassad användarattribut eller vill du lägga till ett JSON har följande format:
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

### <a name="using-localizedcollections"></a>Med hjälp av LocalizedCollections
Om du vill ange en fast lista med värden för svar, måste du skapa en `LocalizedCollections`.  En `LocalizedCollections` är en matris med `Name` och `Value` par.  Den `Name` är vad som visas och `Value` är det som returneras i anspråket.  Att lägga till en `LocalizedCollections`, den har följande format:

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

* `ElementId` är användaren attribut som den här `LocalizedCollections` är ett svar på
* `Name` värdet som visas för användaren
* `Value` är det som returneras i anspråket när det här alternativet är markerat

### <a name="upload-your-changes"></a>Ladda upp dina ändringar
1. När du har slutfört ändringarna till JSON-fil, gå tillbaka till din B2C-klient.
2. Från din **Redigera princip** väljer **språk anpassning**.
3. Välj det språk som du vill ge översättningar.
4. Välj sidan som du vill ge översättningar.
5. Klicka på mappikonen och välj JSON-fil som ska överföras.
6. Ändra sparas i principen automatiskt.

## <a name="using-page-ui-customization-with-language-customization"></a>Med språket anpassning sidan anpassning av Användargränssnittet

Det finns två sätt att lokalisera HTML-innehåll.  Genom att aktivera ['Språk anpassning'](active-directory-b2c-reference-language-customization.md).  Den här funktionen aktiveras kan Azure AD B2C att vidarebefordra parametern öppna ID Connect `ui-locales`, till slutpunkten.  Innehållsservern kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är specifika för språket.

Vi kan också hämtar innehåll från olika platser som är baserade på det språk som används.  I din CORS aktiverat slutpunkten måste du ställa in en mappstruktur som värd för innehåll för specifika språk och vi ringer upp dig rätt om du placerar jokertecknet, `{Culture:RFC5646}`.  Till exempel om jag har detta som min egen sidan URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Kan jag läsa in sidan i `fr` och när den dra HTML- och css innehåll, det hämtar från:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Anpassade språkinställningar

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för.  Du måste ange översättningar för alla strängar i principen.

1. Från din **Redigera princip** väljer **språk anpassning**.
2. Välj **lägga till anpassade språk** från sidans överkant.
3. I fönstret som öppnas för kontexten identifiera vilket språk som du tillhandahåller översättningar för genom att ange en giltig platskoden.
4. Du kan hämta en uppsättning åsidosättningar för engelska och arbeta med översättningar för varje sida.
5. När du är klar med JSON-filer, kan du överföra dem för varje sida.
6. Välj **aktivera** och din princip visas nu det här språket för dina användare.
7. Kom ihåg att spara språk när du har aktiverat den.

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Page UI anpassning etiketter är beständiga som den första uppsättningen med åsidosättningar när 'Språk anpassning' har aktiverats
När du aktiverar 'Språk anpassning' finns tidigare ändringar för etiketter med Page UI-anpassning kvar i en JSON-fil för engelska (en).  Du kan fortsätta att ändra etiketterna och andra strängar genom att överföra språkresurser i 'Språk anpassning'.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft strävar efter att tillhandahålla den senaste översättningar för användning
Vi kontinuerligt förbättra översättningar och lagra dem på efterlevnad för dig.  Kommer att identifiera buggar och ändringar i globala terminologi och göra de uppdateringar som kommer att fungera sömlöst i användar-transporten.
### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk
Vi för närvarande inte har stöd för höger-till-vänster-språk, om du behöver den här funktionen Ta rösta för den här funktionen på [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Sociala identitet providern översättningar
Vi ger parametern ui_locales OIDC sociala inloggningar men de hanteras inte av vissa sociala identitetsleverantörer, inklusive Facebook och Google. 
### <a name="browser-behavior"></a>Webbläsarens beteende
Chrome och Firefox både begäran för sina Ange språk och om det är ett språk som stöds, visas innan.  Gräns för närvarande begär inte ett språk och går direkt till standardspråk.

### <a name="what-languages-are-supported"></a>Vilka språk som stöds?

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
| Gujarati              | gu            |
| Hindi                 | hi            |
| Kroatiska              | tim            |
| ungerska             | hu            |
| Italienska               | it            |
| Japanska              | ja            |
| Kannada               | kn            |
| Koreanska                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
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
| Turkiska               | tr            |
| Kinesiska – förenklad  | zh-hans       |
| Kinesiska – traditionell | zh-hant       |
