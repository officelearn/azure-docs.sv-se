---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117049"
---
## <a name="use-custom-page-content"></a>Använda anpassat sidinnehåll

Genom att använda anpassningsfunktionen för sidgränssnittet kan du anpassa utseendet och känslan för en anpassad princip. Du kan även hålla varumärke och grafik konsekventa mellan programmet och Azure AD B2C.

### <a name="how-it-works"></a>Hur det fungerar

Azure AD B2C kör kod i kundens webbläsare med hjälp av [CORS (Cross-Origin Resource Sharing).](https://www.w3.org/TR/cors/) Vid körning läses innehåll in från en URL som du anger i ditt användarflöde eller din anpassade princip. Varje sida i användarupplevelsen läser in innehållet från den WEBBADRESS som du anger för den sidan. När innehållet har lästs in från webbadressen sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

![Marginal för anpassat sidinnehåll](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Anpassat HTML-sidinnehåll

Skapa en HTML-sida med ditt eget varumärke för att visa ditt anpassade sidinnehåll. Den här sidan `*.html` kan vara en statisk sida eller en dynamisk sida som .NET, Node.js eller PHP.

Ditt anpassade sidinnehåll kan innehålla alla HTML-element, inklusive CSS och JavaScript, men kan inte innehålla osäkra element som iframes. Det enda element som krävs `id` är `api`ett div-element med inställt på , till exempel det här `<div id="api"></div>` på HTML-sidan.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Anpassa standardsidorna för Azure AD B2C

I stället för att skapa ditt anpassade sidinnehåll från grunden kan du anpassa Azure AD B2C:s standardsidesinnehåll.

I följande tabell visas standardsideinnehållet som tillhandahålls av Azure AD B2C. Ladda ner filerna och använd dem som utgångspunkt för att skapa egna anpassade sidor.

| Standardsida | Beskrivning | ID för innehållsdefinition<br/>(endast anpassad princip) |
|:-----------------------|:--------|-------------|
| [undantag.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. | *api.error api.error api.error api.* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Självförsäkrad sida**. Använd den här filen som ett anpassat sidinnehåll för en registreringssida för sociala konton, en plats för registreringssida för lokala konton, en inloggningssida för lokalt konto, återställning av lösenord med mera. Formuläret kan innehålla olika inmatningskontroller, till exempel: en textinmatningsruta, en inmatningsruta för lösenord, en alternativknapp, listrutor med enkel markeringar och kryssrutor med flera markeringar. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifaktor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multifaktorautentisering**. På den här sidan kan användarna verifiera sina telefonnummer (med hjälp av text eller röst) under registrering eller inloggning. | *api.phonefactor (api.phonefactor)* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan Profiluppdatering**. Den här sidan innehåller ett formulär som användarna kan komma åt för att uppdatera sin profil. Den här sidan liknar registreringssidan för sociala konton, förutom fälten för lösenordsinmatning. | *api.selfasserted.profileupdate api.selfasserted.profileupdate api.selfasserted.profileupdate api.* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrerings- eller inloggningssida**. På den här sidan hanteras registrerings- och inloggningsprocessen för användare. Användare kan använda företagsidentitetsleverantörer, sociala identitetsleverantörer som Facebook eller Google+ eller lokala konton. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Värd för sidinnehållet

När du använder dina egna HTML- och CSS-filer för att anpassa användargränssnittet ska du vara värd för användargränssnittets innehåll på alla offentligt tillgängliga HTTPS-slutpunkter som stöder CORS. [Azure Blob storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services](/azure/app-service/), webbservrar, CDN, AWS S3 eller fildelningssystem.

## <a name="guidelines-for-using-custom-page-content"></a>Riktlinjer för användning av anpassat sidinnehåll

- Använd en absolut URL när du inkluderar externa resurser som media-, CSS- och JavaScript-filer i HTML-filen.
- Med hjälp av [sidlayout version](../articles/active-directory-b2c/page-layout.md) 1.2.0 och högre kan du lägga till `data-preload="true"` attributet i HTML-taggarna för att styra inläsningsordningen för CSS och JavaScript. Med `data-preload=true`skapas sidan innan den visas för användaren. Det här attributet förhindrar att sidan "flimrar" genom att förinläsa CSS-filen, utan att den o-stilade HTML-koden visas för användaren. Följande HTML-kodavsnitt visar användningen av `data-preload` taggen.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Vi rekommenderar att du börjar med standardsidans innehåll och bygger ovanpå det.
- Du kan inkludera JavaScript i ditt anpassade innehåll för både [användarflöden](../articles/active-directory-b2c/user-flow-javascript-overview.md) och [anpassade principer](../articles/active-directory-b2c/javascript-samples.md).
- Webbläsarversioner som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42.0 och högre
  - Mozilla Firefox 38.0 och högre
  - Safari för iOS och macOS, version 12 och högre
- På grund av säkerhetsbegränsningar stöder `frame`inte Azure `iframe`AD `form` B2C , eller HTML-element.

## <a name="custom-page-content-walkthrough"></a>Genomgång av anpassat sidinnehåll

Här är en översikt över processen:

1. Förbered en plats som värd för ditt anpassade sidinnehåll (en allmänt tillgänglig, CORS-aktiverad HTTPS-slutpunkt).
1. Ladda ned och anpassa en standardfil för sidinnehåll, till exempel `unified.html`.
1. Publicera ditt anpassade sidinnehåll din offentligt tillgängliga HTTPS-slutpunkt.
1. Ange resursdelning mellan ursprungstillgångar (CORS) för din webbapp.
1. Peka din princip på ditt anpassade principinnehåll URI.

### <a name="1-create-your-html-content"></a>1. Skapa html-innehåll

Skapa ett anpassat sidinnehåll med produktens varumärke i titeln.

1. Kopiera följande HTML-kodavsnitt. Det är välformulerad HTML5 med ett tomt element som kallas * \<div\>\<id="api" /div\> * som finns i * \<brödtexttaggarna.\> * Det här elementet anger var Azure AD B2C-innehåll ska infogas.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Klistra in det kopierade kodavsnittet i en textredigerare och spara sedan filen som *customize-ui.html*.

> [!NOTE]
> HTML-formulärelement tas bort på grund av säkerhetsbegränsningar om du använder login.microsoftonline.com. Om du vill använda HTML-formulärelement i det anpassade HTML-innehållet [använder du b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Skapa ett Azure Blob-lagringskonto

I den här artikeln använder vi Azure Blob-lagring för att vara värd för vårt innehåll. Du kan välja att vara värd för innehållet på en webbserver, men du måste [aktivera CORS på webbservern](https://enable-cors.org/server.html).

Så här är du värd för HTML-innehåll i Blob-lagring:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Nytt** > **lagringslagringskonto på** > **Storage account** **Hub-menyn** .
1. Välj en **prenumeration** för ditt lagringskonto.
1. Skapa en **resursgrupp** eller välj en befintlig.
1. Ange ett unikt **namn** för ditt lagringskonto.
1. Välj **geografisk plats** för ditt lagringskonto.
1. **Distributionsmodellen** kan förbli **Resource Manager**.
1. **Prestanda** kan förbli **standard**.
1. Ändra **kontoslag** till **Blob-lagring**.
1. **Replikering** kan förbli **RA-GRS**.
1. **Åtkomstnivån** kan förbli **hot**.
1. Välj **Granska + skapa** för att skapa lagringskontot.
    När distributionen är klar öppnas sidan **Lagringskonto** automatiskt.

#### <a name="21-create-a-container"></a>2.1 Skapa en behållare

Så här skapar du en offentlig behållare i Blob-lagring:

1. Under **Blob-tjänsten** på menyn till vänster väljer du **Blobbar**.
1. Välj **+Behållare**.
1. För **Namn**anger du *rot*. Namnet kan vara ett namn som du väljer, till exempel *contoso*, men vi använder *rot* i det här exemplet för enkelhet.
1. För **offentlig åtkomstnivå**väljer du **Blob**och sedan **OK**.
1. Välj **rot** för att öppna den nya behållaren.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Ladda upp innehållsfiler för anpassade sidor

1. Välj **Överför**.
1. Markera mappikonen **bredvid Markera en fil**.
1. Navigera till och välj **anpassa-ui.html**, som du skapade tidigare i anpassningsavsnittet för sidgränssnitt.
1. Om du vill ladda upp till en undermapp expanderar du **Avancerat** och anger ett mappnamn i **Ladda upp till mappen**.
1. Välj **Överför**.
1. Välj **customize-ui.html** den kortlursbump som du har laddat upp.
1. Till höger om **textrutan URL** markerar du ikonen **Kopiera till Urklipp** för att kopiera URL:en till Urklipp.
1. I webbläsaren navigerar du till webbadressen som du kopierade för att kontrollera att blobben du laddade upp är tillgänglig. Om den inte är tillgänglig, till `ResourceNotFound` exempel om du stöter på ett fel, kontrollerar du att åtkomsttypen för behållaren är inställd på **blob**.

### <a name="3-configure-cors"></a>3. Konfigurera CORS

Konfigurera Blob-lagring för resursdelning mellan ursprungsgränser genom att utföra följande steg:

1. Välj **CORS**i menyn .
1. För **Tillåtna**ursprung `https://your-tenant-name.b2clogin.com`anger du . Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com`. Använd alla gemener när du anger ditt klientnamn.
1. För **tillåtna**metoder `GET` `OPTIONS`väljer du både och .
1. För **tillåtna rubriker**anger du en asterisk (*).
1. För **exponerade rubriker**anger du en asterisk (*).
1. För **Max ålder**anger du 200.
1. Välj **Spara**.

#### <a name="31-test-cors"></a>3.1 Testa CORS

Verifiera att du är redo genom att utföra följande steg:

1. Upprepa konfigurera CORS-steget. För **tillåtna ursprung**anger du`https://www.test-cors.org`
1. Navigera till [www.test-cors.org](https://www.test-cors.org/) 
1. Klistra in URL-adressen för HTML-filen för rutan **Fjärr-URL.** Till exempel, `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Välj **Skicka begäran**.
    Resultatet bör `XHR status: 200`bli . 
    Om du får ett felmeddelande kontrollerar du att CORS-inställningarna är korrekta. Du kan också behöva rensa webbläsarens cacheminne eller öppna en privat webbläsarsession genom att trycka på Ctrl+Skift+P.
