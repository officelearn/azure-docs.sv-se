---
title: Anpassa användargränssnittet
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användar gränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949414"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassa användar gränssnittet i Azure Active Directory B2C

Anpassning och anpassning av användar gränssnittet som Azure Active Directory B2C (Azure AD B2C) visar för dina kunder och ger en smidig användar upplevelse i ditt program. Dessa upplevelser omfattar registrering, inloggning, profil redigering och återställning av lösen ord. I den här artikeln beskrivs metoder för anpassning av användar gränssnittet för både användar flöden och anpassade principer.

## <a name="ui-customization-in-different-scenarios"></a>Anpassning av gränssnitt i olika scenarier

Det finns flera sätt att anpassa användar gränssnittet för användaren som använder ditt program, varje lämplig för olika scenarier.

### <a name="user-flows"></a>Användarflöden

Om du använder [användar flöden](active-directory-b2c-reference-policies.md)kan du ändra utseendet på dina användar flödes sidor genom att använda inbyggda *mallar*för sidlayout, eller genom att använda din egen HTML och CSS. Båda metoderna beskrivs längre fram i den här artikeln.

Du kan använda [Azure Portal](tutorial-customize-ui.md) för att konfigurera anpassningen av gränssnittet för användar flöden.

### <a name="custom-policies"></a>Anpassade principer

Om du använder [anpassade principer](active-directory-b2c-overview-custom.md) för att tillhandahålla registrering eller inloggning, lösen ords återställning eller profil redigering i programmet använder [du principfiler för att anpassa användar gränssnittet](active-directory-b2c-ui-customization-custom.md).

Om du behöver tillhandahålla dynamiskt innehåll baserat på kundens beslut kan du använda anpassade principer som kan [ändra sid innehållet dynamiskt](active-directory-b2c-ui-customization-custom-dynamic.md) beroende på en parameter som skickas i en frågesträng. Du kan till exempel ändra bakgrunds bilden på Azure AD B2C registrerings-eller inloggnings sida baserat på en parameter som du skickar från ditt webb program eller mobil program.

### <a name="javascript"></a>JavaScript

