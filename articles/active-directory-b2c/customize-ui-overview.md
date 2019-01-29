---
title: Om anpassning av användargränssnitt i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du anpassar användargränssnittet för ditt program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c81701dff8d7eebf08aa6b16c61e6915a905c729
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172722"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Om anpassning av användargränssnitt i Azure Active Directory B2C

Möjligheten att varumärken och anpassa användargränssnittet (UI) som används för att dina program i Azure Active Directory (Azure AD) B2C är viktigt för att tillhandahålla en sömlös upplevelse till kunden. Dessa upplevelser är registrering, inloggning, profilredigering och återställning av lösenord. Den här artikeln innehåller information som hjälper dig att anpassa Användargränssnittet för dina program.

Beroende på dina behov när det gäller dessa upplevelser kan anpassa du Användargränssnittet för ditt program på olika sätt. Exempel:

- Om du använder [användarflöden](active-directory-b2c-reference-policies.md) för att ange lösenord för registrering eller inloggning, Återställ eller profilredigering upplevelser i ditt program, använder du den [Azure portal för att anpassa Användargränssnittet](tutorial-customize-ui.md).
- Om ger ut, dess tillhörande sidan för lösenordsåterställning och verifiering via e-post, använder anpassning likadant som används för en [Azure AD-inloggningssida](../active-directory/fundamentals/customize-branding.md).
- Om kunder försöker redigera sin profil innan de loggar in, omdirigeras de till en sida som du anpassa med hjälp av samma steg som används för att anpassa inloggningssidan för Azure AD.
- Om du använder [anpassade principer](active-directory-b2c-overview-custom.md) att ange lösenord för registrering eller inloggning, återställa eller -profilredigering i ditt program, som du använder [principfiler anpassa Användargränssnittet](active-directory-b2c-ui-customization-custom.md).
- Om du vill ange dynamiskt innehåll baserat på en kunds beslut kan du använda [anpassade principer som kan ändra sidan innehåll](active-directory-b2c-ui-customization-custom-dynamic.md) beroende på en parameter som ska skickas som en frågesträng. Till exempel ändras bakgrundsbilden på sidan för Azure AD B2C registrering eller inloggning, baserat på en parameter som du skickar från dina webb- och mobilprogram.

Azure AD B2C körs koden i din kunds webbläsare och använder en modern lösning som kallas [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). Vid körning laddas innehåll från en URL som du anger i en användarflödet eller princip. Du kan ange olika URL: er för olika sidor. När innehållet har lästs in från din URL, sammanfogningen görs med ett HTML-avsnitt infogas från Azure AD B2C och sedan visas för kunden.

Innan du börjar bör du granska följande riktlinjer:

- Azure AD B2C sammanfogar HTML-innehåll i sidorna. Inte kopiera och försök att ändra standardinnehållet som Azure AD B2C tillhandahåller. Det är bäst att skapa HTML-innehåll från grunden och använda standardinnehållet som referens.
- Av säkerhetsskäl tillåts du inte inkludera JavaScript i ditt innehåll.
- Versioner av webbläsare som stöds är: 
    - Internet Explorer 11, 10 och Microsoft Edge
    - Begränsat stöd för Internet Explorer 9 och 8
    - Google Chrome 42.0 och senare
    - Mozilla Firefox 38.0 och senare
- Kontrollera att du inte inkluderar formuläret taggar i din HTML eftersom den stör POST-åtgärder som genererats av inmatade HTML från Azure AD B2C.

## <a name="where-do-i-store-ui-content"></a>Var lagrar Användargränssnittet innehåll?

Du kan vara värd för innehåll var som helst, t.ex på Användargränssnittet [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), webbservrar, CDN, AWS S3, eller dela filsystem. Det viktiga är att du förvarar innehållet på en offentligt tillgänglig HTTPS-slutpunkt med CORS aktiverat. Du måste använda en absolut URL när du anger den i ditt innehåll.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Du kan göra följande för att anpassa Användargränssnittet:

- Skapa välformulerad HTML innehåll med en tom `<div id="api"></div>` element finns någonstans i den `<body>`. Det här elementet som hämtas där Azure AD B2C-innehåll läggs till. I följande exempel visas en minimal sida:

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

- Vara värd för ditt innehåll på en HTTPS-slutpunkt (med CORS tillåts). Både hämta och alternativ metodbegäranden måste vara aktiverat när du konfigurerar CORS.
- Använd CSS att redigera de gränssnittselement som Azure AD B2C som infogar i din sida. I följande exempel visar en enkel CSS-fil som innehåller också inställningar för registrering inmatade HTML-element:

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

- Skapa eller redigera en princip för att använda det innehåll som du skapade.

I följande tabell visas de HTML-kod som Azure AD B2C sammanfogar i den `<div id="api"></div>` element finns i ditt innehåll.

| Infogade sidan | Beskrivning av HTML |
| ------------- | ------------------- |
| Val av identitets-provider | Innehåller en lista över knappar för Identitetsproviders som kunden kan välja mellan vid registrering eller inloggning. Knapparna omfattar sociala identitetsleverantörer som Facebook, Google eller lokala konton (baserat på e-postadress eller användarnamn namn). |
| Lokalt konto för registrering | Innehåller ett formulär för lokalt konto registrering baserat på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika indatakontroller, till exempel textinmatningsrutan, lösenordsruta, alternativknappen, flervals-listrutorna och välja flera kryssrutor. |
| Sociala konto | Kanske visas när du registrerar dig med ett befintligt konto från en social identitetsprovider, till exempel Facebook eller Google. Den används när ytterligare information måste samlas in från den kund som använder en fyllt i registreringsformuläret. |
| Enhetlig registrering eller inloggning | Hanterar registrering och inloggning för kunder som kan använda sociala identitetsleverantörer som Facebook, Google eller lokala konton. |
| Multi-Factor Authentication | Kunder kan verifiera sina telefonnummer (med text eller röst) under registrering eller inloggning. |
| Fel | Ger information om fel till kunden. |


## <a name="how-do-i-localize-content"></a>Hur jag för att lokalisera innehållet?

Du ska lokalisera din HTML-innehåll genom att aktivera [språkanpassning](active-directory-b2c-reference-language-customization.md) i din Azure AD B2C-klient. Den här funktionen aktiveras kan Azure AD B2C att vidarebefordra parametern öppna ID Connect `ui-locales` till slutpunkten. Innehållsservern kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på det språk som används. I CORS-aktiverade slutpunkten ställer du in en mappstruktur som värd för innehåll för vissa språk. Rätt kallar om du använder jokertecken värdet {kultur: RFC5646}. Till exempel en anpassad sida URI kan se ut `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Du kan läsa in sidan på franska genom att hämta innehåll från `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Exempel

För exempel på anpassning, ladda ned och granska dessa [exempel mallfiler](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Nästa steg

- Om du använder användarflöden, kan du börja anpassa Användargränssnittet med självstudiekursen: [Anpassa användargränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md).
- Om du använder anpassade principer, kan du börja anpassa Användargränssnittet med artikeln: [Anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

