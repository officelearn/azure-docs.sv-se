---
title: Anpassa användargränssnittet
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användar gränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf83807629cc56aa381c97a9ce36d90c94d61f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388909"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassa användar gränssnittet i Azure Active Directory B2C

Anpassning och anpassning av användar gränssnittet som Azure Active Directory B2C (Azure AD B2C) visar för dina kunder och ger en smidig användar upplevelse i ditt program. Dessa upplevelser omfattar registrering, inloggning, profil redigering och återställning av lösen ord. I den här artikeln beskrivs metoder för anpassning av användar gränssnittet för både användar flöden och anpassade principer.

## <a name="ui-customization-in-different-scenarios"></a>Anpassning av gränssnitt i olika scenarier

Det finns flera sätt att anpassa användar gränssnittet för användaren som använder ditt program, varje lämplig för olika scenarier.

### <a name="user-flows"></a>Användarflöden

Om du använder [användar flöden](user-flow-overview.md)kan du ändra utseendet på dina användar flödes sidor genom att använda inbyggda *mallar*för sidlayout, eller genom att använda din egen HTML och CSS. Båda metoderna beskrivs längre fram i den här artikeln.

Du kan använda [Azure Portal](tutorial-customize-ui.md) för att konfigurera anpassningen av gränssnittet för användar flöden.

