---
title: Bädda in en MPEG-DASH Adaptiv strömmande video i ett HTML5-program med DASH.js | Microsoft-dokument
description: Det här avsnittet visar hur du bäddar in en MPEG-DASH Adaptive Streaming Video i ett HTML5-program med DASH.js.
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
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564864"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js  

## <a name="overview"></a>Översikt
MPEG-DASH är en ISO-standard för adaptiv strömning av videoinnehåll, vilket ger betydande fördelar för utvecklare som vill leverera högkvalitativ, adaptiv videostreamingutgång. Med MPEG-DASH justeras videoströmmen automatiskt till en lägre definition när nätverket blir överbelastat. Detta minskar sannolikheten för att tittaren ser en "pausad" video medan spelaren hämtar de närmaste sekunderna för att spela upp (det vill ha minskar sannolikheten för buffring). I takt med att nätverksbelastningen minskar återgår videospelaren i sin tur till en ström av högre kvalitet. Denna förmåga att anpassa den bandbredd som krävs resulterar också i en snabbare starttid för video. Det innebär att de första sekunderna kan spelas i en snabb att ladda ner lägre kvalitet segment och sedan steg upp till en högre kvalitet när tillräckligt innehåll har buffrats.

Dash.js är en MPEG-DASH-videospelare med öppen källkod skriven i JavaScript. Dess mål är att tillhandahålla en robust, plattformsoberoende spelare som fritt kan återanvändas i program som kräver videouppspelning. Det ger MPEG-DASH uppspelning i alla webbläsare som stöder W3C Media Source Extensions (MSE), idag som är Chrome, Microsoft Edge och IE11 (andra webbläsare har angett sin avsikt att stödja MSE). Mer information om DASH.js finns i GitHub dash.js-databasen.

## <a name="creating-a-browser-based-streaming-video-player"></a>Skapa en webbläsarbaserad strömmande videospelare
För att skapa en enkel webbsida som visar en videospelare med de förväntade kontrollerna en sådan uppspelning, paus, spola tillbaka etc., måste du:

1. Skapa en HTML-sida
2. Lägg till videotaggen
3. Lägg till dash.js-spelaren
4. Initiera spelaren
5. Lägg till lite CSS-format
6. Visa resultaten i en webbläsare som implementerar MSE

Initiera spelaren kan slutföras på bara en handfull rader JavaScript-kod. Med dash.js är det verkligen så enkelt att bädda in MPEG-DASH-video i dina webbläsarbaserade program.

## <a name="creating-the-html-page"></a>Skapa HTML-sidan
Det första steget är att skapa **video** en standard HTML-sida som innehåller videoelementet, spara den här filen som basicPlayer.html, som följande exempel illustrerar:

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

## <a name="adding-the-dashjs-player"></a>Lägga till DASH.js-spelaren
Om du vill lägga till referensimplementeringen för dash.js i programmet måste du hämta filen dash.all.js från den senaste versionen av dash.js-projektet. Detta bör sparas i JavaScript-mappen i ditt program. Denna fil är en bekvämlighet fil som drar ihop alla nödvändiga dash.js kod i en enda fil. Om du har en titt runt dash.js-arkivet hittar du de enskilda filerna, testkoden och mycket mer, men om allt du vill göra är att använda dash.js, så är dash.all.js-filen vad du behöver.

Om du vill lägga till dash.js-spelaren i dina program lägger du till en skripttagg i huvudavsnittet i basicPlayer.html:

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

Den här funktionen skapar först en DashContext. Detta används för att konfigurera programmet för en specifik körningsmiljö. Ur teknisk synvinkel definierar den de klasser som beroendeinjektionsramen ska använda när programmet byggs. I de flesta fall använder du Dash.di.DashContext.

Därefter instansierar du den primära klassen för dash.js-ramverket, MediaPlayer. Den här klassen innehåller de grundläggande metoder som behövs, till exempel uppspelning och paus, hanterar relationen med videoelementet och hanterar även tolkningen av MPD-filen (Media Presentation Description), som beskriver videon som ska spelas upp.

Funktionen startup() för klassen MediaPlayer anropas för att säkerställa att spelaren är redo att spela upp video. Funktionen säkerställer bland annat att alla nödvändiga klasser (enligt sammanhanget definieras). När spelaren är klar kan du ansluta videoelementet till den med funktionen attachView(). Startfunktionen gör det möjligt för MediaPlayer att injicera videoströmmen i elementet och även styra uppspelningen efter behov.

Skicka URL:en till MPD-filen till MediaPlayer så att den vet om videon som den förväntas spela upp. Den setupVideo() funktion som just skapats måste köras när sidan har lästs in helt. Gör detta genom att använda onload händelsen av kroppselementet. Ändra `<body>` elementet till:

```html
    <body onload="setupVideo()">
```

Slutligen anger du storleken på videoelementet med CSS. I en anpassningsbar strömningsmiljö är detta särskilt viktigt eftersom storleken på den video som spelas upp kan ändras när uppspelningen anpassas till förändrade nätverksförhållanden. I denna enkla demo helt enkelt tvinga videoelementet att vara 80% av det tillgängliga webbläsarfönstret genom att lägga till följande CSS till huvudavsnittet på sidan:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Spela upp en video
Om du vill spela upp en video pekar du webbläsaren på filen basicPlayback.html och klickar på play på videospelaren som visas.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även

[GitHub dash.js-databas](https://github.com/Dash-Industry-Forum/dash.js) 

