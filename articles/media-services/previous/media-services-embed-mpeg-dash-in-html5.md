---
title: Bädda in en video med MPEG-streck för anpassningsbar uppspelning i ett HTML5-program med bindestreck. js | Microsoft Docs
description: I det här avsnittet visas hur du bäddar in ett MPEG-STRECKs anpassningsbart strömmande video i ett HTML5-program med bindestreck. js.
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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564864"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Bädda in en video med MPEG-streck för anpassningsbar uppspelning i ett HTML5-program med bindestreck. js  

## <a name="overview"></a>Översikt
MPEG-bindestreck är en ISO-standard för anpassningsbar strömning av video innehåll, vilket ger avsevärda fördelar för utvecklare som vill leverera högkvalitativ, anpassad video strömnings kvalitet. Med MPEG-streck justeras video strömmen automatiskt till en lägre definition när nätverket blir överbelastat. Detta minskar sannolikheten för att visnings programmet ser en "pausad" video medan spelaren laddar ned de kommande sekunderna för uppspelning (det vill säga att det minskar sannolikheten för buffring). När nätverket överbelastas minskar Videos pelaren i tur och tillbaka till en data ström med högre kvalitet. Den här möjligheten att anpassa bandbredden som krävs leder också till en snabbare start tid för video. Det innebär att de första sekunderna kan spelas upp i ett fast-till-nedladdning av lägre kvalitets segment och sedan gå upp till en högre kvalitet när tillräckligt med innehåll har buffrats.

Bindestreck. js är en MPEG-STRECKad video spelare med öppen källkod skriven i Java Script. Målet är att tillhandahålla en robust, plattforms oberoende spelare som kan återanvändas fritt i program som kräver video uppspelning. Den innehåller uppspelning av MPEG-streck i alla webbläsare som stöder W3C-medie käll tillägg (MSE), idag som är Chrome, Microsoft Edge och IE11 (andra webbläsare har angett sin avsikt att stödja MSE). Om du vill ha mer information om bindestreck. js, se JS-databasen GitHub. js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Skapa en webbläsarbaserad strömmande Videos pelare
Om du vill skapa en enkel webb sida som visar en Videos pelare med de förväntade kontrollerna som en uppspelning, paus, återspolning osv., måste du:

1. Skapa en HTML-sida
2. Lägg till video tag gen
3. Lägg till bindestreck. js-spelaren
4. Initiera spelaren
5. Lägg till CSS-format
6. Visa resultaten i en webbläsare som implementerar MSE

Det går bara att utföra en fåtal av rader med JavaScript-kod när spelaren initieras. Med hjälp av bindestreck. js är det mycket enkelt att bädda in video med MPEG-streck i dina webbläsarbaserade program.

## <a name="creating-the-html-page"></a>Skapa HTML-sidan
Det första steget är att skapa en standard-HTML-sida som innehåller **video** elementet, spara filen som basicPlayer. html, som i följande exempel:

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

## <a name="adding-the-dashjs-player"></a>Lägga till bindestreck. js-spelaren
Om du vill lägga till referens implementeringen för bindestreck. js i programmet måste du hämta bindestreck. all. js-filen från den senaste versionen av bindestreck. js-projektet. Detta bör sparas i Java Script-mappen i ditt program. Den här filen är en bekväm fil som drar samman all nödvändig snedstreck. js-kod i en enda fil. Om du har en titt på den. js-lagringsplatsen hittar du de enskilda filerna, test koden och mycket mer, men om du vill göra det kan du använda bindestreck. js. sedan är filen bindestreck. all. js det du behöver.

Lägg till en-skript-tagg till head-avsnittet i basicPlayer. html om du vill lägga till bindestreck. js-spelaren i dina program:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Skapa sedan en funktion för att initiera spelaren när sidan läses in. Lägg till följande skript efter den rad där du läser in streck. alla. js:

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

Den här funktionen skapar först en DashContext. Detta används för att konfigurera programmet för en specifik körnings miljö. Från en teknisk synpunkt definierar den de klasser som beroende inmatnings ramverket ska använda när programmet konstrueras. I de flesta fall använder du bindestreck. di. DashContext.

Sedan instansierar du den primära klassen för bindestreck. js Framework, Media Player. Den här klassen innehåller de kärn metoder som krävs, till exempel Play och Pause, hanterar relationen med video elementet och hanterar även tolkningen av filen med medie presentations beskrivningen (MPD), som beskriver videon som ska spelas upp.

Funktionen Startup () i Media Player-klassen anropas för att säkerställa att spelaren är redo att spela upp video. Bland annat säkerställer funktionen att alla nödvändiga klasser (som definieras av kontexten) har lästs in. När spelaren är klar kan du koppla video-elementet till det med hjälp av funktionen attachView (). Start funktionen gör att Media Player kan mata in video strömmen i-elementet och även styra uppspelningen vid behov.

Skicka webb adressen till MPD-filen till Media Player så att den vet om videon som förväntas spelas upp. Funktionen setupVideo () som nyss skapats måste utföras när sidan har lästs in helt. Gör detta med hjälp av onLoad-händelsen i Body-elementet. Ändra `<body>`-elementet till:

```html
    <body onload="setupVideo()">
```

Ange Slutligen storleken på video elementet med CSS. I en anpassningsbar strömmande miljö är detta särskilt viktigt eftersom storleken på videon som spelas upp kan ändras när uppspelningen anpassas till att ändra nätverks förhållandena. I den här enkla demon tvingar du bara video elementet att vara 80% av det tillgängliga webbläsarfönstret genom att lägga till följande CSS i head-avsnittet på sidan:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Spela upp en video
Om du vill spela upp en video, kan du peka din webbläsare i filen basicPlayback. html och klicka på spela upp i Videos pelaren som visas.

## <a name="media-services-learning-paths"></a>Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även

[GitHub bindestreck. js-lagringsplats](https://github.com/Dash-Industry-Forum/dash.js) 

