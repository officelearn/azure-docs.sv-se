---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 4cb4cdef227c786b12790903037fdc02649592e3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95990892"
---
## <a name="use-custom-page-content"></a>Använd anpassat sid innehåll

Med hjälp av anpassnings funktionen för sid gränssnitt kan du anpassa utseendet på en anpassad princip. Du kan även hålla varumärke och grafik konsekventa mellan programmet och Azure AD B2C.

### <a name="how-it-works"></a>Så här fungerar det

Azure AD B2C kör kod i kundens webbläsare genom att använda [resurs delning mellan ursprung (CORS)](https://www.w3.org/TR/cors/). Vid körning läses innehållet in från en URL som du anger i ditt användar flöde eller en anpassad princip. Varje sida i användar gränssnittet läser in innehållet från den URL som du anger för sidan. När innehållet har lästs in från din URL sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

![Anpassad marginal för sid innehåll](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Innehåll för anpassad HTML-sida

Skapa en HTML-sida med ditt eget varumärke för att hantera ditt anpassade sid innehåll. Den här sidan kan vara en statisk `*.html` sida eller en dynamisk sida som .net, Node.js eller php.

Ditt anpassade sid innehåll kan innehålla alla HTML-element, inklusive CSS och Java Script, men kan inte innehålla osäkra element som iframes. Det enda obligatoriska elementet är ett div-element med `id` inställt på `api` , till exempel det här `<div id="api"></div>` i din HTML-sida.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Anpassa standard Azure AD B2C sidor

I stället för att skapa ett anpassat sid innehåll från grunden kan du anpassa Azure AD-B2C's standard sid innehåll.

I följande tabell visas standard sid innehållet som tillhandahålls av Azure AD B2C. Hämta filerna och Använd dem som utgångs punkt för att skapa egna anpassade sidor.

| Standard sida | Description | ID för innehålls definition<br/>(endast anpassad princip) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. | *API. error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Själv kontrollerad sida**. Använd den här filen som ett anpassat sid innehåll för registrerings sidan för ett socialt konto, en registrerings sida för lokalt konto, en inloggnings sida för lokalt konto, lösen ords återställning med mera. Formuläret kan innehålla olika inmatnings kontroller, t. ex. en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multi-Factor Authentication**. På den här sidan kan användarna verifiera sina telefonnummer (med hjälp av text eller röst) under registreringen eller inloggningen. | *API. phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan profil uppdatering**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrerings-eller inloggnings sida**. Den här sidan hanterar användarens registrerings-och inloggnings process. Användare kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Värd för sid innehållet

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet, ska du vara värd för ditt GRÄNSSNITTs innehåll på en offentligt tillgänglig HTTPS-slutpunkt som stöder CORS. Till exempel [Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App tjänster](../articles/app-service/index.yml), webb servrar, CDN, AWS S3 eller fildelnings system.

## <a name="guidelines-for-using-custom-page-content"></a>Rikt linjer för att använda anpassat sid innehåll

- Använd en absolut URL när du inkluderar externa resurser som media, CSS och JavaScript-filer i HTML-filen.
- Med hjälp av 1.2.0 [och senare kan](../articles/active-directory-b2c/page-layout.md) du lägga till `data-preload="true"` attributet i dina HTML-taggar för att kontrol lera inläsnings ordningen för CSS och Java Script. Med `data-preload=true` konstrueras sidan innan den visas för användaren. Det här attributet förhindrar sidan från att flimra genom att i förväg läsa in CSS-filen utan att den icke-formaterade HTML-koden visas för användaren. Följande HTML-kodfragment visar hur `data-preload` taggen används.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Vi rekommenderar att du börjar med standard innehållet på sidan och bygger ovanpå det.
- Du kan inkludera java script i ditt anpassade innehåll för både [användar flöden](../articles/active-directory-b2c/user-flow-javascript-overview.md) och [anpassade principer](../articles/active-directory-b2c/javascript-samples.md).
- Webb läsar versioner som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42,0 och senare
  - Mozilla Firefox 38,0 och senare
  - Safari för iOS och macOS, version 12 och senare
- På grund av säkerhets begränsningar stöder Azure AD B2C inte `frame` , `iframe` eller `form` HTML-element.

## <a name="custom-page-content-walkthrough"></a>Genom gång av anpassat sid innehåll

Här är en översikt över processen:

1. Förbered en plats som ska vara värd för ditt anpassade sid innehåll (en offentligt tillgänglig, CORS-aktiverad HTTPS-slutpunkt).
1. Hämta och anpassa en standard innehålls fil för sidor, till exempel `unified.html` .
1. Publicera ditt anpassade sid innehåll till en offentligt tillgänglig HTTPS-slutpunkt.
1. Ange resurs delning mellan ursprung (CORS) för din webbapp.
1. Peka din princip till din anpassade princip innehålls-URI.

### <a name="1-create-your-html-content"></a>1. skapa ditt HTML-innehåll

Skapa ett anpassat sid innehåll med produktens märkes namn i rubriken.

1. Kopiera följande HTML-kodfragment. Det är väl formulerad HTML5 med ett tomt element som kallas *\<div id="api"\>\</div\>* i *\<body\>* taggarna. Det här elementet anger var Azure AD B2C innehåll ska infogas.

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

1. Klistra in det kopierade kodfragmentet i en text redigerare och spara sedan filen som *customize-ui.html*.

> [!NOTE]
> HTML-formulärets element tas bort på grund av säkerhets begränsningar om du använder login.microsoftonline.com. [Använd b2clogin.com](../articles/active-directory-b2c/b2clogin.md)om du vill använda HTML-formulär element i ditt anpassade HTML-innehåll.

### <a name="2-create-an-azure-blob-storage-account"></a>2. skapa ett Azure Blob Storage-konto

I den här artikeln använder vi Azure Blob Storage för att vara värd för vårt innehåll. Du kan välja att vara värd för ditt innehåll på en webb server, men du måste [Aktivera CORS på din webb server](https://enable-cors.org/server.html).

Utför följande steg för att vara värd för HTML-innehåll i Blob Storage:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På menyn **hubb** väljer du **nytt**  >  **lagrings**  >  **lagrings konto**.
1. Välj en **prenumeration** för ditt lagrings konto.
1. Skapa en **resurs grupp** eller Välj en befintlig.
1. Ange ett unikt **namn** för ditt lagrings konto.
1. Välj den **geografiska platsen** för ditt lagrings konto.
1. **Distributions modellen** kan vara **Resource Manager**.
1. **Prestanda** kan vara **standard**.
1. Ändra **konto typ** till **Blob Storage**.
1. **Replikeringen** kan förbli **RA-GRS**.
1. **Åtkomst nivån** kan vara **aktiv**.
1. Välj **Granska + skapa** för att skapa lagrings kontot.
    När distributionen är klar öppnas sidan **lagrings konto** automatiskt.

#### <a name="21-create-a-container"></a>2,1 Skapa en behållare

Utför följande steg för att skapa en offentlig behållare i Blob Storage:

1. Under **BLOB service** på menyn till vänster väljer du **blobbar**.
1. Välj **+ behållare**.
1. I **namn** anger du *root*. Namnet kan vara ett namn som du väljer, till exempel *contoso*, men vi använder *roten* i det här exemplet för enkelhetens skull.
1. För **offentlig åtkomst nivå** väljer du **BLOB** och sedan **OK**.
1. Välj **rot** för att öppna den nya behållaren.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 Ladda upp dina anpassade innehållsfiler för sidor

1. Välj **Överför**.
1. Välj mappikonen bredvid **Välj en fil**.
1. Navigera till och välj **customize-ui.html**, som du skapade tidigare i avsnittet sid UI anpassning.
1. Om du vill överföra till en undermapp expanderar du **Avancerat** och anger ett mappnamn i **mappen överför till**.
1. Välj **Överför**.
1. Välj den **customize-ui.html** -blob som du överförde.
1. Till höger om text rutan **URL** väljer du ikonen **Kopiera till Urklipp** för att kopiera webb adressen till Urklipp.
1. I webbläsaren navigerar du till den URL som du kopierade för att verifiera att blobben du överförde är tillgänglig. Om det inte går att komma åt, till exempel om du stöter `ResourceNotFound` på ett fel, se till att behållarens åtkomst typ är inställd på **BLOB**.

### <a name="3-configure-cors"></a>3. Konfigurera CORS

Konfigurera Blob Storage för resurs delning mellan ursprung genom att utföra följande steg:

1. I menyn väljer du **CORS**.
1. För **tillåtna ursprung** anger du `https://your-tenant-name.b2clogin.com` . Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Exempelvis `https://fabrikam.b2clogin.com`. Använd små bokstäver när du anger ditt klient namn.
1. För **tillåtna metoder** väljer du både `GET` och `OPTIONS` .
1. För **tillåtna huvuden** anger du en asterisk (*).
1. För **exponerade rubriker** anger du en asterisk (*).
1. Ange 200 för **högsta ålder**.
1. Välj **Spara**.

#### <a name="31-test-cors"></a>3,1-testcors

Verifiera att du är redo genom att utföra följande steg:

1. Upprepa steget Konfigurera CORS. För **tillåtna ursprung** anger du `https://www.test-cors.org`
1. Navigera till [www.test-CORS.org](https://www.test-cors.org/) 
1. I rutan **Fjärradress** klistrar du in URL: en för HTML-filen. Till exempel `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Välj **skicka begäran**.
    Resultatet bör vara `XHR status: 200` . 
    Kontrol lera att CORS-inställningarna är korrekta om du får ett fel meddelande. Du kan också behöva rensa webbläsarens cacheminne eller öppna en privat webbläsarsession genom att trycka på CTRL + SKIFT + P.