---
title: Bädda in Azure Video Indexer widgetar i ditt program | Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 4ce1eedac69b06f491510be93fcfbdfbc879ff7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398140"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Bädda in Video Indexer widgetar i dina program

Video Indexer stöder bädda in två typer av widgetar i ditt program: **kognitiva insikter** och **Player**. 

* En **kognitiva insikter** widget innehåller alla visuella insikter som extraherades från videon indexering processen. 
    Widgeten insights har stöd för följande valfria URL-parametrar:

    |Namn|Definition|Beskrivning|
    |---|---|---|
    |widgetar|Strängar avgränsade med kommatecken|Låter dig styra de insikter som du vill rendera. <br/>Exempel: **widgetar = personer, varumärken** renderas endast personer och varumärken användargränssnittet insikter<br/>Tillgängliga alternativ: personer, nyckelord, kommentarer, varumärken, Sentiment, avskrift, Sök | 
* En **Player** widget gör det möjligt att strömma videon med anpassningsbar bithastighet.

    Widgeten spelare har stöd för följande valfria URL-parametrar:

    |Namn|Definition|Beskrivning|
    |---|---|---|
    |T|Sekunder från början|Gör player början spela upp från angiven tid peka.<br/>Exempel: t = 60|
    |undertexter|Språkkod|Hämtar beskrivning i det angivna språket under widgeten läser in för att vara tillgängliga i menyn bildtexter.<br/>Exempel: bildtexter = en-Us|
    |showCaptions|Ett booleskt värde|Gör spelaren läsa in med bildtexter som redan har aktiverats.<br/>Exempel: showCaptions = true|
    |typ||Aktiverar ett ljudspelare skal (video del tas bort).<br/>Exempel: Skriv = ljud|
    |spela upp automatiskt|Ett booleskt värde|Bestäm om spelaren ska spela upp videon vid har lästs in (standard är SANT).<br/>Exempel: spela upp automatiskt = false|
    |Språk|Språkkod|Kontrollen spelaren styr lokalisering (standard är en-US)<br/>Exempel: språk = de-DE|

## <a name="embedding-public-content"></a>Bädda in offentligt innehåll

