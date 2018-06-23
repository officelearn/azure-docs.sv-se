---
title: Bädda in Azure Video indexeraren widgetar i dina program | Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/04/2018
ms.author: juliako
ms.openlocfilehash: 8da06253c58a2de474e879f18013fa1e57f5668d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355638"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Bädda in Video indexeraren widgetar i dina program

Video indexeraren stöder inbäddning två typer av widgetar i tillämpningsprogrammet: **kognitiva insikter** och **Player**. 

* En **kognitiva insikter** widget innehåller alla visual insikter som extraherats från videon indexering processen. 
    Widgeten insikter stöder följande valfria URL-parametrar:

    |Namn|Definition|Beskrivning|
    |---|---|---|
    |widgetar|Strängar avgränsade med kommatecken|Kan du styra de insikter som du vill ska renderas. <br/>Exempel: **widgetar = personer, varumärken** återges endast personer och märken ui insikter<br/>Tillgängliga alternativ: personer, nyckelord, anteckningar, varumärken, våra, betyg, Sök | 
* En **Player** widget gör det möjligt att strömma videon med anpassningsbar bithastighet.

    Widgeten player stöder följande valfria URL-parametrar:

    |Namn|Definition|Beskrivning|
    |---|---|---|
    |T|Sekunder från början|Gör player början spela upp från angiven tid peka.<br/>Exempel: t = 60|
    |etiketter|Språkkod|Hämtar beskrivningen i det angivna språket under widgeten lästes in för att vara tillgängliga i menyn bildtexter.<br/>Exempel: bildtexter = en-Us|
    |showCaptions|Ett booleskt värde|Gör Windows Media player laddas med etiketter som redan har aktiverats.<br/>Exempel: showCaptions = true|
    |typ||Aktiverar ett ljud player skal (video del tas bort).<br/>Exempel: Skriv = ljud|
    |spela upp automatiskt|Ett booleskt värde|Bestäm om spelaren ska börja spela upp videon vid har lästs in (standard är SANT).<br/>Exempel: automatisk uppspelning = false|
    |Språk|Språkkod|Kontroll av player kontroller lokalisering (standard är en-US)<br/>Exempel: språk = de-DE|

## <a name="embedding-public-content"></a>Bädda in offentligt innehåll

