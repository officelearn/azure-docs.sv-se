---
title: Anpassa användargränssnittet
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användargränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ddf57057b736cd76a74276e5593a865e7df8cc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666875"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassa användargränssnittet i Azure Active Directory B2C

Genom att skapa och anpassa användargränssnittet som Azure Active Directory B2C (Azure AD B2C) visar för dina kunder kan du ge en sömlös användarupplevelse i ditt program. Dessa upplevelser omfattar registrering, inloggning, profilredigering och återställning av lösenord. Den här artikeln introducerar metoderna för anpassning av användargränssnittet för både användarflöden och anpassade principer.

## <a name="ui-customization-in-different-scenarios"></a>Anpassning av användargränssnittet i olika scenarier

Det finns flera sätt att anpassa användargränssnittet för användaren, var och en lämplig för olika scenarier.

### <a name="user-flows"></a>Användarflöden

Om du använder [användarflöden](user-flow-overview.md)kan du ändra utseendet på användarnas flödessidor med hjälp av inbyggda *sidlayoutmallar*eller genom att använda din egen HTML och CSS. Båda metoderna diskuteras senare i den här artikeln.

Du använder [Azure-portalen](tutorial-customize-ui.md) för att konfigurera användargränssnittsanpassningen för användarflöden.

> [!TIP]
> Om du bara vill ändra banderolllogotypen, bakgrundsbilden och bakgrundsfärgen på användarnas flödessidor kan du prova funktionen [Företagsprofilering (förhandsversion)](#company-branding-preview) som beskrivs senare i den här artikeln.

### <a name="custom-policies"></a>Anpassade principer

Om du använder [anpassade principer](custom-policy-overview.md) för att ange registrering eller inloggning, återställning av lösenord eller profilredigering i ditt program använder du [principfiler för att anpassa användargränssnittet](custom-policy-ui-customization.md).

Om du behöver ange dynamiskt innehåll baserat på en kunds beslut använder du anpassade principer som kan [ändra sidinnehåll dynamiskt](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) beroende på en parameter som skickas i en frågesträng. Du kan till exempel ändra bakgrundsavbildningen på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilappprogram.

### <a name="javascript"></a>JavaScript

Du kan aktivera JavaScript-kod på klientsidan i både [användarflöden](user-flow-javascript-overview.md) och [anpassade principer](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Anpassning av gränssnittsgränssnittet

Om du bara tillhandahåller inloggning, tillsammans med den medföljande sidan för återställning av lösenord och verifiering, använder du samma anpassningssteg som används för en [Azure AD-inloggningssida](../active-directory/fundamentals/customize-branding.md).

Om kunder försöker redigera sin profil innan de loggar in omdirigeras de till en sida som du anpassar med samma steg som används för att anpassa inloggningssidan för Azure AD.

## <a name="page-layout-templates"></a>Mallar för sidlayout

Användarflöden ger flera inbyggda mallar du kan välja mellan för att ge dina användarupplevelsesidor ett professionellt utseende. Dessa layoutmallar kan också och fungera som utgångspunkt för din egen anpassning.

Under **Anpassa** på den vänstra menyn väljer du **Sidlayouter** och väljer sedan **Mall**.

![Listrutan Mallval i sidan användarflöde i Azure Portal](media/customize-ui-overview/template-selection.png)

Välj sedan en mall i listan. Här är exempel på inloggningssidorna för varje mall:

| Ocean Blå | Skiffer Grå | Klassisk |
|:-:|:-:|:-:|
|![Exempel på mallen Ocean Blue som återges på inloggningssidan för registrering](media/customize-ui-overview/template-ocean-blue.png)|![Exempel på mallen Skiffer grå återges på inloggningssidan för registrering](media/customize-ui-overview/template-slate-gray.png)|![Exempel på den klassiska mallen som återges på inloggningssidan för registrering](media/customize-ui-overview/template-classic.png)|

När du väljer en mall används den markerade layouten på alla sidor i användarflödet och URI:n för varje sida visas i fältet **Anpassad sid-URI.**

## <a name="custom-html-and-css"></a>Anpassad HTML och CSS

Om du vill utforma din egen principlayout med din anpassade HTML och CSS kan du göra det genom att växla "Använd anpassat sidinnehåll" för vart och ett av layoutnamnen som finns i din princip. Följ nedanstående instruktioner om anpassade layoutkonfigurationer:

Azure AD B2C kör kod i kundens webbläsare med hjälp av en metod som kallas [CORS (Cross-Origin Resource Sharing).](https://www.w3.org/TR/cors/)

Vid körning läses innehåll in från en URL som du anger i ditt användarflöde eller din anpassade princip. Varje sida i användarupplevelsen läser in innehållet från den WEBBADRESS som du anger för den sidan. När innehållet har lästs in från webbadressen sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

Läs följande vägledning innan du använder dina egna HTML- och CSS-filer för att anpassa användargränssnittet:

- Azure AD B2C **sammanfogar** HTML-innehåll till dina sidor. Kopiera inte och försök inte ändra standardinnehållet som Azure AD B2C tillhandahåller. Det är bäst att skapa HTML-innehåll från grunden och använda standardinnehållet som referens.
- **JavaScript** kan inkluderas i ditt anpassade innehåll för både [användarflöden](user-flow-javascript-overview.md) och [anpassade principer](javascript-samples.md).
- **Webbläsarversioner** som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42.0 och högre
  - Mozilla Firefox 38.0 och högre
  - Safari för iOS och macOS, version 12 och högre
- Ta inte med **formulärtaggar** i HTML-koden. Formulärtaggar stör POST-åtgärder som genereras av HTML-koden som injiceras av Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Var lagrar jag användargränssnittsinnehåll?

När du använder dina egna HTML- och CSS-filer för att anpassa användargränssnittet kan du vara värd för användargränssnittets innehåll på alla offentligt tillgängliga HTTPS-slutpunkter som stöder CORS. [Azure Blob storage,](../storage/blobs/storage-blobs-introduction.md)webbservrar, CDN, AWS S3 eller fildelningssystem.

Det viktiga är att du är värd för innehållet på en allmänt tillgänglig HTTPS-slutpunkt med [CORS aktiverat](https://enable-cors.org/server.html). Du måste använda en absolut WEBBADRESS när du anger den i innehållet.

> [!NOTE]
> Mer information om hur du skapar HTML-innehåll, laddar upp innehåll till Azure Blob-lagring och konfigurerar CORS finns i avsnittet [Anpassad sidinnehållsgenomgång](custom-policy-ui-customization.md#custom-page-content-walkthrough) i anpassningsartikeln för användargränssnittet.

## <a name="get-started-with-custom-html-and-css"></a>Komma igång med anpassad HTML och CSS

Kom igång med din egen HTML och CSS i dina användarupplevelsesidor genom att följa dessa riktlinjer.

- Skapa välformat HTML-innehåll `<div id="api"></div>` med ett tomt `<body>`element som finns någonstans i . Det här elementet markerar var Azure AD B2C-innehållet infogas. I följande exempel visas en minimal sida:

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

- Använd CSS för att utforma gränssnittselementen som Azure AD B2C infogar på sidan. I följande exempel visas en enkel CSS-fil som även innehåller inställningar för de registrerade HTML-elementen:

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

- Vara värd för ditt innehåll på en HTTPS-slutpunkt (med CORS tillåtet). Både GET- och OPTIONS-begäransmetoder måste aktiveras när CORS konfigureras.
- Skapa eller redigera ett användarflöde eller en anpassad princip för att använda innehållet som du har skapat.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-fragment från Azure AD B2C

I följande tabell visas de HTML-fragment som Azure `<div id="api"></div>` AD B2C sammanfogar till elementet som finns i innehållet.

| Infogad sida | Beskrivning av HTML |
| ------------- | ------------------- |
| Val av identitetsprovider | Innehåller en lista med knappar för identitetsleverantörer som kunden kan välja mellan under registrering eller inloggning. Dessa knappar inkluderar sociala identitetsleverantörer som Facebook, Google eller lokala konton (baserat på e-postadress eller användarnamn). |
| Registrering av lokalt konto | Innehåller ett formulär för lokal kontoanmälas baserat på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika inmatningskontroller, till exempel textinmatningsruta, inmatningsruta för lösenord, alternativknapp, listrutor med enkel markeringar och kryssrutor med flera markeringar. |
| Registrering av sociala konton | Kan visas när du registrerar dig med ett befintligt konto från en leverantör av social identitet som Facebook eller Google. Den används när ytterligare information måste samlas in från kunden med hjälp av ett registreringsformulär. |
| Enhetlig registrering eller inloggning | Hanterar både registrering och inloggning av kunder som kan använda sociala identitetsleverantörer som Facebook, Google eller lokala konton. |
| Multi-Factor Authentication | Kunder kan verifiera sina telefonnummer (med hjälp av text eller röst) under registrering eller inloggning. |
| Fel | Ger felinformation till kunden. |

## <a name="company-branding-preview"></a>Företagets varumärke (förhandsgranskning)

Du kan anpassa dina användarflödessidor med en bannerlogotyp, bakgrundsbild och bakgrundsfärg med hjälp av Azure Active Directory [Company-varumärke](../active-directory/fundamentals/customize-branding.md).

Om du vill anpassa dina användarflödessidor konfigurerar du först företagets varumärke i Azure Active Directory och aktiverar det sedan i sidlayouterna för dina användarflöden i Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Konfigurera varumärkesexponering

Börja med att ställa in banderolllogotypen, bakgrundsbilden och bakgrundsfärgen **i Företagets varumärke**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Under **Hantera**väljer du **Företagsprofil.**
1. Följ stegen i [Lägg till varumärkesprofilering på organisationens inloggningssida för Azure Active Directory](../active-directory/fundamentals/customize-branding.md).

Tänk på dessa saker när du konfigurerar företagsprofilering i Azure AD B2C:

* Företagets varumärke i Azure AD B2C är för närvarande begränsat till **bakgrundsbild,** **bannerlogotyp**och **anpassning av bakgrundsfärger.** De andra egenskaperna i företagets varumärkesfönster, till exempel de i **Avancerade inställningar,** *stöds inte*.
* I användarnas flödessidor visas bakgrundsfärgen innan bakgrundsbilden läses in. Vi rekommenderar att du väljer en bakgrundsfärg som nära matchar färgerna i bakgrundsbilden för en jämnare inläsningsupplevelse.
* Bannerlogotypen visas i verifieringsmeddelanden som skickas till användarna när de initierar ett användarflöde för registrering.

### <a name="enable-branding-in-user-flow-pages"></a>Aktivera varumärkesprofilering på användarflödessidor

När du har konfigurerat företagets varumärke aktiverar du det i dina användarflöden.

1. Välj **Azure AD B2C**i den vänstra menyn i Azure-portalen .
1. Under **Principer**väljer du **Användarflöden (principer)**.
1. Välj det användarflöde som du vill aktivera företagsprofilering för. Företagets varumärke **stöds inte** för användarflödestyperna *Logga in v1* och *Profilredigering v1.*
1. Under **Anpassa**väljer du **Sidlayouter**och väljer sedan den layout som du vill skapa. Välj till exempel **Enhetlig registrering eller logga in på sidan**.
1. För **sidlayoutversionen (förhandsgranskning)** väljer du version **1.2.0** eller högre.
1. Välj **Spara**.

Om du vill skapa alla sidor i användarflödet anger du sidlayoutversionen för varje sidlayout i användarflödet.

![Val av sidlayout i Azure AD B2C i Azure-portalen](media/customize-ui-overview/portal-02-page-layout-select.png)

I det här kommenterade exemplet visas en anpassad bannerlogotyp och bakgrundsbild på en sida för *registrering och inloggning* av användarflöde som använder mallen Ocean Blue:

![Branded registrerings-/inloggningssida som betjänas av Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Använda företagsprofiltillgångar i anpassad HTML

Om du vill använda företagets varumärkestillgångar i anpassad `<div id="api">` HTML lägger du till följande taggar utanför taggen:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Bildkällan ersätts med bakgrundsbildens och banderolllogotypens. Som beskrivs i avsnittet [Kom igång med anpassad HTML och CSS](#get-started-with-custom-html-and-css) använder du CSS-klasser för att formatera och placera tillgångarna på sidan .

## <a name="localize-content"></a>Lokalisera innehåll

Du lokaliserar html-innehållet genom att aktivera [språkanpassning](user-flow-language-customization.md) i din Azure AD B2C-klient. Om du aktiverar den här funktionen kan Azure AD `ui-locales` B2C vidarebefordra parametern OpenID Connect till slutpunkten. Innehållsservern kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehåll kan hämtas från olika platser baserat på det språk som används. I den CORS-aktiverade slutpunkten ställer du in en mappstruktur som värd för innehåll för specifika språk. Du ringer den rätta om du använder `{Culture:RFC5646}`jokertecknets värde.

Din anpassade sid-URI kan till exempel se ut så här:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Du kan läsa in sidan på franska genom att dra innehåll från:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exempel

Du hittar flera exempelmallfiler i [databasen B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) på GitHub.

Exempel-HTML- och CSS-filerna i mallarna finns i katalogen [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Nästa steg

- Om du använder **användarflöden**kan du börja anpassa användargränssnittet med självstudien:

    [Anpassa användargränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md).
- Om du använder **anpassade principer**kan du börja anpassa användargränssnittet med artikeln:

    [Anpassa användargränssnittet för ditt program med hjälp av en anpassad princip i Azure Active Directory B2C](custom-policy-ui-customization.md).
