---
title: Språk anpassning i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du anpassar språk upplevelsen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5dd82cbb8370fd4c3ee0dca6a9acd0046c73c0ef
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716799"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Språk anpassning i Azure Active Directory B2C

Med språk anpassning i Azure Active Directory B2C (Azure AD B2C) kan ditt användar flöde anpassa olika språk så att de passar dina kund behov.  Microsoft tillhandahåller översättningarna för [36-språk](#supported-languages), men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse bara är avsedd för ett enda språk kan du anpassa valfri text på sidorna.

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning
Du kan använda språk anpassning för att välja vilka språk som ditt användar flöde är tillgängligt i. När funktionen är aktive rad kan du ange `ui_locales`frågesträngparametern, från ditt program. När du anropar Azure AD B2C översätts sidan till det språk som du har angett. Den här typen av konfiguration ger dig fullständig kontroll över språken i ditt användar flöde och ignorerar språk inställningarna i kundens webbläsare.

Du kanske inte behöver den kontroll nivån över vilka språk som din kund ser. Om du inte anger en `ui_locales` parameter, styrs kundens upplevelse av webbläsarens inställningar.  Du kan fortfarande styra vilka språk som ditt användar flöde ska översättas till genom att lägga till det som ett språk som stöds. Om en kunds webbläsare är inställd på att visa ett språk som du inte vill ha stöd för, visas det språk som du valde som standard i en kultur som stöds i stället.

- **UI – lokala språk**som har angetts: När du har aktiverat språk anpassning översätts ditt användar flöde till det språk som anges här.
- **Webbläsare – begärt språk**: Om ingen `ui_locales` parameter har angetts översätts ditt användar flöde till det begärda språket för webbläsaren, *om språket stöds*.
- **Standard språk för princip**: Om webbläsaren inte anger något språk, eller om det anger en som inte stöds, översätts användar flödet till standard språket för användar flödet.

>[!NOTE]
>Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa dina strängar](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Stöd för begärda språk för ui_locales
Principer som skapades innan den allmänna tillgängligheten för språk anpassning måste aktivera den här funktionen först. Principer och användar flöden som har skapats efter att språk anpassning har Aktiver ATS som standard.

När du aktiverar språk anpassning för ett användar flöde kan du kontrol lera språket för användar flödet genom att lägga till `ui_locales` parametern.
1. Välj **användar flöden**i Azure AD B2C klient.
2. Klicka på det användar flöde som du vill aktivera för översättningar.
3. Välj **språk**.
4. Välj **Aktivera språk anpassning**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Välj vilka språk i ditt användar flöde som är aktiverade
Aktivera en uppsättning språk som ditt användar flöde ska översättas till när den begärs av webbläsaren utan `ui_locales` parametern.
1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
2. På sidan **språk** för användar flödet väljer du ett språk som du vill stödja.
3. I fönstret Egenskaper ändrar du **aktiverat** till **Ja**.
4. Välj **Spara** överst i fönstret Egenskaper.

>[!NOTE]
>Om en `ui_locales` parameter inte anges översätts sidan till kundens webb läsar språk endast om den är aktive rad.
>

## <a name="customize-your-strings"></a>Anpassa dina strängar
Med språk anpassning kan du anpassa en sträng i ditt användar flöde.
1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
2. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
3. Under **filer på sidan filnivå – resurser**väljer du den sida som du vill redigera.
4. Välj **Hämta** standardvärden (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera dina strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan
1. Öppna JSON-filen som hämtats från tidigare instruktioner i en JSON-redigerare.
2. Hitta det element som du vill ändra.  Du kan söka `StringId` efter den sträng som du söker eller leta efter det `Value` attribut som du vill ändra.
3. `Value` Uppdatera attributet med det du vill visa.
4. Ändra `Override` till`true`för varje sträng som du vill ändra.
5. Spara filen och överför ändringarna. (Du kan hitta överförings kontrollen på samma plats som du laddade ned JSON-filen.)

>[!IMPORTANT]
>Om du behöver åsidosätta en sträng, se till att ange `Override` värdet till. `true`  Om värdet inte ändras ignoreras posten.
>

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
Om du vill ange en uppsättning värden för svar måste du skapa ett `LocalizedCollections` -attribut.  `LocalizedCollections`är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas.  Använd följande `LocalizedCollections`format för att lägga till:

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

* `ElementId`är det användar-attribut som `LocalizedCollections` det här attributet är ett svar på.
* `Name`är värdet som visas för användaren.
* `Value`är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar
1. När du har slutfört ändringarna i JSON-filen går du tillbaka till B2C-klienten.
2. Välj **användar flöden** och klicka på det användar flöde som du vill aktivera för översättningar.
3. Välj **språk**.
4. Välj det språk som du vill översätta till.
5. Välj den sida där du vill ge översättningar.
6. Välj mappikonen och välj den JSON-fil som ska överföras.

Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sid gränssnittet med hjälp av språk anpassning

Det finns två sätt att lokalisera ditt HTML-innehåll. Ett sätt är att aktivera [språk anpassning](active-directory-b2c-reference-language-customization.md). Om du aktiverar den här funktionen kan Azure AD B2C vidarebefordra OpenID Connect- `ui-locales`parametern till din slut punkt.  Din innehålls Server kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är språkspecifika.

Du kan också hämta innehåll från olika platser baserat på de nationella inställningar som används. I din CORS-aktiverade slut punkt kan du ställa in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}`.  Anta till exempel att det här är din anpassade sid-URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Du kan läsa in sidan i `fr`. När sidan hämtar HTML-och CSS-innehåll, hämtas de från:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägg till anpassade språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningarna för alla strängar i användar flödet.  Språk-och lands koder är begränsade till dem i ISO 639-1-standarden.

1. Välj **användar flöden**i Azure AD B2C klient.
2. Klicka på det användar flöde där du vill lägga till anpassade språk och klicka sedan på **språk**.
3. Välj **Lägg till anpassat språk** överst på sidan.
4. I kontext fönstret som öppnas identifierar du vilket språk som du tillhandahåller översättningar för genom att ange en giltig språkkod.
5. För varje sida kan du ladda ned en uppsättning åsidosättningar för engelska och arbeta med översättningarna.
6. När du är klar med JSON-filerna kan du ladda upp dem för varje sida.
7. Välj **Aktivera**och ditt användar flöde kan nu visa det här språket för dina användare.
8. Spara språket.

>[!IMPORTANT]
>Du måste antingen aktivera de anpassade språken eller överföra åsidosättningar för det innan du kan spara.
>

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Sid gränssnitt anpassnings etiketter som åsidosättningar
När du aktiverar språk anpassning behålls dina tidigare redigeringar av etiketter med anpassning av sid gränssnitt i en JSON-fil för engelska (en). Du kan fortsätta att ändra dina etiketter och andra strängar genom att överföra språk resurser i språk anpassning.
### <a name="up-to-date-translations"></a>Aktuella översättningar
Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar kontinuerligt översättningarna och ser till att de är kompatibla. Microsoft kommer att identifiera buggar och ändringar i global terminologi och göra uppdateringar som fungerar sömlöst i ditt användar flöde.
### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk
Microsoft tillhandahåller för närvarande inte stöd för språk som skrivs från höger till vänster. Du kan göra detta med hjälp av anpassade språk och använda CSS för att ändra hur strängarna visas.  Om du behöver den här funktionen kan du rösta på [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Översättningar av sociala identitets leverantörer
Microsoft tillhandahåller `ui_locales` OIDC-parametern för sociala inloggningar. Men vissa sociala identitets leverantörer, inklusive Facebook och Google, följer inte dem.
### <a name="browser-behavior"></a>Webb läsar beteende
Chrome och Firefox båda förfrågningarna om deras inställda språk. Om det är ett språk som stöds visas det före standardvärdet. Microsoft Edge begär för närvarande inte ett språk och går direkt till standard språket.

### <a name="supported-languages"></a>Språk som stöds

| Språk              | Språkkod |
|-----------------------|---------------|
| Bangla                | bn            |
| Tjeckiska                 | cs            |
| Danska                | da            |
| Tyska                | de            |
| Grekiska                 | el            |
| Svenska               | sv-SE            |
| Spanska               | es            |
| Finska               | fi            |
| Franska                | fr            |
| Gujarati              | gu            |
| Hindi                 | Hej            |
| Kroatiska              | tim            |
| Ungerska             | hu            |
| Italienska               | it            |
| Japanska              | ja            |
| Kannada               | 5,0            |
| Koreanska                | ko            |
| Malayalam             | ml            |
| Marathi               | Mr            |
| Malajiska                 | ms            |
| Norska bokmål      | nb            |
| Nederländska                 | nl            |
| Punjabi               | PA            |
| Polska                | pl            |
| Portugisiska (Brasilien)   | pt-br         |
| Portugisiska-Portugal | pt-pt         |
| Rumänska              | ro            |
| Ryska               | ru            |
| Slovakiska                | sk            |
| Svenska               | sv            |
| Tamilska                 | layout            |
| Telugu                | &            |
| Thai                  | :e            |
| Turkiska               | TR            |
| Kinesiska – Förenklad  | zh-hans       |
| Kinesiska (traditionell) | zh-hant       |
