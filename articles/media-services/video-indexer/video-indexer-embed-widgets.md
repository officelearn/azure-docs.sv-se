---
title: Bädda in Video Indexer widgetar i dina program
titlesuffix: Azure Media Services
description: Lär dig hur du bäddar in Video Indexer widgetar i ditt program.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: fc0b447630b5e1ac360b1d84869cea02186672fc
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036631"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Bädda in Video Indexer widgetar i dina program

Den här artikeln visar hur du kan bädda in Video Indexer widgetar i dina program. Video Indexer stöder inbäddning av tre typer av widgetar i ditt program: *Kognitiva insikter*, *spelare*och *redigerare*. 

Från och med version 2 innehåller widgeten bas-URL den region där det angivna kontot finns. Ett konto i regionen Västra USA genererar till exempel: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Widgettyper

### <a name="cognitive-insights-widget"></a>Insiktswidget

En kognitiv Insights-widget innehåller alla visuella insikter som har extraherats från video indexerings processen. Widgeten kognitiv Insights stöder följande valfria URL-parametrar.

|Name|Definition|Beskrivning|
|---|---|---|
|`widgets`|Strängar avgränsade med kommatecken|Gör att du kan styra de insikter som du vill återge. <br/> Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` återger endast personer och varumärke UI Insights.<br/>Tillgängliga alternativ: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Observera att `widgets` URL-parametern inte stöds i version 2.<br/>|
|`locale`|En kort språkkod|Styr insikter-språket. Standardvärdet är `en`. <br/> Exempel: `locale=de`.|
|`tab`|Den valda fliken standard|Styr fliken **insikter** som återges som standard. <br/> Exempel: `tab=timeline` återger insikter med fliken **tids linje** vald.|

### <a name="player-widget"></a>Spelarwidget

Du kan använda Player-widgeten för att strömma video med hjälp av anpassningsbar bit hastighet. Widgeten Player stöder följande valfria URL-parametrar.

|Name|Definition|Beskrivning|
|---|---|---|
|`t`|Sekunder från start|Gör att spelaren startar uppspelningen från den angivna tids punkten.<br/> Exempel: `t=60`.|
|`captions`|En språkkod|Hämtar texten på det angivna språket när widget inläsningen ska vara tillgänglig på menyn under **texter** .<br/> Exempel: `captions=en-US`.|
|`showCaptions`|Ett booleskt värde|Gör att spelaren läses in med aktiverad textning.<br/> Exempel: `showCaptions=true`.|
|`type`||Aktiverar ett ljuds pelar skal (video delen tas bort).<br/> Exempel: `type=audio`.|
|`autoplay`|Ett booleskt värde|Anger om spelaren ska börja spela upp videon vid inläsning. Standardvärdet är `true`.<br/> Exempel: `autoplay=false`.|
|`language`|En språkkod|Kontrollerar Player-språket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`.|

### <a name="editor-widget"></a>Editor-widget

Du kan använda Editor-widgeten för att skapa nya projekt och hantera video insikter. Editor-widgeten stöder följande valfria URL-parametrar.

|Name|Definition|Beskrivning|
|---|---|---|
|`accessToken`<sup>*</sup>|Sträng|Ger åtkomst till videor som bara finns i det konto som används för att bädda in widgeten.<br> Widgeten Editor kräver `accessToken` parametern.|
|`language`|En språkkod|Kontrollerar Player-språket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`.|
|`locale`|En kort språkkod|Styr insikter-språket. Standardvärdet är `en`.<br/>Exempel: `language=de`.|

<sup>*</sup>Ägaren bör ge `accessToken` en varning.

## <a name="embedding-public-content"></a>Bädda in offentligt innehåll

1. Logga in på [video Indexer](https://www.videoindexer.ai/) webbplats.
2. Välj den video som du vill arbeta med.
3. Välj knappen **bädda in** som visas under videon.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    När du har valt knappen **bädda** in kan du välja den widget som du vill bädda in i programmet. 
4. Välj vilken typ av widget du vill ha (**kognitiva insikter**, **spelare**eller **redigerare**).
 
5. Kopiera inbäddnings koden och Lägg sedan till den i programmet. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Om du har problem med att dela dina video-URL `location` : er lägger du till parametern till länken. Parametern ska vara inställd på de [Azure-regioner där video Indexer finns](regions.md). Till exempel: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Bädda in privat innehåll

Om du vill bädda in en privat video måste du skicka en åtkomsttoken i attributet **src** i iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Använd något av följande för att hämta innehåll för kognitiva Insights-widgeten:<br/>
- API för [Get Insights-widgeten](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Hämta video åtkomst-token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Lägg till den som en frågeparameter till URL: en. Ange den här URL: en som **src** -värde för iframe, som visas ovan.

Om du vill ge redigerings funktioner för insikter i din inbäddade widget måste du skicka en åtkomsttoken som innehåller redigerings behörigheter. Använd [Get Insights-widgeten](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) eller [Hämta video åtkomst-token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) med `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interaktion med widgetar

