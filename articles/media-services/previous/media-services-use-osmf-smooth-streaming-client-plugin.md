---
title: Smooth Streaming Plugin för Open Source Media Framework
description: Lär dig hur du använder plugin:et för jämn direktuppspelning av Azure Media Services för Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 45a2829411ea4713df898c90be73792718160cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255115"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Så här använder du Microsoft Smooth Streaming Plugin för Adobe Open Source Media Framework  
## <a name="overview"></a>Översikt
Microsoft Smooth Streaming plugin för Open Source Media Framework 2.0 (SS för OSMF) utökar standardfunktionerna i OSMF och lägger till Microsoft Smooth Streaming-innehållsuppspelning till nya och befintliga OSMF-spelare. Insticksprogrammet lägger också till Smooth Streaming-uppspelningsfunktioner till Strobe Media Playback (SMP).

SS för OSMF innehåller två versioner av plugin:

* Statisk Smooth Streaming plugin för OSMF (.swc)
* Dynamisk smooth streaming plugin för OSMF (.swf)

Detta dokument förutsätter att läsaren har en allmän arbetskunskap om OSMF och OSMF plug-ins. Mer information om OSMF finns i dokumentationen på den [officiella OSMF-webbplatsen](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming plugin för OSMF 2,0
Insticksprogrammet stöder inläsning och uppspelning av on-demand Smooth Streaming innehåll med följande funktioner:

* Uppspelning på begäran Jämn strömning (Spela upp, Pausa, Söka, Stoppa)
* Live Smooth Streaming uppspelning (Spela upp)
* Live DVR-funktioner (Paus, Sök, DVR-uppspelning, Go-to-Live)
* Stöd för video codec - H.264
* Stöd för ljud codec - AAC
* Flera ljudspråksväxlingar med INBYGGDA OSMF-API:er
* Maximalt val av uppspelningskvalitet med OSMF-inbyggda API:er
* Sidovagns undertexter med OSMF-bildtexter plugin
* Adobe&reg; &reg; Flash Player 11.4 eller senare.
* Den här versionen stöder endast OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funktioner som stöds och kända problem
En fullständig lista över funktioner som stöds, funktioner som inte stöds och kända problem finns i [det här dokumentet](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Laddar plugin-programmet
OSMF plugins kan laddas statiskt (vid kompilering tid) eller dynamiskt (vid körning). Smooth Streaming plugin för OSMF nedladdning innehåller både dynamiska och statiska versioner.

* Statisk inläsning: För att läsa in statiskt krävs en statisk biblioteksfil (SWC). Statiska plugins läggs till som en referens till projekten och sammanfogas inuti den slutliga utdatafilen vid kompileringstiden.
* Dynamisk inläsning: För att läsa in dynamiskt krävs en förkompilerad (SWF) fil. Dynamiska insticksprogram läses in i körningen och ingår inte i projektutdata. (Kompilerad utgång) Dynamiska insticksprogram kan läsas in med HJÄLP AV HTTP- och FILE-protokoll.

Mer information om statisk och dynamisk inläsning finns på den officiella [OSMF-plugin-sidan](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS för OSMF statisk inläsning
Kodavsnittet nedan visar hur du laddar SS-insticksprogrammet för OSMF statiskt och spelar upp en grundläggande video med klassen OSMF MediaFactory. Innan du inkluderar SS för OSMF-kod, se till att projektreferensen innehåller statiska insticksprogrammet "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS för OSMF dynamisk inläsning
Kodavsnittet nedan visar hur du laddar SS-insticksprogrammet för OSMF dynamiskt och spelar upp en grundläggande video med klassen OSMF MediaFactory. Innan du inkluderar SS för OSMF-kod, kopiera "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" dynamisk plugin till projektmappen om du vill läsa in med filprotokollet, eller kopiera under en webbserver för HTTP-belastning. Det finns ingen anledning att inkludera "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" i projektreferenserna.

paketet {

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media Uppspelning med SS ODMF Dynamic Plugin
Smooth Streaming för OSMF dynamisk plugin är kompatibel med [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Du kan använda SS för OSMF-plugin för att lägga till uppspelning av jämnt strömmande innehåll till SMP. Det gör du genom att kopiera "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" under en webbserver för HTTP-inläsning med hjälp av följande steg:

1. Bläddra i [inställningssidan för Strobe Media Playback](http://osmf.org/dev/2.0gm/setup.html). 
2. Ställ in src till en smooth streaming-källa (t.ex.http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Gör önskade konfigurationsändringar och klicka på Förhandsgranska och uppdatera.
   
   **Anm.)** Webbservern för innehåll behöver en giltig crossdomain.xml. 
4. Kopiera och klistra in koden på en enkel HTML-sida med hjälp av din favorittextredigerare, till exempel i följande exempel:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Lägg smooth streaming OSMF plugin till inbäddningskoden och spara.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Spara HTML-sidan och publicera på en webbserver. Bläddra till den publicerade webbsidan&reg; med hjälp av din favorit Flash Player-aktiverad webbläsare (Internet Explorer, Chrome, Firefox, så vidare).
3. Njut av jämnt&reg; strömmande innehåll i Adobe Flash&reg; Player.

För mer information om allmän OSMF utveckling, se den officiella [OSMF utvecklingssida](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Microsoft Adaptive Streaming Plugin för OSMF Update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

