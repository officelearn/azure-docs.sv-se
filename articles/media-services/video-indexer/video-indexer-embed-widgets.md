---
title: Bädda in Video Indexer widgetar i dina program
titlesuffix: Azure Media Services
description: Lär dig hur du bäddar in Video Indexer-widgetar i ett program.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: a051f40cb5586cae58d8e4939f4fcee35438bf69
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292517"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Bädda in Video Indexer widgetar i dina program

Den här artikeln visar hur du kan bädda in Video Indexer-widgetar i program. Video Indexer har stöd för inbäddning av två typer av widgetar i program: **insikter** och **spelare**. 
## <a name="widget-types"></a>Widgettyper

### <a name="cognitive-insights-widget"></a>Insiktswidget

En **insikts**widget innehåller alla visuella insikter som extraherades från videoindexeringsprocessen. Insiktswidgeten har stöd för följande valfria URL-parametrar:

|Namn|Definition|Beskrivning|
|---|---|---|
|widgets|Strängar avgränsade med kommatecken|Gör att du kan styra vilka insikter du vill rendera. <br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` renderar endast insikter om personer och varumärken<br/>Tillgängliga alternativ: people, keywords, annotations, brands, sentiments, transcript, search.<br/>stöds inte via URL för version = 2<br/><br/>**Obs!** URL-parametern **widgets** stöds inte om **version = 2** används. |
|version|Versioner av **insikts**widgeten|Om du vill få de senaste uppdateringarna för insiktswidgeten lägger du till frågeparametern `?version=2` i inbäddnings-URL:en. Till exempel, `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2` <br/> Om du vill få den tidigare versionen tar du bort `version=2` från URL:en.

### <a name="player-widget"></a>Spelarwidget

Med en **spelar**widget kan du strömma videon med anpassningsbar bithastighet. Spelarwidgeten har stöd för följande valfria URL-parametrar:

|Namn|Definition|Beskrivning|
|---|---|---|
|t|Sekunder från början|Gör att spelaren börjar spela upp från angiven tidpunkt.<br/>Exempel: t=60|
|captions|Språkkod|Hämtar textningen på det angivna språket under inläsning av widgeten så att den blir tillgänglig på textningsmenyn.<br/>Exempel: captions=sv-SE|
|showCaptions|Ett booleskt värde|Gör att spelaren läses in med aktiverad textning.<br/>Exempel: showCaptions=true|
|typ||Aktiverar ett ljudspelarskal (videodelen tas bort).<br/>Exempel: type=audio|
|autoplay|Ett booleskt värde|Anger om spelaren ska börja spela upp videon när den har lästs in (standard är true).<br/>Exempel: autoplay=false|
|language|Språkkod|Styr spelarens språk (standard är en-US)<br/>Exempel: language=de-DE|

## <a name="embedding-public-content"></a>Bädda in offentligt innehåll

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Klicka på videon som du vill arbeta med.
3. Klicka på knappen Bädda in som visas nedanför videon.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    När du klickar på knappen visas en inbäddningsmodal på skärmen där du kan välja vilken widget du vill bädda in i programmet.
    När du väljer en widget (**spelare** eller **insikter**) genereras inbäddningskoden som du kan klistra in i programmet.
 
4. Välj vilken typ av widget du vill använda (**insikter** eller **spelare**).
5. Kopiera inbäddningskoden och lägg till den i programmet. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Bädda in privat innehåll

Du kan endast hämta inbäddningskoder från inbäddningsfönster (som visas i föregående avsnitt) för **offentliga** videor. 

Om du vill bädda in en **privat** video måste du skicka en åtkomsttoken i **iframe**-taggens **src**-attribut:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>
    
Använd API:t för [**Hämta insikter-widgeten**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) för att hämta innehållet i insiktswidgeten, eller använd [**Hämta videoåtkomsttoken**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) och lägg till den som en frågeparameter i URL:en, enligt ovan. Ange den här URL:en som **iframe**-taggens **src**-värde.

Om du vill tillhandahålla funktioner för redigering av insikter (som i vår webbapp) i den inbäddade widgeten måste du skicka en åtkomsttoken med redigeringsbehörigheter. Använd [**Hämta insikter-widgeten**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) eller [**Hämta videoåtkomsttoken**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) med **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interaktion med widgetar

**Insikts**widgeten kan interagera med en video i programmet. Det här avsnittet visar hur du uppnår den här interaktionen.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Kommunikation mellan ursprung

För att få Video Indexer-widgetar att kommunicera med andra komponenter gör Video Indexer-tjänsten följande:

- Använder HTML5-metoden **postMessage** för kommunikation mellan ursprung och 
- Verifierar meddelandet utifrån VideoIndexer.ai-ursprung. 

Om du väljer att implementera egen spelarkod och utföra integreringen med **insikts**widgetar är det ditt ansvar att verifiera ursprunget för meddelandet som kommer från VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Bädda in båda typerna av widgetar i ett program/en blogg (rekommenderas) 

Det här avsnittet visar hur du uppnår interaktion mellan två Video Indexer-widgetar så att spelaren hoppar till relevant ställe när en användare klickar på insiktskontrollen i programmet.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopiera inbäddningskoden för **spelar**widgeten.
2. Kopiera inbäddningskoden för **insikts**widgeten.
3. Lägg till [**medlarfilen**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) för att hantera kommunikationen mellan de två widgetarna:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

När en användare klickar på insiktskontrollen i programmet hoppar spelaren nu till relevant ställe.

Mer information finns i [den här demonstrationen](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in insiktswidgeten och använda Azure Media Player för att spela upp innehållet

Det här avsnittet visar hur du uppnår interaktion mellan en **insikts**widget och en Azure Media Player-instans med hjälp av [AMP-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Lägg till ett Video Indexer-plugin-program för AMP-spelaren.

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

3. Kopiera inbäddningskoden för **insikts**widgeten.

Nu bör du kunna kommunicera med Azure Media Player.

Mer information finns i [den här demonstrationen](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Bädda in Video Indexer-insiktswidgeten och använda en egen spelare (vilken spelare som helst)

Om du använder en egen spelare måste du hantera spelaren själv för att uppnå kommunikationen. 

1. Infoga videospelaren.

    Till exempel en HTML5-standardspelare

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bädda in insiktswidgeten.
3. Implementera kommunikation för spelaren genom att lyssna på ”message”-händelsen. Exempel:

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


Mer information finns i [den här demonstrationen](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Lägga till undertexter

Om du bäddar in Video Indexer-insikter med en egen AMP-spelare kan du använda metoden **GetVttUrl** för att hämta undertexter. Du kan också anropa en javascript-metod från Video Indexer AMP-plugin-programmet **getSubtitlesUrl** (enligt ovan). 

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbara widgetar

### <a name="cognitive-insights-widget"></a>Insiktswidget
Du kan välja vilka typer av insikter du vill få genom att ange dem som ett värde till följande URL-parameter som läggs till i inbäddningskoden du får (från API:t eller från webbappen):

**&widgets=** \<lista över önskade widgetar>

Möjliga värden är: people, keywords, sentiments, transcript, search.

Om du till exempel vill bädda in en widget som endast innehåller person- och sökningsinsikter ser iframe-inbäddnings-URL:en ut så här: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search

Titeln på iframe-fönstret kan också anpassas genom att ange **&title=**<YourTitle> i iframe-URL:en. (Det anpassar HTML-värdet \<title>).
Om du till exempel vill ge iframe-fönstret titeln ”MyInsights” ser URL:en ut så här: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights. Observera att det här alternativet endast är relevant i fall när du behöver öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Spelarwidget
Om du bäddar in Video Indexer-spelaren kan du välja storlek för spelaren genom att ange storlek på iframe-fönstret.

Exempel:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />

Som standard har Video Indexer-spelaren automatiskt genererade undertexter baserat på avskriften för videon som har extraherats från videon med det källspråk som valdes när videon laddades upp.

Om du vill bädda in med ett annat språk kan du lägga till **&captions=< Language | ”all” | “false” >** i den inbäddade spelarens URL eller ange ”all” som värde om du vill använda textning för alla tillgängliga språk.
Om du vill att textningen ska visas som standard kan du skicka **&showCaptions=true**

Inbäddnings-URL:en ser då ut så här: https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian. Om du vill inaktivera textning kan du skicka ”false” som värde för parametern captions.

Auto play – spelaren börjar spela upp videon som standard. Du kan välja bort det genom att skicka &autoplay=false till inbäddnings-URL:en ovan.

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar Video Indexer-insikter finns i [den här artikeln](video-indexer-view-edit.md).

Ta även en titt på [CodePen för Video Indexer](https://codepen.io/videoindexer/pen/eGxebZ).