> [!TIP]
> Om du bara vill ändra banderollens banderoll, bakgrunds bild och bakgrunds färg för dina användar flödes sidor, kan du testa funktionen för för [hands versionen av företaget (förhands granskning)](#company-branding-preview) längre fram i den här artikeln.

### <a name="custom-policies"></a>Anpassade principer

Om du använder [anpassade principer](custom-policy-overview.md) för att tillhandahålla registrering eller inloggning, lösen ords återställning eller profil redigering i programmet använder [du principfiler för att anpassa användar gränssnittet](custom-policy-ui-customization.md).

Om du behöver tillhandahålla dynamiskt innehåll baserat på kundens beslut kan du använda anpassade principer som kan [ändra sid innehållet dynamiskt](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) beroende på en parameter som skickas i en frågesträng. Du kan till exempel ändra bakgrunds bilden på Azure AD B2C registrerings-eller inloggnings sida baserat på en parameter som du skickar från ditt webb program eller mobil program.

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

Om du vill skapa en egen princip-layout med din anpassade HTML och CSS kan du göra det genom att växla "Använd anpassat sid innehåll" för varje namn som finns i principen. Följ anvisningarna nedan om konfigurationer för anpassad layout:

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
  - Safari för iOS och macOS, version 12 och senare
- Ta inte med **formulär Taggar** i din HTML-kod. Formulär taggarna stör de POST åtgärder som genereras av HTML-koden som infogas av Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Var lagrar jag GRÄNSSNITTs innehåll?

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet kan du vara värd för ditt GRÄNSSNITTs innehåll på en offentligt tillgänglig HTTPS-slutpunkt som stöder CORS. Till exempel [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webb servrar, CDN, AWS S3 eller fildelnings system.

Den viktiga punkten är att du är värd för innehållet på en offentligt tillgänglig HTTPS-slutpunkt med [CORS aktiverat](https://enable-cors.org/server.html). Du måste använda en absolut URL när du anger den i ditt innehåll.

> [!NOTE]
> Mer information om hur du skapar HTML-innehåll, laddar upp innehåll till Azure Blob Storage och konfigurerar CORS finns i avsnittet om [anpassad sid innehåll](custom-policy-ui-customization.md#custom-page-content-walkthrough) i avsnittet om anpassning av användar gränssnittet.

## <a name="get-started-with-custom-html-and-css"></a>Kom igång med anpassad HTML och CSS

Kom igång med din egen HTML och CSS på sidorna med användar upplevelsen genom att följa dessa rikt linjer.

- Skapa välformulerat HTML-innehåll med ett tomt `<div id="api"></div>` element någonstans i `<body>` . Det här elementet anger var Azure AD B2C innehållet infogas. I följande exempel visas en minimal sida:

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

I följande tabell visas de HTML-fragment som Azure AD B2C sammanfogas i det `<div id="api"></div>` element som finns i ditt innehåll.

| Infogad sida | Beskrivning av HTML |
| ------------- | ------------------- |
| Val av identitetsprovider | Innehåller en lista över knappar för identitets leverantörer som kunden kan välja från vid registrering eller inloggning. Dessa knappar inkluderar sociala identitets leverantörer som Facebook, Google eller lokala konton (baserat på e-postadress eller användar namn). |
| Registrera lokalt konto | Innehåller ett formulär för registrering av lokalt konto baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, till exempel text inmatnings ruta, rutan lösen ords post, alternativ knapp, list rutor med flera val och kryss rutor med flera val. |
| Registrering av socialt konto | Kan visas när du registrerar dig med ett befintligt konto från en social identitetsprovider som Facebook eller Google. Den används när ytterligare information måste samlas in från kunden med hjälp av ett registrerings formulär. |
| Enhetlig registrering eller inloggning | Hanterar både registrering och inloggning av kunder som kan använda sociala identitets leverantörer som Facebook, Google eller lokala konton. |
| Multi-Factor Authentication | Kunder kan verifiera sina telefonnummer (med text eller röst) under registreringen eller inloggningen. |
| Fel | Tillhandahåller fel information till kunden. |

## <a name="company-branding-preview"></a>Företags anpassning (för hands version)

Du kan anpassa dina användar flödes sidor med en banderoll, bakgrunds bild och bakgrunds färg genom att använda Azure Active Directory [företags anpassning](../active-directory/fundamentals/customize-branding.md).

Om du vill anpassa dina användar flödes sidor konfigurerar du först företags anpassning i Azure Active Directory. Därefter aktiverar du den i sidlayouten för dina användar flöden i Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Konfigurera varumärkesexponering

Börja med att ange banderoll, bakgrunds bild och bakgrunds färg inom **företags anpassning**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **Hantera**väljer du **företags anpassning**.
1. Följ stegen i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](../active-directory/fundamentals/customize-branding.md).

Tänk på följande när du konfigurerar företags anpassning i Azure AD B2C:

* Företags anpassning i Azure AD B2C är för närvarande begränsad till **bakgrunds bild**, **banderoll-logotyp**och **bakgrunds färg** anpassning. De andra egenskaperna i rutan företags anpassning, till exempel de i **Avancerade inställningar**, *stöds inte*.
* Bakgrunds färgen visas i dina användar flödes sidor innan bakgrunds bilden läses in. Vi rekommenderar att du väljer en bakgrunds färg som nära matchar färgerna i bakgrunds bilden för en smidig inläsnings upplevelse.
* Banderoll-logotypen visas i bekräftelse-e-postmeddelanden som skickas till användarna när de initierar ett registrerings användar flöde.

### <a name="enable-branding-in-user-flow-pages"></a>Aktivera anpassning på användar flödes sidor

När du har konfigurerat företags anpassning aktiverar du det i dina användar flöden.

1. På den vänstra menyn i Azure Portal väljer du **Azure AD B2C**.
1. Under **principer**väljer du **användar flöden (principer)**.
1. Välj det användar flöde som du vill aktivera företags anpassning för. Företags anpassning **stöds inte** för användar flödes typerna *Logga in v1* och *profil redigering v1* .
1. Under **Anpassa** **väljer du sidlayouter och**väljer sedan den layout som du vill anpassa. Välj till exempel **enhetlig registrering eller inloggnings sida**.
1. För sidlayouten **(för hands version)** väljer du version **1.2.0** eller senare.
1. Välj **Spara**.

Om du vill anpassa alla sidor i användar flödet anger du sidlayouten för varje sidlayout i användar flödet.

![Val av sidlayout i Azure AD B2C i Azure Portal](media/customize-ui-overview/portal-02-page-layout-select.png)

Det här kommenterade exemplet visar en anpassad logo typ och bakgrunds bild på sidan *Registrera dig och logga* in på användar flöde som använder den blå mallen för Atlanten:

![Anpassad registrerings-och inloggnings sida som hanteras av Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Använda företags anpassnings till gångar i anpassad HTML

Om du vill använda företagets anpassnings till gångar i anpassad HTML lägger du till följande Taggar utanför `<div id="api">` taggen:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Bild källan ersätts med bakgrunds bildens och banderollens logo typ. Som beskrivs i avsnittet [komma igång med anpassad HTML och CSS](#get-started-with-custom-html-and-css) , använder du CSS-klasser för att formatera och placera till gångarna på sidan.

## <a name="localize-content"></a>Lokalisera innehåll

Du lokaliserar ditt HTML-innehåll genom att aktivera [språk anpassning](user-flow-language-customization.md) i Azure AD B2C klient organisationen. När du aktiverar den här funktionen kan Azure AD B2C vidarebefordra parametern OpenID Connect `ui_locales` till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på det språk som används. I din CORS-aktiverade slut punkt ställer du in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}` .

Din anpassade sid-URI kan till exempel se ut så här:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Du kan läsa in sidan på franska genom att dra innehåll från:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exempel

Du kan hitta flera exempel mallfiler i [B2C-AzureBlobStorage-client-](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) lagringsplatsen på GitHub.

HTML-och CSS-exempelfilerna i mallarna finns i katalogen [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Nästa steg

- Om du använder **användar flöden**kan du börja anpassa ditt användar gränssnitt med själv studie kursen:

    [Anpassa användar gränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md).
- Om du använder **anpassade principer**kan du börja anpassa användar gränssnittet med artikeln:

    [Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C](custom-policy-ui-customization.md).
