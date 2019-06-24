---
title: 'Självstudier: Starta uppslukande läsaren (Node.js)'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du ett Node.js-program som startar uppslukande läsaren.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 98b46636be321bfe87c08687600894d0c8ab54db
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311710"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Självstudier: Starta uppslukande läsaren (Node.js)

I den [översikt](./overview.md), lär du dig om vad uppslukande läsaren är och hur den implementerar beprövade metoder för att förbättra läsbarheten för språk-inlärning, kommande läsare och studenter för att lära dig skillnaderna. Den här självstudien beskriver hur du skapar ett Node.js-webbprogram som startar uppslukande läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Node.js-webbapp med Express
> * Hämta en åtkomsttoken
> * Starta uppslukande läsaren innehåll
> * Ange språket för ditt innehåll
> * Ange språket för gränssnittet uppslukande läsare
> * Starta uppslukande läsaren med matematiska innehåll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En prenumerationsnyckel för uppslukande läsare. Skaffa en genom att följa [instruktionerna](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
* [Node.js](https://nodejs.org/) och [Yarn](https://yarnpkg.com)
* En IDE som [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Skapa en Node.js-webbapp med Express

Skapa en Node.js-webbapp med den `express-generator` verktyget.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installera yarn beroenden och lägga till beroenden `request` och `dotenv`, som används senare under kursen.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>Hämta en åtkomsttoken

Skriv sedan ett serverdels-API för att hämta en åtkomsttoken med hjälp av din prenumerationsnyckel. Du behöver din prenumerationsnyckel och slutpunkt för nästa steg. Du hittar din prenumerationsnyckel på sidan nycklar i din uppslukande Reader-resurs i Azure-portalen. Du kan hitta din slutpunkt på sidan Översikt.

När du har din prenumerationsnyckel och slutpunkt, skapa en ny fil med namnet _.env_, och klistra in följande kod i den genom att ersätta `{YOUR_SUBSCRIPTION_KEY}` och `{YOUR_ENDPOINT}` med din prenumerationsnyckel och slutpunkt, respektive.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

Glöm inte att genomföra den här filen i källkontrollen eftersom den innehåller hemligheter som inte bör göras offentlig.

Därefter öppnar _app.js_ och Lägg till följande överst i filen. Detta läser in prenumerationsnyckel och slutpunkten som miljövariabler i noden.

```javascript
require('dotenv').config();
```

Öppna den _routes\index.js_ fil- och följande importera överst i filen:

```javascript
var request = require('request');
```

Lägg sedan till följande kod direkt under den raden. Den här koden skapar en API-slutpunkt som skaffar en åtkomsttoken med hjälp av din prenumerationsnyckel och returnerar sedan den token.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

Den här API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth](https://oauth.net/2/)); att arbete är utanför omfattningen för den här självstudien.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta uppslukande läsaren innehåll

1. Öppna _views\layout.pug_, och Lägg till följande kod under den `head` tagga innan den `body` tagg. Dessa `script` taggar läsa in den [uppslukande läsare SDK](https://github.com/Microsoft/immersive-reader-sdk) och jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Öppna _views\index.pug_, och Ersätt innehållet med följande kod. Den här koden fylls sidan med exempel innehåll och lägger till en knapp som startar uppslukande läsaren.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. Webbapp är nu klar. Starta appen genom att köra:

    ```bash
    npm start
    ```

4. Öppna webbläsaren och navigera till _http://localhost:3000_ . Du bör se innehållet ovan på sidan. Klicka på den **uppslukande läsare** knappen för att starta uppslukande läsaren med innehållet.

## <a name="specify-the-language-of-your-content"></a>Ange språket för ditt innehåll

Uppslukande läsaren har stöd för många olika språk. Du kan ange språket i ditt innehåll genom att följa stegen nedan.

1. Öppna _views\index.pug_ och Lägg till följande kod nedan i `p(id=content)` tagg som du lade till i föregående steg. Den här koden lägger till en del innehåll spanska innehåll till din sida.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Lägg till följande ovanför anropet till i JavaScript-koden `ImmersiveReader.launchAsync`. Den här koden skickar spanska innehållet i uppslukande läsaren.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Gå till _http://localhost:3000_ igen. Du bör se den spanska texten på sidan och när du klickar på **uppslukande läsare**, den börjar gälla den uppslukande läsaren.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Ange språket för gränssnittet uppslukande läsare

Som standard matchar språket i gränssnittet uppslukande läsare webbläsarens språkinställningar. Du kan också ange språket i gränssnittet uppslukande läsare med följande kod.

1. I _views\index.pug_, Ersätt anropet till `ImmersiveReader.launchAsync(token, content)` med koden nedan.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Navigera till _http://localhost:3000_ . När du startar uppslukande läsaren visas gränssnittet på franska.

## <a name="launch-the-immersive-reader-with-math-content"></a>Starta uppslukande läsaren med matematiska innehåll

Du kan inkludera matematiska innehåll i uppslukande läsaren genom att använda [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Ändra _views\index.pug_ att inkludera följande kod över anropet till `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="http://www.w3.org/1998/Math/MathML" display="block"> \
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

2. Navigera till _http://localhost:3000_ . När du startar uppslukande läsare och bläddra till slutet ser matematiska formeln.

## <a name="next-steps"></a>Nästa steg

* Utforska den [uppslukande läsare SDK](https://github.com/Microsoft/immersive-reader-sdk) och [uppslukande läsare SDK-referens](./reference.md)
* Visa kodexempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)