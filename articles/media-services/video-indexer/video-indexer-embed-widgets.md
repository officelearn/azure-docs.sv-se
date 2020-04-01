---
title: Bädda in videoindexeringswidgetar i dina appar
titleSuffix: Azure Media Services
description: Läs om hur du bäddar in videoindexeringswidgetar i dina appar.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 6ed5c509cf310b743e4ef52f411dfa34e5db09c1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411610"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Bädda in videoindexeringswidgetar i dina appar

Den här artikeln visar hur du kan bädda in videoindexeringswidgetar i dina appar. Video Indexer stöder inbäddning av tre typer av widgetar i dina appar: *Cognitive Insights*, *Player*och *Editor*.

Från och med version 2 innehåller widgetbasadressen regionen för det angivna kontot. Ett konto i regionen Västra USA genererar `https://wus2.videoindexer.ai/embed/insights/...`till exempel: .

## <a name="widget-types"></a>Widgettyper

### <a name="cognitive-insights-widget"></a>Insiktswidget

En insiktswidget innehåller alla visuella insikter som extraherades från videoindexeringsprocessen. Widgeten Cognitive Insights stöder följande valfria URL-parametrar:

|Namn|Definition|Beskrivning|
|---|---|---|
|`widgets` | Strängar avgränsade med kommatecken | Gör att du kan styra de insikter som du vill återge.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` återger endast personer och nyckelord användargränssnittsstatistik.<br/>Tillgängliga alternativ: personer, animeradeegenskaper , nyckelord, etiketter, känslor, känslor, ämnen, nyckelrutor, avskrift, ocr, högtalare, scener och namngivna Entiteter.|
|`controls`|Strängar avgränsade med kommatecken|Gör att du kan styra de kontroller som du vill återge.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` återger bara sökalternativet och nedladdningsknappen.<br/>Tillgängliga alternativ: sök, ladda ner, förinställningar, språk.|
|`language`|En kort språkkod (språknamn)|Styr statistikspråket.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Eller`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | En kort språkkod | Styr språket i användargränssnittet. Standardvärdet är `en`. <br/>Exempel: `locale=de`.|
|`tab` | Den markerade standardfliken | Styr fliken **Insikter** som återges som standard. <br/>Exempel: `tab=timeline` återger insikterna med fliken **Tidslinje** markerad.|

### <a name="player-widget"></a>Spelarwidget

Du kan använda widgeten Player för att strömma video med hjälp av adaptiv bithastighet. Widgeten Player stöder följande valfria URL-parametrar.

|Namn|Definition|Beskrivning|
|---|---|---|
|`t` | Sekunder från början | Gör att spelaren börjar spela från den angivna tidpunkten.<br/> Exempel: `t=60`. |
|`captions` | En språkkod | Hämtar bildtexten på det angivna språket under den inläsning av widgeten som ska vara tillgänglig på **menyn Bildtexter.**<br/> Exempel: `captions=en-US`. |
|`showCaptions` | Ett booleskt värde | Gör att spelaren läses in med aktiverad textning.<br/> Exempel: `showCaptions=true`. |
|`type`| | Aktiverar ett ljudspelarskal (videodelen tas bort).<br/> Exempel: `type=audio`. |
|`autoplay` | Ett booleskt värde | Anger om spelaren ska börja spela upp videon när den läses in. Standardvärdet är `true`.<br/> Exempel: `autoplay=false`. |
|`language`/`locale` | En språkkod | Styr spelarens språk. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`.|

### <a name="editor-widget"></a>Widgeten Redigerare

Du kan använda widgeten Redigerare för att skapa nya projekt och hantera en videos insikter. Widgeten Redigerare stöder följande valfria URL-parametrar.

|Namn|Definition|Beskrivning|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Ger åtkomst till videor som bara finns i det konto som används för att bädda in widgeten.<br> Widgeten Redigerare `accessToken` kräver parametern. |
|`language` | En språkkod | Styr spelarens språk. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`. |
|`locale` | En kort språkkod | Styr statistikspråket. Standardvärdet är `en`.<br/>Exempel: `language=de`. |

<sup>*</sup>Ägaren bör `accessToken` vara försiktig.

## <a name="embedding-public-content"></a>Bädda in offentligt innehåll

1. Logga in på [videoindexerarens](https://www.videoindexer.ai/) webbplats.
2. Välj den video som du vill arbeta med.
3. Välj den inbäddningsknapp (**</>**) som visas under videon.

    När du har valt knappen **Bädda in** kan du välja den widget som du vill bädda in i appen.
4. Välj den typ av widget som du vill använda (**Kognitiva insikter**, **Spelare**eller **Redigerare**).
5. Kopiera inbäddningskoden (visas i **Kopiera den inbäddade koden** i dialogrutan Dela & Bädda **in).**
6. Lägg till koden i appen.

> [!NOTE]
> Om du har problem med att `location` dela dina videoadresser lägger du till parametern i länken. Parametern ska anges till [Azure-regioner där Video Indexer finns](regions.md). Till exempel: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Bädda in privat innehåll

Om du vill bädda in en privat `src` video måste du skicka en åtkomsttoken i attributet för iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Använd någon av följande metoder för att hämta widgeten Cognitive Insights:

- [Api:et för widgeten Hämta statistik.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- [Token för hämta videoåtkomst](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Lägg till den som frågeparameter i URL:en. Ange den här `src` URL:en som värde för iframe, som tidigare visats.

Om du vill tillhandahålla redigeringsstatistikfunktioner i den inbäddade widgeten måste du skicka en åtkomsttoken som innehåller redigeringsbehörigheter. Använd [Widgeten Hämta statistik](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) eller hämta [videoåtkomsttoken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) med `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interaktion med widgetar