1. Logga in på ditt [Video indexeraren](https://api-portal.videoindexer.ai/) konto. 
2. Klicka på knappen ”Bädda in” som visas under videon.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    När du klickar på knappen en embed modal visas på skärmen där du kan välja vilka widget du vill bädda in i ditt program.
    Att välja en widget (**Player** eller **kognitiva insikter**), genererar en inbäddad kod som du kan klistra in i ditt program.
 
3. Välj typ av widget som du vill (**kognitiva insikter** eller **Player**).
4. Kopiera den inbäddade koden och Lägg till ditt program. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Bädda in personligt innehåll

Du kan hämta bädda in koder från bädda in popup-fönster (som visas i föregående avsnitt) för **offentliga** endast videor. 

Om du vill bädda in en **privata** video-du måste skicka en åtkomst-token i den **iframe**'s **src** attribut:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Använd den [ **få insikter Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API för att hämta kognitiva insikter widget innehållet, eller Använd [ **hämta Video åtkomst-Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) och lägga till som som en fråga param till url som visas ovan. Ange Webbadressen som den **iframe**'s **src** värde.

Om du vill ge redigera insikter funktioner (t.ex. Vi har i vår webbprogram) i din inbäddade widget behöver du skicka en åtkomst-token med redigeringen av behörighet. Använd [ **få insikter Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) eller [ **hämta Video åtkomst-Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) med **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Widgetar interaktion

Den **kognitiva insikter** widget kan interagera med en video på ditt program. Det här avsnittet visar hur du utför den här interaktionen.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Cross-origin-kommunikation

För att få Video indexeraren widgetar att kommunicera med andra komponenter, gör tjänsten Video indexerare följande:

- Använder cross-origin-kommunikation HTML5-metoden **postMessage** och 
- Verifierar meddelandet över VideoIndexer.ai ursprung. 

Om du vill implementera player koden och utföra integrering med **kognitiva insikter** widgetar, är det ditt ansvar att validera det meddelande som kommer från VideoIndexer.ai ursprung.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Bädda in båda typerna av widgetar i ditt program / blogg (rekommenderas) 

Det här avsnittet visar hur du uppnå interaktion mellan två Video indexeraren widgetar så när en användare klickar på kontrollen inblick i ditt program, Windows Media player leder till relevant tidpunkt.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopiera den **Player** widget bädda in kod.
2. Kopiera den **kognitiva insikter** bädda in kod.
3. Lägg till den [ **Mediator filen** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) att hantera kommunikationen mellan två widgetar:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Nu när användaren klickar på kontrollen inblick i ditt program, leder Windows Media player till relevant tidpunkt.

Mer information finns i [i den här demon](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in widgeten kognitiva insikter och använda Azure Media Player spela upp innehållet

Det här avsnittet visas hur du uppnår interaktionen mellan en **kognitiva insikter** widget och ett Azure Media Player-instans med den [AMP plugin](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Lägga till ett Video indexeraren plugin-program för Windows Media player AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Skapa en instans av Azure Media Player med Video indexeraren plugin-programmet.

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

3. Kopiera den **kognitiva insikter** bädda in kod.

Du borde nu för att kommunicera med Azure Media Player.

Mer information finns i [i den här demon](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Bädda in Video indexeraren kognitiva insikter widget och använder egna player (kan vara en spelare)

Om du använder egna player, måste du ta hand om manipulering spelaren själv för att uppnå kommunikationen. 

1. Infoga din videospelare.

    Till exempel en vanlig HTML5-spelare

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bädda in widgeten kognitiva insikter.
3. Implementera kommunikation för spelaren genom att lyssna på händelsen ”meddelandet”. Exempel:

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


Mer information finns i [i den här demon](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Lägga till textning

Om du bädda in Video indexeraren insikter med AMP spelaren kan du använda den **GetVttUrl** metod för att hämta textning (textning). Du kan också anropa en javascript-metod från Video indexeraren AMP plugin-programmet **getSubtitlesUrl** (som visas tidigare). 

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbart widgetar

### <a name="cognitive-insights-widget"></a>Kognitiva insikter widget
Du kan välja vilka typer av information som du vill genom att ange dem som ett värde till följande URL-parametern till den inbäddade koden du får (från API: et eller från webbprogrammet):

**& widgetar =** \<lista över önskade widgetar >

Möjliga värden är: personer nyckelord, våra, utskrifter, sökning.

Till exempel om du vill bädda in en widget som innehåller endast personer och Sök insikter iframe bädda in URL: en kommer se ut så här: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, Sök

Rubrik på fönstret iframe kan även anpassas genom att tillhandahålla **& title =** <YourTitle> till iframe-url. (Den anpassar html \<title > värdet).
Till exempel om du vill ge din iframe-fönstret rubriken ”MyInsights” URL-adressen ser ut så här: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Observera att det här alternativet gäller endast i fall när du behöver öppna insikter som är i ett nytt fönster.

### <a name="player-widget"></a>Player widget
Om du bädda in Video indexeraren player du storleken på Windows Media player genom att ange storleken på iframe.

Exempel:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Som standard Video indexeraren player kommer har automatiskt genererat textning baserat på betyg i videon som har extraherats från video med källan språket som valdes när videon har överförts.

Om du vill bädda in med ett annat språk kan du lägga till **& bildtexter = < språk | ”alla” | ”false” >** att bädda in player URL eller placera ”alla” som värde om du vill ha alla tillgängliga språk beskrivningar.
Om du vill att beskrivningar som ska visas som standard kan du skicka **& showCaptions = true**

Bädda in URL: en sedan ser ut så här: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Om du vill inaktivera bildtexter kan du överföra ”false” som värde för parametern bildtexter.

Spela upp automatiskt – som standard spelaren börjar spelas upp videon. Du kan välja att skicka & Spela upp automatiskt = false för att bädda in URL: en ovan.

## <a name="next-steps"></a>Nästa steg

Information om hur du visa och redigera Video indexeraren insikter finns [detta](video-indexer-view-edit.md) artikel.

## <a name="see-also"></a>Se också

[Indexeraren videoöversikt](video-indexer-overview.md)
