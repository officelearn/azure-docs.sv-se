---
title: Om anpassning av användar gränssnitt i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du anpassar användar gränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bbce41271914df4bb8a75804bc66ae538fe766fa
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716753"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Om anpassning av användar gränssnitt i Azure Active Directory B2C

Möjligheten att anpassa användar gränssnittet (UI) som Azure Active Directory (Azure AD) B2C till dina program är viktigt för att ge kunden en sömlös upplevelse. Dessa upplevelser omfattar registrering, inloggning, profil redigering och återställning av lösen ord. Den här artikeln innehåller information som hjälper dig att anpassa användar gränssnittet för dina program.

Beroende på dina behov när det kommer till dessa upplevelser anpassar du användar gränssnittet för ditt program på olika sätt. Exempel:

- Om du använder [användar flöden](active-directory-b2c-reference-policies.md) för att tillhandahålla registrering eller inloggning, lösen ords återställning eller profil redigerings upplevelser i ditt program, använder du [Azure Portal för att anpassa användar gränssnittet](tutorial-customize-ui.md).
- Om du använder ett v2-användarkonto kan du använda en [mall](#page-layout-templates) för sidlayout för att ändra utseendet på dina användar flödes sidor utan ytterligare anpassning. Du kan till exempel använda ett blått blått eller ett bakgrunds grått tema på alla sidor i ditt användar flöde.
- Om du bara tillhandahåller inloggning, dess medföljande lösen ords återställning och e-postverifieringar, använder du samma anpassnings steg som används för en [inloggnings sida för Azure AD](../active-directory/fundamentals/customize-branding.md).
- Om kunderna försöker redigera sin profil innan de loggar in, omdirigeras de till en sida som du anpassar med samma steg som används för att anpassa inloggnings sidan för Azure AD.
- Om du använder [anpassade principer](active-directory-b2c-overview-custom.md) för att tillhandahålla registrering eller inloggning, lösen ords återställning eller profil redigering i ditt program, använder du [principfiler för att anpassa användar gränssnittet](active-directory-b2c-ui-customization-custom.md).
- Om du behöver tillhandahålla dynamiskt innehåll baserat på kundens beslut, använder du [anpassade principer som kan ändra sid innehåll](active-directory-b2c-ui-customization-custom-dynamic.md) beroende på en parameter som skickas i en frågesträng. Bakgrunds bilden på Azure AD B2C registrerings-eller inloggnings sida ändras till exempel baserat på en parameter som du skickar från ditt webb program eller mobil program.
- Du kan aktivera JavaScript-kod på klient sidan i Azure AD B2C [användar flöden](user-flow-javascript-overview.md) eller [anpassade principer](page-layout.md).

Azure AD B2C kör kod i kundens webbläsare och använder ett modernt sätt som kallas [CORS (Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/). Vid körning läses innehållet in från en URL som du anger i ett användar flöde eller en princip. Du anger olika URL: er för olika sidor. När innehållet har lästs in från din URL sammanfogas det med ett HTML-fragment som infogats från Azure AD B2C och sedan visas för din kund.

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet bör du gå igenom följande rikt linjer innan du börjar:

- Azure AD B2C sammanfogar HTML-innehåll till dina sidor. Kopiera inte och försök att ändra standard innehållet som Azure AD B2C tillhandahåller. Det är bäst att bygga HTML-innehåll från grunden och använda standard innehållet som referens.
- Java Script kan nu inkluderas i ditt anpassade innehåll.
- Webb läsar versioner som stöds är:
    - Internet Explorer 11, 10 och Microsoft Edge
    - Begränsat stöd för Internet Explorer 9 och 8
    - Google Chrome 42,0 och senare
    - Mozilla Firefox 38,0 och senare
- Se till att du inte tar med formulär Taggar i din HTML eftersom det stör de POST åtgärder som genererats av den inmatade HTML-koden från Azure AD B2C.

## <a name="page-layout-templates"></a>Mallar för sidlayout

För användar flöden i v2 kan du välja en fördefinierad mall som ger dina standard sidor bättre utseende och fungerar som en bra grund för din egen anpassning.

På den vänstra menyn, under **Anpassa**, väljer du **sidlayouter**. Välj sedan **mall (för hands version)** .

![Listruta för val av mall på sidan användar flöde i Azure Portal](media/customize-ui-overview/template.png)

Välj en mall i listan. Till exempel använder den **blå** mallen till höger följande layout för dina användar flödes sidor:

![Exempel på en blå mall i Atlanten som återges på inloggnings Sidan](media/customize-ui-overview/ocean-blue.png)

När du väljer en mall tillämpas den valda layouten på alla sidor i ditt användar flöde och URI: n för varje sida visas i fältet URI för **anpassad sida** .

## <a name="where-do-i-store-ui-content"></a>Var lagrar jag GRÄNSSNITTs innehåll?

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet kan du vara värd för ditt GRÄNSSNITTs innehåll var som helst, till exempel på [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webb servrar, CDN, AWS S3 eller fildelnings system. Den viktiga punkten är att du är värd för innehållet på en offentligt tillgänglig HTTPS-slutpunkt med CORS aktiverat. Du måste använda en absolut URL när du anger den i ditt innehåll.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Du gör följande för att anpassa användar gränssnittet:

- Skapa välformulerat HTML-innehåll med ett `<div id="api"></div>` tomt element någonstans `<body>`i. Det här elementet anger var Azure AD B2C innehållet infogas. I följande exempel visas en minimal sida:

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

- Vara värd för ditt innehåll på en HTTPS-slutpunkt (med CORS tillåtet). Du måste aktivera både GET-och option Request-metoderna när du konfigurerar CORS.
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

- Skapa eller redigera en princip för att använda det innehåll som du har skapat.

I följande tabell visas de HTML-fragment som Azure AD B2C sammanfogas i det `<div id="api"></div>` element som finns i ditt innehåll.

| Infogad sida | Beskrivning av HTML |
| ------------- | ------------------- |
| Val av identitetsprovider | Innehåller en lista över knappar för identitets leverantörer som kunden kan välja från vid registrering eller inloggning. Dessa knappar inkluderar sociala identitets leverantörer som Facebook, Google eller lokala konton (baserat på e-postadress eller användar namn). |
| Registrera lokalt konto | Innehåller ett formulär för registrering av lokalt konto baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, till exempel text inmatnings ruta, rutan lösen ords post, alternativ knapp, list rutor med flera val och kryss rutor med flera val. |
| Registrering av socialt konto | Kan visas när du registrerar dig med ett befintligt konto från en social identitetsprovider som Facebook eller Google. Den används när ytterligare information måste samlas in från kunden med hjälp av ett registrerings formulär. |
| Enhetlig registrering eller inloggning | Hanterar både registrering och inloggning av kunder som kan använda sociala identitets leverantörer som Facebook, Google eller lokala konton. |
| Multifaktorautentisering | Kunder kan verifiera sina telefonnummer (med text eller röst) under registreringen eller inloggningen. |
| Fel | Tillhandahåller fel information till kunden. |


## <a name="how-do-i-localize-content"></a>Hur gör jag för att lokalisera innehåll?

Du lokaliserar ditt HTML-innehåll genom att aktivera [språk anpassning](active-directory-b2c-reference-language-customization.md) i Azure AD B2C klient organisationen. När du aktiverar den här funktionen kan Azure AD B2C vidarebefordra parametern `ui-locales` OpenID Connect till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på det språk som används. I din CORS-aktiverade slut punkt ställer du in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet {Culture: RFC5646}. Till exempel kan din anpassade sid-URI se ut `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Du kan läsa in sidan på franska genom att hämta innehåll från`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Exempel

För anpassnings exempel hämtar och granskar du dessa [exempel mallfiler](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Nästa steg

- Om du använder användar flöden kan du börja anpassa ditt användar gränssnitt med själv studie kursen: [Anpassa användar gränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md).
- Om du använder anpassade principer kan du börja anpassa användar gränssnittet med artikeln: [Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

