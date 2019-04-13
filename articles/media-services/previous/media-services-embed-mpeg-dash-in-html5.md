---
title: Bädda in en MPEG-DASH-anpassad direktuppspelad Video i ett HTML5-program med DASH.js | Microsoft Docs
description: Det här avsnittet visar hur du bäddar in en MPEG-DASH-anpassad direktuppspelad Video i ett HTML5-program med DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f521fd11a2053cf8cf1ea0f9f91667fe475f0eee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522443"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Bädda in en MPEG-DASH-anpassad direktuppspelad Video i ett HTML5-program med DASH.js  

## <a name="overview"></a>Översikt
MPEG-DASH är en ISO-standarden för anpassad strömning av video innehållet, vilket ger betydande fördelar för utvecklare som vill leverera högkvalitativa, anpassningsbar videoströmning utdata. Med MPEG-DASH, videoströmmen anpassar sig automatiskt till en lägre definition när nätverket blir överbelastad. Detta minskar risken för användaren ser ett ”pausat” video medan spelaren hämtar nästa några sekunder för att spela upp (även kallat buffring). Som överbelastning på nätverket minskar videospelaren i sin tur att återgå till en dataström med högre kvalitet. Den här möjligheten att anpassa den bandbredd som krävs resulterar också i en snabbare starttid för video. Som innebär att några sekunder kan spelas upp i ett fast att hämta lägre kvalitet segment och sedan steget upp till en högre kvalitet när tillräckligt med innehåll har buffrats.

Dash.js är en öppen källkod MPEG-DASH videospelaren skriven i JavaScript. Målet är att tillhandahålla en robust, plattformsoberoende spelare som kan återanvändas fritt i program som kräver videouppspelning. Det ger MPEG-DASH-uppspelning i alla webbläsare som stöder W3C Media källa tillägg (MSE) idag som är Chrome, Microsoft Edge och IE11 (andra webbläsare har angett sin avsikt att stödja MSE). Mer information om DASH.js Se js dash.js GitHub-lagringsplatsen.

## <a name="creating-a-browser-based-streaming-video-player"></a>Skapa en webbläsarbaserad strömmande videospelare
Om du vill skapa en enkel webbsida som visar en videospelare med den förväntade styr sådana en play, pausa, tillbakaspolning etc., måste du:

1. Skapa en HTML-sida
2. Lägg till videotaggen
3. Lägg till spelaren dash.js
4. Initiera spelaren
5. Lägga till vissa CSS-format
6. Visa resultatet i en webbläsare som implementerar MSE

Initierar spelaren kan utföras på bara ett fåtal rader med JavaScript-kod. Med dash.js kan är det verkligen det enkelt att bädda in MPEG-DASH i dina webbläsarbaserade program.

## <a name="creating-the-html-page"></a>Skapa HTML-sida
Det första steget är att skapa en standard HTML-sida som innehåller den **video** element, spara filen som basicPlayer.html, som i följande exempel visar:

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

## <a name="adding-the-dashjs-player"></a>Att lägga till spelaren DASH.js
Om du vill lägga till referensimplementering dash.js till programmet, måste du hämta filen dash.all.js från den senaste versionen av dash.js projekt. Detta ska sparas i mappen JavaScript för ditt program. Den här filen är en bekvämlighet-fil som sammanställer alla nödvändiga dash.js koden i en enda fil. Om du har en titt runt dash.js databasen kan du hitta enskilda filer, testa koden och mycket mer, men om du vill göra är använda dash.js, och sedan filen dash.all.js är vad du behöver.

Om du vill lägga till dash.js spelaren i dina program, lägger du till ett skripttagg basicPlayer.html head-avsnitt:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Skapa sedan en funktion för att initiera spelaren när sidan läses in. Lägg till följande skript efter den rad där du läser in dash.all.js:

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

Den här funktionen skapar först en DashContext. Detta används för att konfigurera program för en specifik körningsmiljö. Den definierar de klasser som beroende inmatning framework ska använda när programmet från en teknisk synvinkel. I de flesta fall kan använda du Dash.di.DashContext.

Därefter skapa en instans av den primära klassen av dash.js framework, Media Player. Den här klassen innehåller metoder som krävs till exempel spela och pausa, hanterar relationen med videoelementet och hanterar också tolkningen av filen Media Presentation beskrivning (MPD), som beskriver videon spelas upp.

Funktionen startup() för Media Player-klassen anropas för att säkerställa att spelaren är redo att spela upp video. Bland annat säkerställer funktionen att alla nödvändiga klasserna (som definieras av kontexten) har lästs in. När spelaren är klar, kan du koppla videoelementet till den med hjälp av funktionen attachView(). Start-funktionen kan Media Player och mata in videoströmmen i elementet som du kan också styra uppspelning vid behov.

Skicka URL till MPD-fil till den Media Player så att den vet om videon och det förväntas att spela upp. Funktionen setupVideo() nyss skapade måste utföras när sidan har lästs in helt. Det gör du genom VidStart av body-elementet. Ändra din `<body>` element till:

```html
    <body onload="setupVideo()">
```

Slutligen ange storleken på elementet video med hjälp av CSS. Detta är särskilt viktigt i en anpassningsbar direktuppspelning miljö, eftersom storleken på den video som spelas kan ändras när uppspelning anpassas på förändrade nätverksförhållanden. I den här enkel demo bara tvinga videoelementet vara 80% av det tillgängliga webbläsarfönstret genom att lägga till följande CSS head-avsnitt på sidan:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Spela upp en Video
Spela upp en video, väljer din webbläsare till basicPlayback.html-filen och klicka på Spela upp på videospelaren visas.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Utveckla videospelarprogram](media-services-develop-video-players.md)

[GitHub-lagringsplatsen för dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