Widgeten Cognitive Insights kan interagera med en video i din app. Det här avsnittet visar hur du uppnår den här interaktionen.

![Widget videoindexeraren videoindexerare för kognitiva insikter](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Kommunikation mellan ursprung

Så här hämtar du videoindexeringswidgetar för att kommunicera med andra komponenter:

- Använder html5-metoden `postMessage`för kommunikation med flera ursprung .
- Verifierar meddelandet utifrån VideoIndexer.ai-ursprung.

Om du implementerar din egen spelarkod och integrerar med widgetar för Cognitive Insights är det ditt ansvar att validera ursprunget för meddelandet som kommer från VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Bädda in widgetar i appen eller bloggen (rekommenderas)

Det här avsnittet visar hur du uppnår interaktion mellan två videoindexeringswidgetar så att när en användare väljer insiktskontrollen i din app hoppar spelaren till det relevanta ögonblicket.

1. Kopiera inbäddningskoden för spelarwidgeten.
2. Kopiera inbäddningskoden för insiktswidgeten.
3. Lägg till [medlarfilen](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) för att hantera kommunikationen mellan de två widgetarna:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Nu när en användare väljer insiktskontroll på din app hoppar spelaren till det relevanta ögonblicket.

Mer information finns i [demoen Video Indexer - Embed both Widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in insiktswidgeten och använda Azure Media Player för att spela upp innehållet

Det här avsnittet visar hur du uppnår interaktion mellan widgeten Cognitive Insights och en Azure Media Player-instans med hjälp av [AMP-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Lägg till ett plugin-program för videoindexering för AMP-spelaren:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
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
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Kopiera inbäddningskoden för insiktswidgeten.

Du kan nu kommunicera med Azure Media Player.

Mer information finns i [demoen Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Bädda in widgeten Video Indexer Cognitive Insights och använd en annan videospelare

Om du använder en annan videospelare än Azure Media Player måste du manuellt manipulera videospelaren för att uppnå kommunikationen.

1. Infoga videospelaren.

    Till exempel en vanlig HTML5-spelare:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bädda in insiktswidgeten.
3. Implementera kommunikation för spelaren genom att lyssna på ”message”-händelsen. Ett exempel:

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

Mer information finns i [demoen Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Lägga till undertexter

Om du bäddar in videoindexeringsstatistik med din `GetVttUrl` egen Azure Media [Player](https://aka.ms/azuremediaplayer)kan du använda metoden för att få dold textning (undertexter). Du kan också anropa en JavaScript-metod från `getSubtitlesUrl` plugin-programmet Video Indexer AMP (som tidigare).

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbara widgetar

### <a name="cognitive-insights-widget"></a>Insiktswidget

Du kan välja vilka typer av insikter du vill ha. Det gör du genom att ange dem som ett värde i följande URL-parameter som läggs till i `&widgets=<list of wanted widgets>`inbäddningskoden som du får (från API:et eller från webbappen): .

De möjliga värdena `animatedCharacters` `keywords`är: `sentiments` `people`, `keyframes`, `transcript` `ocr`, `speakers` `labels` `scenes`, `emotions`, `topics`, , , , , , och `namedEntities`.

Om du till exempel vill bädda in en widget som bara innehåller personer och sökordsinsikter, ser iframe-inbäddningsadressen ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Titeln på iframe-fönstret kan också `&title=<YourTitle>` anpassas genom att tillhandahålla iframe-URL:en. (HTML-värdet `<title>` anpassas).
   
Om du till exempel vill ge iframe-fönstret titeln "MyInsights" kommer webbadressen att se ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observera att det här alternativet endast är relevant i fall när du behöver öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Spelarwidget

Om du bäddar in Video Indexer-spelaren kan du välja storlek för spelaren genom att ange storlek på iframe-fönstret.

Ett exempel:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Som standard har Video Indexer-spelaren automatiskt skapade dold textningar som baseras på utskriften av videon. Avskriften extraheras från videon med källspråket som valdes när videon laddades upp.

Om du vill bädda in med ett `&captions=<Language Code>` annat språk kan du lägga till webbadressen till inbäddningsspelaren. Om du vill att bildtexterna ska visas som standard kan du skicka &showCaptions=true.

Bädda in URL då kommer att se ut så här:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Autoplay

Som standard börjar spelaren spela upp videon. du kan välja att `&autoplay=false` inte gå vidare till föregående inbäddningsadress.

## <a name="code-samples"></a>Kodexempel

Se [kodexempel repo](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) som innehåller exempel för Video Indexer API och widgets:

| Fil/mapp                       | Beskrivning                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Ladda videoindexeringsvideo i en anpassad Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Bädda in VI-insikter med en anpassad Azure Media Player.                             |
| `control-vi-embedded-player`      | Bädda in VI Player och kontrollera den utifrån.                                    |
| `custom-index-location`           | Bädda in VI-insikter från en anpassad extern plats (kan vara en blob för kunden).     |
| `embed-both-insights`             | Grundläggande användning av VI Insights både spelare och insikter.                            |
| `embed-insights-with-AMP`         | Bädda in VI Insights-widgeten med en anpassad Azure Media Player.                      |
| `customize-the-widgets`           | Bädda in VI-widgetar med anpassade alternativ.                                     |
| `embed-both-widgets`              | Bädda in VI Player och Insights och kommunicera mellan dem.                      |
| `url-generator`                   | Genererar widgetar anpassad inbäddnings-URL baserat på användarspecificerade alternativ.             |
| `html5-player`                    | Bädda in VI-insikter med en standardvideospelare för HTML5.                           |

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar insikter för videoindexerare finns i Visa och redigera statistik för [videoindexerare](video-indexer-view-edit.md).

Kolla också in [Video indexerare CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
