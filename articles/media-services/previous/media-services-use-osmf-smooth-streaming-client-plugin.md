---
title: Jämn direktuppspelning plugin-programmet för Open Source Media Framework
description: Lär dig hur du använder plugin-programmet Azure Media Services Smooth Streaming för Adobe Open Source Media Framework.
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
ms.openlocfilehash: c40d8b93a7487619cc94586c7e6b4cdc550435cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825599"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Hur du använder Microsoft-Smooth Streaming-plugin-programmet för Adobe Open Source Media Framework  
## <a name="overview"></a>Översikt
Plugin-program för Microsoft Smooth Streaming för Open Source Media Framework 2.0 (SS för OSMF) utökar standard-funktionerna för OSMF och lägger till Microsoft Smooth Streaming innehåll uppspelning nya och befintliga OSMF spelare. Plugin-programmet lägger också till Smooth Streaming-uppspelning kapacitet att Strobe Media Playback (SMP).

SS för OSMF innehåller två versioner av plugin-programmet:

* Statisk Smooth Streaming-plugin för OSMF (.swc)
* Dynamisk Smooth Streaming-plugin för OSMF (.swf)

Det här dokumentet förutsätts att läsaren har allmänna kunskaper om OSMF och OSMF plugin-program. Mer information om OSMF finns i dokumentationen på den [officiella OSMF plats](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming-plugin för OSMF 2.0
Plugin-programmet stöder inläsning och uppspelning av på begäran Smooth Streaming-innehåll med följande funktioner:

* Spela upp Smooth Streaming på begäran (spela, pausa, Sök, Stop)
* Live Smooth Streaming uppspelning (Play)
* Live DVR-funktioner (pausa, Sök, DVR uppspelningen och Go-to-Live)
* Stöd för video-codec - H.264
* Stöd för ljud codec - AAC
* Flera språk växlar med OSMF inbyggda API: er
* Max uppspelning kvalitet val med OSMF inbyggda API: er
* Sidovagnen undertexter med plugin-programmet för OSMF undertexter
* Adobe&reg; Flash&reg; Player 11,4 eller högre.
* Den här versionen stöder endast OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funktioner som stöds och kända problem
En fullständig lista över funktioner som stöds, funktioner som inte stöds och kända problem finns i [det här dokumentet](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Läser in plugin-programmet
OSMF plugin-program kan läsas in statiskt (vid kompilering) eller dynamiskt (vid körning). Plugin-program för Smooth Streaming för OSMF download omfattar både dynamiska och statiska versioner.

* Statisk inläsning: Om du vill läsa in statiskt, krävs ett statiskt bibliotek (SWC)-fil. Statisk plugin-program har lagts till som en referens till projekt och sammanfoga i den slutgiltiga utdatafilen vid kompileringen.
* Dynamisk inläsning: Om du vill läsa in dynamiskt, krävs en förkompilerade (SWF)-fil. Dynamisk plugin-program laddas i runtime och inte ingår i projektet utdata. (Kompilerade utdata) Dynamisk plugin-program kan läsas med hjälp av HTTP- och protokoll.

Mer information om statisk och dynamisk inläsning finns i officiellt [OSMF plugin-programmet sidan](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS för OSMF statiska inläsning
Kodfragmentet nedan visar hur du läser in SS-plugin-programmet för OSMF statiskt och spela upp en grundläggande video med OSMF MediaFactory-klassen. Innan inklusive SS för OSMF kod, se till att projektreferensen innehåller ”MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc” statiska plugin-programmet.

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
Kodfragmentet nedan visar hur du läser in SS-plugin-programmet för OSMF dynamiskt och spela upp en grundläggande video med hjälp av klassen OSMF MediaFactory. Kopiera ”MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf” dynamisk plugin-programmet till projektmappen, om du vill läsa in filen protokollet innan inklusive SS för OSMF kod, eller kopiera under en webbserver för HTTP. Det finns inget behov att inkludera ”MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc” i projektreferenserna.

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe mediauppspelning med SS ODMF dynamisk plugin-programmet
Smooth Streaming för OSMF dynamisk plugin-programmet är kompatibelt med [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Du kan använda SS för OSMF-plugin-programmet för att lägga till Smooth Streaming innehåll uppspelning i SMP. Gör detta genom att kopiera ”MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf” under en webbserver för HTTP med följande steg:

1. Bläddra i [Strobe medieuppspelning installationssidan](http://osmf.org/dev/2.0gm/setup.html). 
2. Ange src till en Smooth Streaming-datakälla (t.ex.) http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Gör önskade ändringar och klicka på förhandsgranskning och uppdatera.
   
   **Obs** webbservern innehåll måste en giltig crossdomain.xml. 
4. Kopiera och klistra in koden för att en enkel HTML-sida med valfri textredigerare, som i följande exempel:

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



1. Lägga till plugin-program för Smooth Streaming OSMF i den inbäddade koden och spara.
   
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
2. Spara HTML-sidan och publicera till en webbserver. Bläddra till den publicerade webbsida med din favorit Flash&reg; Player-aktiverad webbläsare (Internet Explorer, Chrome, Firefox, osv).
3. Dra nytta av Smooth Streaming-innehåll i Adobe&reg; Flash&reg; Player.

Mer information om allmänna OSMF utvecklingen finns på officiellt [OSMF utveckling sidan](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Microsoft anpassningsbar strömning plugin-programmet för OSMF Update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