1. Logga in på din [Video Indexer](https://api-portal.videoindexer.ai/) konto. 
2. Klicka på knappen ”Bädda in” som visas under videon.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    När du klickar på knappen, en inbäddad modal visas på skärmen där du kan välja vilka widget du vill bädda in i ditt program.
    Att välja en widget (**Player** eller **kognitiva insikter**), genererar en inbäddad kod som du kan klistra in i ditt program.
 
3. Välj typ av widget som du vill (**kognitiva insikter** eller **Player**).
4. Kopiera inbäddningskoden och lägga till i ditt program. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Bädda in personligt innehåll

Du kan hämta bädda in koder från bädda in popup-fönster (som visas i föregående avsnitt) för **offentliga** endast videor. 

Om du vill bädda in en **privata** video och du måste utföra en åtkomst-token i den **iframe**'s **src** attribut:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Använd den [ **hämta insikter Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API för att hämta widgetinnehåll kognitiva insikter eller Använd [ **hämta Video åtkomsttoken** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) och lägga till den som en fråga param till url som visas ovan. Ange URL: en som den **iframe**'s **src** värde.

Om du vill tillhandahålla redigering insikter funktioner (t.ex. Vi har i vårt webbprogram) i din inbäddade widget måste du skicka en åtkomst-token med redigeringsbehörigheter. Använd [ **hämta insikter Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) eller [ **hämta Video åtkomsttoken** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) med **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Widgetar interaktion

Den **kognitiva insikter** widget kan interagera med en video om ditt program. Det här avsnittet visar hur du utför den här interaktionen.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Resursdelning för korsande ursprung kommunikation

För att få Video Indexer widgetar att kommunicera med andra komponenter, gör Video Indexer-tjänsten följande:

- Använder resursdelning för korsande ursprung kommunikation HTML5-metoden **postMessage** och 
- Verifierar meddelandet över VideoIndexer.ai ursprung. 

Om du väljer att implementera dina egna player-kod och genomföra integrering med **kognitiva insikter** widgetar, det är ditt ansvar att verifiera ursprunget för det meddelande som kommer från VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Bädda in båda typerna av widgetar i ditt program / blogg (rekommenderas) 

Det här avsnittet visas hur du uppnår interaktion mellan två Video Indexer widgetar det när en användare klickar på kontrollen insyn i ditt program, spelaren leder till relevant nu.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopiera den **Player** widget inbäddningskod.
2. Kopiera den **kognitiva insikter** inbäddningskod.
3. Lägg till den [ **Mediator filen** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) att hantera kommunikationen mellan de två widgetarna:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

När en användare klickar på kontrollen insyn i ditt program nu leder spelaren till relevant nu.

Mer information finns i [den här demon](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in widgeten kognitiva insikter och använda Azure Media Player för att spela upp innehållet

Det här avsnittet visas hur du uppnår interaktion mellan en **kognitiva insikter** widget och ett Azure Media Player instans med hjälp av den [AMP-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Lägg till en Video Indexer-plugin-programmet för AMP spelaren.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Skapa en instans av Azure Media Player med Video Indexer-plugin-programmet.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Kopiera den **kognitiva insikter** inbäddningskod.

Du borde nu för att kommunicera med Azure Media Player.

Mer information finns i [den här demon](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Bädda in Video Indexer kognitiva insikter widget och Använd din egen spelare (kan vara en spelare)

Om du använder egna player kan du behöva ta hand om manipulera spelaren själv för att uppnå kommunikationen. 

1. Infoga dina videospelaren.

    Till exempel en standard HTML5-spelare

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bädda in widgeten kognitiva insikter.
3. Implementera kommunikation för dina player genom att lyssna på händelsen ”message”. Exempel:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>


Mer information finns i [den här demon](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Att lägga till undertexter

Om du bäddar in videoindexering med din egen AMP player kan du använda den **GetVttUrl** metod för att hämta dold textning (undertexter). Du kan också anropa en javascript-metod från plugin-programmet Video Indexer och **getSubtitlesUrl** (som visas tidigare). 

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddade widgetar

### <a name="cognitive-insights-widget"></a>Kognitiva insikter widget
Du kan välja vilka typer av insikter som du vill genom att ange dem som ett värde till följande URL-parameter som lagts till i den inbäddade koden du får (från API: et eller från webbprogrammet):

**& widgetar =** \<lista över önskade widgetar >

Möjliga värden är: personer, nyckelord, sentiment, avskrift, sökning.

Till exempel om du vill bädda in en widget som innehåller endast personer och Sök efter insikter iframe bädda in URL: en ut så här: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, Sök

Rubriken på fönstret iframe kan också anpassas genom att tillhandahålla **& Rubrik =** <YourTitle> till iframe-url. (Det anpassar html \<title > värde).
Till exempel om du vill ge din iframe-fönstret rubriken ”MyInsights” URL: en ut så här: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Observera att det här alternativet gäller endast i fall när du behöver att öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Player-widget
Om du bäddar in Video Indexer player kan du välja storleken på spelaren genom att ange storleken på iframe.

Till exempel:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Som standard Video Indexer player kommer har Autogenererad dold textning baserat på avskrifter av videon som har extraherats från videon med källspråk som valdes när videon har överförts.

Om du vill bädda in med ett annat språk kan du lägga till **& undertexter = < språk | ”alla” | ”false” >** till URL: en för inbäddade spelare eller put ”alla” som värde om du vill ha alla tillgängliga språk bildtexter.
Om du vill att undertexter som ska visas som standard kan du skicka **& showCaptions = true**

Bädda in URL: en sedan ser ut så här: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Om du vill inaktivera undertexter kan du skicka ”false” som värde för parametern för bildtexter.

Automatisk play – som standard spelaren börjar spelas upp videon. Du kan välja att skicka & automatisk uppspelning = false om du vill bädda in URL: en ovan.

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar videoindexering finns i [detta](video-indexer-view-edit.md) artikeln.

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)
