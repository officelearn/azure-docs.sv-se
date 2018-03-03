---
title: "Bädda in en MPEG-DASH anpassningsbar direktuppspelad Video i ett HTML5-program med DASH.js | Microsoft Docs"
description: "Det här avsnittet visar hur du bäddar in en MPEG-DASH anpassningsbar direktuppspelad Video i ett HTML5-program med DASH.js."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 499c78737b95885b753589c06f2614ce917adfea
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Bädda in en MPEG-DASH anpassningsbar direktuppspelad Video i ett HTML5-program med DASH.js
## <a name="overview"></a>Översikt
MPEG-DASH är en ISO-standarden för anpassningsbar strömning av video innehållet, vilket medför betydande fördelar för utvecklare som vill leverera hög kvalitet, anpassningsbar videoströmmar utdata. Med MPEG-DASH justeras video-ström automatiskt till en lägre definition när nätverket blir överbelastad. Detta minskar sannolikheten för visningsprogrammet se en video som ”pausad” medan hämtas nästa några sekunder att spela upp (aka buffert). Eftersom överbelastning minskar återgår i sin tur video spelaren till en dataström med högre kvalitet. Den här möjligheten att anpassa den bandbredd som krävs också resulterar i en snabbare starttid för video. Som innebär att några sekunder kan spelas upp i ett fast hämta lägre kvalitet segment och sedan steg upp till en högre kvalitet när tillräckligt innehåll har buffrats.

Dash.js är en öppen källkod MPEG-DASH videospelare skriven i JavaScript. Syftet är att tillhandahålla en robust, plattformsoberoende spelare som fritt kan återanvändas i program som kräver uppspelning av video. Det ger MPEG-DASH uppspelning i valfri webbläsare som stöder W3C Media källa tillägg (mus) idag Chrome, Microsoft Edge och IE11 (sin avsikt att stödja mus har angett i andra webbläsare). Mer information om DASH.js finns js dash.js GitHub-lagret.

## <a name="creating-a-browser-based-streaming-video-player"></a>Skapa en webbläsarbaserad strömmande videospelare
Om du vill skapa en enkel webbsida som visar en videospelare med den förväntade styr dessa en play, pausa, tillbakaspolning etc., måste du:

1. Skapa en HTML-sida
2. Lägg till taggen video
3. Lägg till dash.js player
4. Starta Windows Media player
5. Lägga till vissa CSS-format
6. Visa resultaten i en webbläsare som implementerar mus

Initierar Windows Media player kan utföras i bara ett fåtal rader med JavaScript-kod. Med dash.js verkligen är det enkelt att bädda in video MPEG-DASH i dina webbläsarbaserat program.

## <a name="creating-the-html-page"></a>Skapa HTML-sida
Det första steget är att skapa en vanlig HTML-sida som innehåller den **video** element, spara filen som basicPlayer.html, som i följande exempel visar:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Lägga till DASH.js Player
Om du vill lägga till dash.js referens-implementering i programmet måste du hämta filen dash.all.js från version 1.0 av dash.js projekt. Detta ska sparas i mappen JavaScript i ditt program. Den här filen är en bekvämlighet som sammanställer alla nödvändiga dash.js koden i en enda fil. Om du har en titt runt dash.js databasen kan du hitta de enskilda filerna, testa koden och mycket mer, men om du vill göra är använda dash.js, är dash.all.js filen vad du behöver.

Lägg till en skripttagg basicPlayer.html head-avsnittet för att lägga till spelaren dash.js för dina program:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Skapa sedan en funktion för att initiera spelaren när sidan läses in. Lägg till följande skript efter raden som du kan läsa in dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Den här funktionen skapar först ett DashContext. Används för att konfigurera program för en specifik körningsmiljö. Den definierar de klasser som beroende injection framework ska användas för att konstruera programmet från en teknisk synvinkel. I de flesta fall använder du Dash.di.DashContext.

Därefter instansiera primära klassen för ramverket dash.js Media Player. Den här klassen innehåller metoder som behövs spela upp och pausa, hanterar relationen med elementet video och hanterar också för tolkningen av filen Media Presentation beskrivning (MPD), som beskriver videon ska spelas upp.

Funktionen startup() av Media Player-klassen anropas för att säkerställa att spelaren är redo att spela upp video. Bland annat säkerställer funktionen att alla nödvändiga klasserna (som definieras av kontexten) har lästs in. När Windows Media player är klar kan bifoga du video elementet till den med hjälp av funktionen attachView(). Start-funktionen kan Media Player att mata in video-ström i elementet och också styra uppspelning vid behov.

Skicka URL till MPD-fil till de Media Player så att den vet om videon det förväntas att spela upp. Funktionen setupVideo() skapade måste utföras när sidan har lästs in helt. Du kan göra detta med hjälp av onload-händelse body-elementet. Ändra din <body> elementet så att:

```html
    <body onload="setupVideo()">
```

Slutligen, anger du storleken på elementet video med hjälp av CSS. Detta är särskilt viktigt i en anpassningsbar strömning miljö, eftersom storleken på video som spelas kan ändras när uppspelning anpassar sig till ändrade nätverksförhållanden. I den här enkel demo bara tvinga video elementet ska vara 80% av det tillgängliga webbläsarfönstret genom att lägga till följande CSS head-avsnittet av sidan:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Spela upp en Video
Om du vill spela upp en video webbläsaren basicPlayback.html-filen och klicka på play på videospelare visas.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Utveckla videospelarprogram](media-services-develop-video-players.md)

[GitHub dash.js-databas](https://github.com/Dash-Industry-Forum/dash.js) 