Du kan aktivera JavaScript-kod på klient sidan i både [användar flöden](user-flow-javascript-overview.md) och [anpassade principer](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Logga endast in-UI-anpassning

Om du bara tillhandahåller inloggning, tillsammans med den medföljande sidan för lösen ords återställning och verifierings-e-post, använder du samma anpassnings steg som används för en [inloggnings sida för Azure AD](../active-directory/fundamentals/customize-branding.md).

Om kunderna försöker redigera sin profil innan de loggar in, omdirigeras de till en sida som du anpassar med samma steg som används för att anpassa inloggnings sidan för Azure AD.

## <a name="page-layout-templates"></a>Mallar för sidlayout

Användar flöden innehåller flera inbyggda mallar som du kan välja mellan för att ge dina användar upplevelse sidor ett professionellt utseende. Dessa mallar kan också fungera som utgångs punkt för din egen anpassning.

Välj **sidlayouter** under **Anpassa** på den vänstra menyn och välj sedan **mall**.

![Listruta för val av mall på sidan användar flöde i Azure Portal](media/customize-ui-overview/template-selection.png)

Välj sedan en mall i listan. Här följer några exempel på inloggnings sidorna för varje mall:

| Havet, blå | Platta, grå | Klassisk |
|:-:|:-:|:-:|
|![Exempel på en blå mall i Atlanten som återges på inloggnings Sidan](media/customize-ui-overview/template-ocean-blue.png)|![Exempel på den bakgrunds grå mall som återges på inloggnings Sidan](media/customize-ui-overview/template-slate-gray.png)|![Exempel på en klassisk mall som återges på inloggnings Sidan](media/customize-ui-overview/template-classic.png)|

När du väljer en mall tillämpas den valda layouten på alla sidor i ditt användar flöde och URI: n för varje sida visas i fältet URI för **anpassad sida** .

## <a name="custom-html-and-css"></a>Anpassad HTML och CSS

Azure AD B2C kör kod i kundens webbläsare med hjälp av en metod som kallas [CORS (Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/).

Vid körning läses innehållet in från en URL som du anger i ditt användar flöde eller en anpassad princip. Varje sida i användar gränssnittet läser in innehållet från den URL som du anger för sidan. När innehållet har lästs in från din URL sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

Läs följande rikt linjer innan du använder dina egna HTML-och CSS-filer för att anpassa användar gränssnittet:

- Azure AD B2C **sammanfogar** HTML-innehåll till dina sidor. Kopiera inte och försök att ändra standard innehållet som Azure AD B2C tillhandahåller. Det är bäst att bygga HTML-innehåll från grunden och använda standard innehållet som referens.
- **Java Script** kan inkluderas i ditt anpassade innehåll för både [användar flöden](user-flow-javascript-overview.md) och [anpassade principer](javascript-samples.md).
- **Webb läsar versioner** som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42,0 och senare
  - Mozilla Firefox 38,0 och senare
- Ta inte med **formulär Taggar** i din HTML-kod. Formulär taggarna stör de POST åtgärder som genereras av HTML-koden som infogas av Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Var lagrar jag GRÄNSSNITTs innehåll?

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet kan du vara värd för ditt GRÄNSSNITTs innehåll på en offentligt tillgänglig HTTPS-slutpunkt som stöder CORS. Till exempel [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webb servrar, CDN, AWS S3 eller fildelnings system.

Den viktiga punkten är att du är värd för innehållet på en offentligt tillgänglig HTTPS-slutpunkt med CORS aktiverat. Du måste använda en absolut URL när du anger den i ditt innehåll.

## <a name="get-started-with-custom-html-and-css"></a>Kom igång med anpassad HTML och CSS

Kom igång med din egen HTML och CSS på sidorna med användar upplevelsen genom att följa dessa rikt linjer.

- Skapa välformulerat HTML-innehåll med ett tomt `<div id="api"></div>`-element någonstans i `<body>`. Det här elementet anger var Azure AD B2C innehållet infogas. I följande exempel visas en minimal sida:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Använd CSS för att formatera de GRÄNSSNITTs element som Azure AD B2C infogar på sidan. I följande exempel visas en enkel CSS-fil som även innehåller inställningar för inmatade HTML-element för registrering:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Vara värd för ditt innehåll på en HTTPS-slutpunkt (med CORS tillåtet). Du måste aktivera både GET-och option Request-metoderna när du konfigurerar CORS.
- Skapa eller redigera ett användar flöde eller en anpassad princip för att använda det innehåll som du har skapat.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-fragment från Azure AD B2C

I följande tabell visas de HTML-fragment som Azure AD B2C sammanfogas i det `<div id="api"></div>`-element som finns i ditt innehåll.

| Infogad sida | Beskrivning av HTML |
| ------------- | ------------------- |
| Val av identitetsprovider | Innehåller en lista över knappar för identitets leverantörer som kunden kan välja från vid registrering eller inloggning. Dessa knappar inkluderar sociala identitets leverantörer som Facebook, Google eller lokala konton (baserat på e-postadress eller användar namn). |
| Registrera lokalt konto | Innehåller ett formulär för registrering av lokalt konto baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, till exempel text inmatnings ruta, rutan lösen ords post, alternativ knapp, list rutor med flera val och kryss rutor med flera val. |
| Registrering av socialt konto | Kan visas när du registrerar dig med ett befintligt konto från en social identitetsprovider som Facebook eller Google. Den används när ytterligare information måste samlas in från kunden med hjälp av ett registrerings formulär. |
| Enhetlig registrering eller inloggning | Hanterar både registrering och inloggning av kunder som kan använda sociala identitets leverantörer som Facebook, Google eller lokala konton. |
| Multifaktorautentisering | Kunder kan verifiera sina telefonnummer (med text eller röst) under registreringen eller inloggningen. |
| Fel | Tillhandahåller fel information till kunden. |

## <a name="localize-content"></a>Lokalisera innehåll

Du lokaliserar ditt HTML-innehåll genom att aktivera [språk anpassning](active-directory-b2c-reference-language-customization.md) i Azure AD B2C klient organisationen. När du aktiverar den här funktionen kan Azure AD B2C vidarebefordra OpenID Connect-parametern `ui-locales` till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på det språk som används. I din CORS-aktiverade slut punkt ställer du in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}`.

Din anpassade sid-URI kan till exempel se ut så här:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Du kan läsa in sidan på franska genom att dra innehåll från:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exempel

Du kan hitta flera exempel mallfiler i [B2C-AzureBlobStorage-client-](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) lagringsplatsen på GitHub.

HTML-och CSS-exempelfilerna i mallarna finns i katalogen [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Nästa steg

- Om du använder **användar flöden**kan du börja anpassa ditt användar gränssnitt med själv studie kursen:

    [Anpassa användar gränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md).
- Om du använder **anpassade principer**kan du börja anpassa användar gränssnittet med artikeln:

    [Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
