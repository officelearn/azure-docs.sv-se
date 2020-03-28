---
title: 'Självstudiekurs: Integrera flera immersive Reader-resurser'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa ett Node.js-program som startar Immersive Reader med flera Immersive Reader-resurser.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046277"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Självstudiekurs: Integrera flera immersive Reader-resurser

I [översikten](./overview.md)lärde du dig om vad Immersive Reader är och hur den implementerar beprövade tekniker för att förbättra läsförståelsen för språkstuderande, nya läsare och elever med inlärningsskillnader. I [snabbstarten Node.js](./quickstart-nodejs.md)lärde du dig hur du använder Immersive Reader med en enda resurs. Den här självstudien beskriver hur du integrerar flera Immersive Reader-resurser i samma program. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa flera Immersive Reader-resurser under en befintlig resursgrupp
> * Starta Immersive Reader med flera resurser

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Följ [snabbstarten](./quickstart-nodejs.md) för att skapa en webbapp som startar Immersive Reader med NodeJS. I snabbstarten konfigurerar du en enda Immersive Reader-resurs. Vi kommer att bygga på toppen av det i den här guiden.

## <a name="create-the-immersive-reader-resources"></a>Skapa Immersive Reader-resurser

Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att skapa varje Immersive Reader-resurs. **Skriptet Create-ImmersiveReaderResource** `ResourceName` `ResourceSubdomain`har `ResourceLocation` , och som parametrar. Dessa bör vara unika för varje resurs som skapas. De återstående parametrarna ska vara desamma som du använde när du konfigurerade den första Immersive Reader-resursen. På så sätt kan varje resurs länkas till samma Azure-resursgrupp och Azure AD-program.

Exemplet nedan visar hur du skapar två resurser, en i WestUS och en annan i EastUS. Lägg märke till `ResourceName` `ResourceSubdomain`de `ResourceLocation`unika värdena för , och .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Lägga till resurser i miljökonfigurationen

I snabbstarten skapade du en miljökonfigurationsfil `ClientSecret`som `Subdomain` innehåller parametrarna `TenantId`, `ClientId`, och. Eftersom alla dina resurser använder samma Azure AD-program kan `TenantId`vi `ClientId`använda `ClientSecret`samma värden för , och . Den enda ändring som behöver göras är att lista varje underdomän för varje resurs.

Den nya __.env-filen__ ska nu se ut ungefär så här:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Se till att inte arkivera den här filen i källkontroll, eftersom den innehåller hemligheter som inte bör offentliggöras.

Därefter ska vi ändra _filen routes\index.js_ som vi har skapat för att stödja våra resurser. Ersätt innehållet med följande kod.

Precis som tidigare skapar den här koden en API-slutpunkt som hämtar en Azure AD-autentiseringstoken med hjälp av lösenordet för tjänstens huvudnamn. Den här gången tillåter det användaren att ange en resursplats och skicka in den som en frågeparameter. Det returnerar sedan ett objekt som innehåller token och motsvarande underdomän.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Api-slutpunkten **getimmersivereaderlaunchparams** bör skyddas bakom någon form av autentisering (till exempel [OAuth)](https://oauth.net/2/)för att förhindra att obehöriga användare får token som ska användas mot din Immersive Reader-tjänst och fakturering. att arbetet är utanför ramen för den här självstudien.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta Immersive Reader med exempelinnehåll

1. Öppna _vyer\index.pug_och ersätt innehållet med följande kod. Den här koden fyller på sidan med ett visst exempelinnehåll och lägger till två knappar som startar Immersive Reader. En för att starta Immersive Reader för EastUS-resursen och en annan för WestUS-resursen.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Vår webbapp är nu klar. Starta appen genom att köra:

    ```bash
    npm start
    ```

4. Öppna webbläsaren och [http://localhost:3000](http://localhost:3000)navigera till . Du bör se ovanstående innehåll på sidan. Klicka på antingen **EastUS Immersive Reader-knappen** eller **WestUS Immersive Reader-knappen** för att starta Immersive Reader med hjälp av dessa respektive resurser.

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)
* Visa kodexempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)