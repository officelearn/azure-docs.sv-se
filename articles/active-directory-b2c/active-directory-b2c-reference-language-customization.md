---
title: Språkanpassning i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om hur du anpassar den språk-upplevelsen.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5bf7cc199fc8a439b5071faa89cce2e3ed47a75
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724323"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Språkanpassning i Azure Active Directory B2C

Språkanpassning i Azure Active Directory B2C (Azure AD B2C) gör att ditt användarflöde för olika språk så att den passar dina kunders behov.  Microsoft tillhandahåller översättningar för [36 språk](#supported-languages), men du kan också ange dina egna översättningar för alla språk. Även om din upplevelse har angetts för bara ett enda språk och kan du anpassa text på sidor.  

## <a name="how-language-customization-works"></a>Så här fungerar språkanpassning
Du kan använda språkanpassning för att välja vilka språk som ditt användarflöde finns i. När funktionen är aktiverad, kan du ange frågesträngparametern `ui_locales`, från ditt program. När du anropar i Azure AD B2C, översätts sidan till det språk som du har angett. Den här typen av konfiguration ger dig fullständig kontroll över språk i ditt användarflöde och ignorerar språkinställningarna för kundens webbläsare. 

Du kanske inte behöver den här nivån av kontroll över vilka språk som kunderna ser. Om du inte anger en `ui_locales` parametern kundens upplevelse styrs av sina webbläsarinställningar.  Du kan fortfarande styra vilka språk som ditt användarflöde översätts till genom att lägga till det som ett språk som stöds. Om en kund webbläsare är inställd på att visa ett språk som du inte vill att stödja, är det språk som du valt som standard i kulturer som stöds visas i stället.

- **UI-språk angett språk**: när du har aktiverat språkanpassning ditt användarflöde översätts till det språk som anges här.
- **Webbläsaren begärt språk**: Om ingen `ui_locales` angavs, ditt användarflöde översätts till webbläsaren begärt-språket *om språket stöds*.
- **Standardspråk för principen**: om webbläsaren inte anger ett språk eller anger en som inte stöds, användarflödet översätts till användarens standardspråk för flödet.

>[!NOTE]
>Om du använder anpassade användarattribut, måste du ange dina egna översättningar. Mer information finns i [anpassa din strängar](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Stöd för begärda språk för ui_locales 
Principerna som skapats innan den allmänna tillgängligheten för språkanpassning måste du aktivera den här funktionen först. Principer och användarflöden som skapades när du har språkanpassning aktiverat som standard. 

När du aktiverar språkanpassning på ett användarflöde, du kan styra användarflödet språk genom att lägga till den `ui_locales` parametern.
1. I din Azure AD B2C-klient väljer **användarflöden**.
2. Klicka på det användarflöde som du vill aktivera för översättningar.
3. Välj **språk**.  
4. Välj **aktivera språkanpassning**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Välj vilka språk i ditt användarflöde ska aktiveras 
Aktivera ett antal språk för ditt användarflöde för att översättas till vid förfrågan från webbläsaren utan den `ui_locales` parametern.
1. Kontrollera att ditt användarflöde har språkanpassning aktiverat från föregående instruktioner.
2. På den **språk** för användarflödet, Välj ett språk som du vill stödja.
3. I egenskapsfönstret ändrar **aktiverad** till **Ja**.  
4. Välj **spara** överst i egenskapsfönstret.

>[!NOTE]
>Om en `ui_locales` parametern inte anges, sidan översätts till kundens webbläsarspråket endast om den är aktiverad.
>

## <a name="customize-your-strings"></a>Anpassa din strängar
Språkanpassning kan du anpassa valfri sträng i ditt användarflöde.
1. Kontrollera att ditt användarflöde har språkanpassning aktiverat från de föregående anvisningarna.
2. På den **språk** för användarflödet, Välj det språk som du vill anpassa.
3. Under **sidan Utjämna resurser filer**, Välj den sida som du vill redigera.
4. Välj **hämta standardvärden** (eller **hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera din strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan
1. Öppna JSON-fil som hämtats från föregående anvisningarna i en JSON-redigerare.
2. Hitta elementet som du vill ändra.  Du kan hitta `StringId` för strängen som du letar efter eller leta efter den `Value` attribut som du vill ändra.
3. Uppdatera den `Value` attributet med vad som ska visas.
4. För varje sträng som du vill ändra, ändra `Override` till `true`.
5. Spara filen och överföra dina ändringar. (Du hittar kontrollen på samma plats som där du laddade ned JSON-filen.) 

>[!IMPORTANT]
>Om du vill åsidosätta en sträng kan du se till att ange den `Override` värde att `true`.  Om värdet har inte ändrats, ignoreras posten. 
>

### <a name="change-extension-attributes"></a>Ändra tilläggsattribut
Om du vill ändra strängen för en anpassad användarattribut, eller om du vill lägga till en till JSON, är det i följande format:
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

Ersätt `<ExtensionAttribute>` med namnet på din anpassade användarattribut.  

Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Tillhandahåll en lista med värden genom att använda LocalizedCollections
Om du vill ange en fast lista med värden för svar, måste du skapa en `LocalizedCollections` attribut.  `LocalizedCollections` är en matris med `Name` och `Value` par. Ordningen för objekten blir den ordning som de visas.  Att lägga till `LocalizedCollections`, använder du följande format:

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

* `ElementId` är du attributet att denna `LocalizedCollections` attributet är ett svar på.
* `Name` är det värde som visas för användaren.
* `Value` är det som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar
1. När du har slutfört ändringarna till JSON-fil kan du gå tillbaka till din B2C-klient.
2. Välj **användarflöden** och klicka på det användarflöde som du vill aktivera för översättningar.
3. Välj **språk**.
4. Välj det språk som du vill översätta till.
5. Välj sidan där du vill ange översättningar.
6. Välj mappikonen och välj JSON-fil att ladda upp.
 
Ändringarna sparas automatiskt i ditt användarflöde.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sidan Användargränssnittet med hjälp av språkanpassning

Det finns två sätt att lokalisera din HTML-innehåll. Ett sätt är att aktivera [språkanpassning](active-directory-b2c-reference-language-customization.md). Den här funktionen aktiveras kan Azure AD B2C att vidarebefordra parametern öppna ID Connect `ui-locales`, till slutpunkten.  Innehållsservern kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som språkspecifika.

Du kan också hämta innehåll från olika platser baserat på det språk som används. Du kan ställa in en mappstruktur som värd för innehåll för specifika språk i din CORS-aktiverad slutpunkt. Du ringer upp rätt om du använder jokertecknet `{Culture:RFC5646}`.  Anta exempelvis att det här är din URI för anpassad sida:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Du kan läsa in sidan i `fr`. När sidan hämtar HTML och CSS innehåll, det hämta från:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägg till anpassat språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningar för alla strängar i användarflödet.  Språk och nationella inställningar koder är begränsade till dem i ISO 639-1-standarden. 

1. I din Azure AD B2C-klient väljer **användarflöden**.
2. Klicka på användarflödet där du vill lägga till anpassade språk och klickar sedan på **språk**.
3. Välj **Lägg till anpassat språk** högst upp på sidan.
4. Identifiera vilket språk som du etablerar översättningar för genom att ange en giltig nationalitetskod i fönstret som öppnas för kontexten.
5. För varje sida kan du hämta en uppsättning åsidosättningar för engelska och arbeta med översättningar.
6. När du är klar med JSON-filerna kan överföra du dem för varje sida.
7. Välj **aktivera**, och ditt användarflöde kan nu visa det här språket för dina användare.
8. Spara språk.

>[!IMPORTANT]
>Du måste aktivera anpassade språk eller överför åsidosättningar för den innan du kan spara.
>

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Page UI-anpassning etiketter som åsidosättningar
När du aktiverar språkanpassning sparas tidigare ändringar för etiketter med hjälp av sidan anpassningar i en JSON-fil för engelska (en). Du kan fortsätta att ändra dina etiketter och andra strängar genom att ladda upp språkresurser i språkanpassning.
### <a name="up-to-date-translations"></a>Uppdaterade översättningar
Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar översättningar och behåller dem i kompatibilitet för du kontinuerligt. Microsoft identifiera buggar och ändringarna i globala terminologi och gör uppdateringar som kommer att fungera sömlöst i ditt användarflöde.
### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk
Microsoft innehåller inte för närvarande stöd för höger-till-vänster-språk. Du kan göra detta genom att använda anpassade språkinställningar och med hjälp av CSS för att ändra hur strängarna visas.  Om du behöver den här funktionen kan du rösta [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Sociala providern översättningar
Microsoft tillhandahåller den `ui_locales` OIDC-parametern till sociala inloggningar. Men vissa sociala identitetsleverantörer, inklusive Facebook och Google, respekterar inte dem. 
### <a name="browser-behavior"></a>Beteende för webbläsare
Chrome och Firefox både begära för sina set-språk. Om det är ett språk som stöds, visas den före standard. Gränsen för närvarande begär inte ett språk och går direkt till standardspråket.

### <a name="supported-languages"></a>Språk som stöds

| Språk              | Språkkod |
|-----------------------|---------------|
| Bangla                | bn            |
| Tjeckiska                 | cs            |
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
| Ungerska             | hu            |
| Italienska               | it            |
| Japanska              | ja            |
| Kannada               | KN            |
| Koreanska                | ko            |
| Malayalam             | ml            |
| Marathi               | MR            |
| Malajiska                 | ms            |
| Norska bokmål      | nb            |
| Nederländska                 | nl            |
| Punjabi               | pa            |
| Polska                | pl            |
| Portugisiska - Brasilien   | pt-br         |
| Portugisiska - Portugal | pt-pt         |
| Rumänska              | ro            |
| Ryska               | ru            |
| Slovakiska                | Sk            |
| Svenska               | sv            |
| Tamilska                 | ta            |
| Telugu                | te            |
| Thai                  | :e            |
| Turkiska               | TR            |
| Kinesiska – förenklad  | zh hans       |
| Kinesiska – traditionell | zh-hant       |
