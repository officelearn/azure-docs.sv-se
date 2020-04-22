---
title: Fullständiga inställningar för Azure Media Player
description: Läs om hur du konfigurerar Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727247"
---
# <a name="azure-media-player-full-setup"></a>Full installation av Azure Media Player #

Azure Media Player är enkelt att konfigurera. Det tar bara en liten stund att få grundläggande uppspelning av medieinnehåll direkt från ditt Azure Media Services-konto. [Prover](https://github.com/Azure-Samples/azure-media-player-samples) finns också i exempelkatalogen för frisläppandet.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Steg 1: Inkludera JavaScript- och CSS-filerna i sidhuvudet ##

Med Azure Media Player kan du komma åt skripten från CDN-värdversionen. Det rekommenderas ofta nu att sätta JavaScript `<body>` före slutet `<head>`kroppen taggen i stället för , men Azure Media Player innehåller en "HTML5 Shiv", som måste vara i huvudet för äldre IE-versioner att respektera videotaggen som ett giltigt element.

> [!NOTE]
> Om du redan använder en HTML5-shiv som [Modernizr](http://modernizr.com/) kan du inkludera JavaScript för Azure Media Player var som helst. Men se till att din version av Modernizr innehåller kniv för video.

### <a name="cdn-version"></a>CDN-version ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> Du **NOT** bör INTE `latest` använda den version i produktion, eftersom detta kan ändras på begäran. Ersätt `latest` med en version av Azure Media Player. Ersätt till `latest` exempel `2.1.1`med . Azure Media Player-versioner kan efterfrågas [härifrån](azure-media-player-changelog.md).

> [!NOTE]
> Eftersom `1.2.0` versionen är det inte längre nödvändigt att inkludera platsen till reservteknikerna (den hämtar automatiskt platsen från den relativa sökvägen till azuremediaplayer.min.js-filen). Du kan ändra platsen för reservtekniker genom att `<head>` lägga till följande skript i skriptet efter ovanstående.

> [!NOTE]
> På grund av den typ av Flash och Silverlight plugins, swf och xap filer bör vara värd på en domän utan känslig information eller data - detta automatiskt tas hand om för dig med Azure CDN värdversion.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Steg 2: Lägga till en HTML5-videotagg på sidan ##

Med Azure Media Player kan du använda en HTML5-videotagg för att bädda in en video. Azure Media Player kommer sedan att läsa taggen och få den att fungera i alla webbläsare, inte bara de som stöder HTML5-video. Utöver den grundläggande markeringen behöver Azure Media Player några extra bitar.

1. Attributet `<data-setup>` på `<video>` talar om för Azure Media Player att automatiskt ställa in videon när sidan är klar och läsa alla (i JSON-format) från attributet.
1. Attributet: `id` Ska användas och unikt för varje video på samma sida.
1. Attributet `class` innehåller två klasser:
    - `azuremediaplayer`tillämpar format som krävs för Azure Media Player-gränssnittsfunktioner
    - `amp-default-skin`använder standardskalet på HTML5-kontrollerna
1. Den `<source>` innehåller två obligatoriska attribut
    - `src`attribut kan innehålla en **.ism/manifestfil* från Azure Media Services läggs till, Azure Media Player lägger automatiskt till url:erna för DASH, SMOOTH och HLS i spelaren
    - `type`är den nödvändiga MIME-typen av dataströmmen. MIME-typen som associeras med *".ism/manifest"* är *"application/vnd.ms-sstr+xml"*
1. Det *valfria attributet* `<data-setup>` på `<source>` talar om för Azure Media Player om det finns några unika leveransprinciper för strömmen från Azure Media Services, inklusive, men inte begränsat till, krypteringstyp (AES eller PlayReady, Widevine eller FairPlay) och token.

Inkludera/exkludera attribut, inställningar, källor och spår precis som för HTML5-video.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Som standard finns den stora uppspelningsknappen i det övre vänstra hörnet så att den inte täcker upp de intressanta delarna av affischen. Om du föredrar att centrera den stora uppspelningsknappen kan du lägga till ytterligare ett `amp-big-play-centered` `class` element. `<video>`

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternativ installation för dynamiskt inläst HTML ###

Om din webbsida eller ditt program läser in videotaggen dynamiskt (ajax, adddChild, etc.), så att den inte kan finnas när sidan läses in, vill du manuellt ställa in spelaren istället för att förlita sig på attributet data-setup. För att göra detta, först ta bort data-setup attributet från taggen så det finns ingen förvirring runt när spelaren initieras. Kör sedan följande JavaScript en tid efter att Javascript för Azure Media Player har lästs in och efter att videotaggen har lästs in i DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Det första argumentet `amp` i funktionen är ID:t för videotaggen. Byt ut den mot din egen.

Det andra argumentet är ett alternativobjekt. Det låter dig ange ytterligare alternativ som du kan med attributet datainställningar.

Det tredje argumentet är en "klar" motringning. När Azure Media Player har initierats anropas den här funktionen. I det färdiga motringningsobjektet refererar "detta" till spelarförekomsten.

I stället för att använda ett element-ID kan du också skicka en referens till själva elementet.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)