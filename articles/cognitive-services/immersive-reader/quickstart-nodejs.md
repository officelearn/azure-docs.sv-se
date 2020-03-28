---
title: 'Snabbstart: Skapa en webbapp som startar Immersive Reader med Node.js'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du en webbapp från grunden och lägger till Immersive Reader API-funktionen.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75946329"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Snabbstart: Skapa en webbapp som startar Immersive Reader (Node.js)

[Den uppslukande läsaren](https://www.onenote.com/learningtools) är ett inkluderande utformat verktyg som implementerar beprövade tekniker för att förbättra läsförståelsen.

I den här snabbstarten skapar du en webbapp från grunden och integrerar Immersive Reader med hjälp av Immersive Reader SDK. Ett fullständigt fungerande exempel på den här snabbstarten finns [här](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* En Immersive Reader-resurs som konfigurerats för Azure Active Directory-autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att komma igång. Du behöver några av de värden som skapas här när du konfigurerar miljöegenskaperna. Spara utdata från sessionen i en textfil för framtida referens.
* [Node.js](https://nodejs.org/) och [garn](https://yarnpkg.com)
* En IDE som [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Skapa en nod.js-webbapp med Express

Skapa en nod.js-webbapp med verktyget. `express-generator`

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installera garnberoenden och lägg `request` `dotenv`till beroenden och , som kommer att användas senare i snabbstarten.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Konfigurera autentisering

### <a name="configure-authentication-values"></a>Konfigurera autentiseringsvärden

Skapa en ny fil med _namnet .env_ i projektets rot. Klistra in följande kod i den och ange de värden som angavs när du skapade Immersive Reader-resursen.
Ta inte med citattecken eller tecknen "{" och "}".

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

### <a name="update-the-router-to-acquire-the-token"></a>Uppdatera routern för att hämta token
Öppna _filen routes\index.js_ och ersätt den automatiskt genererade koden med följande kod.

Den här koden skapar en API-slutpunkt som hämtar en Azure AD-autentiseringstoken med hjälp av tjänstens huvudlösenord. Den hämtar också underdomänen. Det returnerar sedan ett objekt som innehåller token och underdomänen.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**GetTokenAndSubdomain** API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth)](https://oauth.net/2/)för att förhindra att obehöriga användare får token att använda mot din Immersive Reader-tjänst och fakturering. att arbetet ligger utanför ramen för denna snabbstart.

## <a name="add-sample-content"></a>Lägga till exempelinnehåll

Nu lägger vi till exempelinnehåll i den här webbappen. Öppna _vyer\index.pug_ och ersätt den automatiskt genererade koden med det här exemplet:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Observera att all text **lang** har ett lang-attribut, som beskriver textens språk. Det här attributet hjälper Immersive Reader att tillhandahålla relevanta språk- och grammatikfunktioner.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Vår webbapp är nu klar. Starta appen genom att köra:

```bash
npm start
```

Öppna webbläsaren och _http://localhost:3000_navigera till . Du bör se följande:

![Exempelapp](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Starta den uppslukande läsaren

När du klickar på knappen "Uppslukande läsare" ser du Immersive Reader lanseras med innehållet på sidan.

![Avancerad läsare](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)