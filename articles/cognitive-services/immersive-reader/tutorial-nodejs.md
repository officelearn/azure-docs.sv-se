---
title: 'Självstudiekurs: Starta den uppslukande läsaren med Node.js'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa ett Node.js-program som startar Immersive Reader.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842035"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Självstudiekurs: Starta den uppslukande läsaren (Node.js)

I [översikten](./overview.md)lärde du dig om vad Immersive Reader är och hur den implementerar beprövade tekniker för att förbättra läsförståelsen för språkstuderande, nya läsare och elever med inlärningsskillnader. Den här självstudien beskriver hur du skapar ett Node.js-webbprogram som startar Immersive Reader. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en nod.js-webbapp med Express
> * Hämta en åtkomsttoken
> * Starta Immersive Reader med exempelinnehåll
> * Ange språket för ditt innehåll
> * Ange språket i Immersive Reader-gränssnittet
> * Starta Immersive Reader med matematiskt innehåll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* En Immersive Reader-resurs som konfigurerats för Azure Active Directory-autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att komma igång. Du behöver några av de värden som skapas här när du konfigurerar miljöegenskaperna. Spara utdata från sessionen i en textfil för framtida referens.
* [Node.js](https://nodejs.org/) och [garn](https://yarnpkg.com)
* En IDE som [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Skapa en nod.js-webbapp med Express

Skapa en nod.js-webbapp med verktyget. `express-generator`

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installera garnberoenden och lägg `request` `dotenv`till beroenden och , som kommer att användas senare i självstudien.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

Skriv sedan ett serverd-API för att hämta en Azure AD-autentiseringstoken.

Du behöver några värden från Azure AD-konfigurationsförutsättningssteget ovan för den här delen. Gå tillbaka till textfilen som du sparade av sessionen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

När du har dessa värden skapar du en ny fil med namnet _.env_och klistrar in följande kod i den och anger dina anpassade egenskapsvärden ovanifrån. Ta inte med citattecken eller tecknen "{" och "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Se till att inte arkivera den här filen i källkontroll, eftersom den innehåller hemligheter som inte bör offentliggöras.

Öppna sedan _app.js_ och lägg till följande högst upp i filen. Då läses egenskaperna som definierats i .env-filen som miljövariabler till Nod.

```javascript
require('dotenv').config();
```

Öppna _filen routes\index.js_ och ersätt innehållet med följande kod.

Den här koden skapar en API-slutpunkt som hämtar en Azure AD-autentiseringstoken med hjälp av tjänstens huvudlösenord. Den hämtar också underdomänen. Det returnerar sedan ett objekt som innehåller token och underdomänen.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Api-slutpunkten **getimmersivereaderlaunchparams** bör skyddas bakom någon form av autentisering (till exempel [OAuth)](https://oauth.net/2/)för att förhindra att obehöriga användare får token som ska användas mot din Immersive Reader-tjänst och fakturering. att arbetet är utanför ramen för den här självstudien.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta Immersive Reader med exempelinnehåll

1. Öppna _vyer\layout.pug_och lägg till `head` följande kod `body` under taggen före taggen. Dessa `script` taggar läser in [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) och jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Öppna _vyer\index.pug_och ersätt innehållet med följande kod. Den här koden fyller på sidan med visst exempelinnehåll och lägger till en knapp som startar Immersive Reader.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Vår webbapp är nu klar. Starta appen genom att köra:

    ```bash
    npm start
    ```

4. Öppna webbläsaren och _http://localhost:3000_navigera till . Du bör se ovanstående innehåll på sidan. Klicka på knappen **Uppslukande läsare** om du vill starta Immersive Reader med ditt innehåll.

## <a name="specify-the-language-of-your-content"></a>Ange språket för ditt innehåll

Den uppslukande läsaren har stöd för många olika språk. Du kan ange språket för ditt innehåll genom att följa stegen nedan.

1. Öppna _vyer\index.pug_ och lägg till `p(id=content)` följande kod under taggen som du lade till i föregående steg. Den här koden lägger till visst innehåll spanskt innehåll på din sida.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Lägg till följande ovan anropet i `ImmersiveReader.launchAsync`JavaScript-koden i JavaScript-koden . Den här koden skickar det spanska innehållet till den uppslukande läsaren.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigera _http://localhost:3000_ till igen. Du bör se den spanska texten på sidan, och när du klickar på **Immersive Reader**, kommer det att dyka upp i Immersive Reader också.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Ange språket i Immersive Reader-gränssnittet

Som standard matchar språket i Immersive Reader-gränssnittet webbläsarens språkinställningar. Du kan också ange språket i Immersive Reader-gränssnittet med följande kod.

1. Ersätt anropet till `ImmersiveReader.launchAsync(token, subdomain, content)` med koden nedan i _vyer\index.pug._

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigera _http://localhost:3000_till . När du startar Immersive Reader visas gränssnittet på franska.

## <a name="launch-the-immersive-reader-with-math-content"></a>Starta Immersive Reader med matematiskt innehåll

Du kan inkludera matematiskt innehåll i Immersive Reader med [mathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Ändra _vyer\index.pug_ för att inkludera följande `ImmersiveReader.launchAsync`kod ovanför anropet till:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navigera _http://localhost:3000_till . När du startar Immersive Reader och bläddrar till botten visas matematikformeln.

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)
* Visa kodexempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