Widgeten kognitiv Insights kan interagera med en video i ditt program. Det här avsnittet visar hur du uppnår den här interaktionen.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Kommunikation mellan ursprung

För att få Video Indexer widgetar för att kommunicera med andra komponenter, Video Indexer-tjänsten:

- Använder metoden för kommunikation med cross-origin-metoden **postMessage**. 
- Verifierar meddelandet utifrån VideoIndexer.ai-ursprung. 

Om du implementerar din egen Player-kod och integrerar med kognitiv Insights-widget är det ditt ansvar att verifiera ursprunget för meddelandet som kommer från VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Bädda in widgetar i programmet eller bloggen (rekommenderas) 

I det här avsnittet visas hur du uppnår interaktionen mellan två Video Indexer-widgetar, så att spelaren hoppar till relevant tidpunkt när en användare väljer kontrollen Insight i programmet.

1. Kopiera inbäddnings koden för widgeten för Player.
2. Kopiera inbäddnings koden för kognitiv Insights.
3. Lägg till [Mediator-filen](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) för att hantera kommunikationen mellan de två widgetarna:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Nu när en användare väljer kontrollen Insight i programmet, hoppar spelaren till relevant stund.

Mer information finns i [demonstrationen av video Indexer-bädda in båda widgetarna](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in insiktswidgeten och använda Azure Media Player för att spela upp innehållet

I det här avsnittet visas hur du uppnår interaktionen mellan en kognitiv Insights-widget och en Azure Media Player-instans med hjälp av [amp-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Lägg till ett Video Indexer-plugin-program för AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instansiera Azure Media Player med plugin-programmet Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
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

        // Init your AMP instance.
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
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Kopiera inbäddnings koden för kognitiv Insights.

Nu bör du kunna kommunicera med Azure Media Player.

Mer information finns i [demonstrationen Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Bädda in widgeten Video Indexer kognitiva insikter och Använd en annan Videos pelare

Om du använder en annan Videos pelare än Azure Media Player måste du manuellt ändra Videos pelaren för att uppnå kommunikationen. 

1. Infoga videospelaren.

    Till exempel en standard HTML5-spelare:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bädda in insiktswidgeten.
3. Implementera kommunikation för spelaren genom att lyssna på ”message”-händelsen. Exempel:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Mer information finns i [demonstrationen Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Lägga till undertexter

Om du bäddar in Video Indexer insikter med dina egna [Azure Media Player](https://aka.ms/azuremediaplayer)kan du använda **GetVttUrl** -metoden för att hämta textning (under texter). Du kan också anropa en JavaScript-metod från Video Indexer AMP plugin- **getSubtitlesUrl** (se tidigare). 

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbara widgetar

### <a name="cognitive-insights-widget"></a>Insiktswidget

Du kan välja vilka typer av insikter du vill. Det gör du genom att ange dem som ett värde för följande URL-parameter som läggs till i den inbäddnings kod som du får (från API: et eller från webb programmet `&widgets=<list of wanted widgets>`):.

Möjliga värden är: **personer**, **nyckelord**, **sentiment**, **avskrift**och **sökning**.

Om du till exempel vill bädda in en widget som bara innehåller personer och Sök insikter så ser iframe-inbäddningen ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Rubriken för iframe-fönstret kan också anpassas genom att tillhandahålla `&title=<YourTitle>` iframe-URL: en. (Den anpassar HTML- \<rubriken > värde).
    
Om du till exempel vill ge iframe-fönstret rubriken "mina insikter" ser URL: en ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observera att det här alternativet endast är relevant i fall när du behöver öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Spelarwidget

Om du bäddar in Video Indexer-spelaren kan du välja storlek för spelaren genom att ange storlek på iframe-fönstret.

Exempel:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Som standard innehåller Video Indexer Player automatiskt skapade stängda under texter som baseras på avskriften av videon. Avskriften extraheras från videon med det käll språk som valdes när videon laddades upp.

Om du vill bädda in med ett annat språk kan du lägga till `&captions=< Language | ”all” | “false” >` i URL: en för inbäddning av Player. Om du vill ha beskrivningar i alla tillgängliga språk rubriker använder du värdet `all`. Om du vill att rubrikerna ska visas som standard kan du skicka `&showCaptions=true`.

Inbäddnings-URL: en kommer sedan att se ut så här: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Om du vill inaktivera under `captions` texter kan du skicka parametervärdet som. `false`

#### <a name="autoplay"></a>Spela
Som standard startar spelaren uppspelning av videon. Du kan välja att inte skicka `&autoplay=false` till den tidigare inbäddnings-URL: en.

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar Video Indexer insikter finns i [Visa och redigera video Indexer insikter](video-indexer-view-edit.md).

Ta också en titt på [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
