---
title: "Komma igång med Azure Media Clipper | Microsoft Docs"
description: "Komma igång med Azure Media Clipper, ett verktyg för att skapa videoklipp från AMS-tillgångar"
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper
Det här avsnittet visas de grundläggande stegen för att komma igång med Azure Media Clipper. De följande avsnitten finns information om hur du konfigurerar Azure Media Clipper.

- Lägg först till följande länkar för Azure Media Player och Azure Media Clipper till dokumentets head. Vi rekommenderar att explicit ange en version av Clipper och Azure Media Player i URL: er. Använd inte den senaste versionen av resurserna i produktion, eftersom de kan komma att ändras på begäran.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Lägg sedan till följande klasser i elementet div där du vill skapa en instans av Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Om du vill aktivera det mörka temat Lägg eventuellt mörkt skal-klassen:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Därefter instansiera Clipper med följande API-anrop:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Parametrarna för metodanropet initieringen är:
- `selector`{KRÄVS, string}: CSS-väljare av matchande HTML-element där widgeten ska renderas.
- `restVersion`{KRÄVS, string}: Azure Media Services REST API-version till målet. REST-versionen definierar formatet för utdata som genererats av widgeten. För närvarande stöds endast 2.0.
- `submitSubclipCallback`{KRÄVS, promise} Återanropsfunktionen anropas när användaren klickar på knappen ”Skicka” för validering. Återanropsfunktionen förvänta utdata som genererats av widgeten (en konfiguration för render jobb eller en filterdefinitionen). Mer information finns i Skicka underklipp återanrop.
- `logLevel`{VALFRITT, {”info', 'Varna', 'fel'}}: loggningsnivån som ska visas i webbläsarens konsol. Standardvärde: fel
- `minimumMarkerGap`{VALFRITT, int}: den minimala storleken för ett underklipp (i sekunder). Obs: värdet ska vara större eller lika med 6, vilket är standard.
- `singleBitrateMp4Profile`{VALFRITT JSON-objekt} Enkel bithastighet mp4 profil som används för render jobbkonfigurationen som genererats av widgeten. Om inte anges, används den [enkel bithastighet MP4 standardprofil](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile`{VALFRITT JSON-objekt} Flera bithastighet mp4-profil som används för att återge jobbkonfiguration som genererats av widgeten. Om inte anges, används den [multibithastighet MP4 standardprofil](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap`{VALFRITT json-objekt} Kan du anpassa kortkommandon för validering. Mer information finns i [anpassningsbara kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback`{VALFRITT promise} Återanropsfunktionen anropas för att läsa in (asynkront) en ny sida av tillgångar i fönstret tillgångar varje gång användaren rullar längst ned i fönstret. Mer information finns i tillgången fönstret inläsaren återanrop.
- `height`{VALFRITT, number} Totalt antal höjden på widgeten (minimihöjden är 600 px utan tillgångar rutan och 850 px med fönstret tillgångar).
- `subclippingMode`(Valfritt, {'all', ' återge', 'filtrera'}): subclipping-läge(n) tillåts. Standardvärdet är alla.
- `filterAssetsTypes`(Valfritt, bool): filterAssetsTypes kan du Visa/Dölj filter dropdown från fönstret tillgångar. Standardvärdet är true.
- `speedLevels`(Valfritt, array): speedLevels kan ange olika hastighet nivåer för videospelaren, se [dokumentation för Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) för mer information.
- `resetOnJobDone`(Valfritt, bool): resetOnJobDone tillåter Clipper återställs subclipper till ett initialt tillstånd när ett jobb har skickats.
- `autoplayVideo`(Valfritt, bool): autoplayVideo kan Clipper spela upp videon på belastningen. Standardvärdet är true.
- `language`{VALFRI sträng}: språk ställer in språket för validering. Om den inte anges försöker widgeten localize meddelanden utifrån språkinställningar för webbläsaren. Om inget språk har identifierats i webbläsaren, standard widgeten engelska. Mer information finns i [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnitt.
- `languages`{VALFRITT, JSON}: parametern språk ersätter standardordlistan språk med en ordlista som definierats av användaren. Mer information finns i [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnitt.
- `extraLanguages`(Valfritt, JSON): parametern extraLanaguages lägger till nya språk i standardordlista. Mer information finns i [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnitt.

## <a name="typescript-definition"></a>Maskin-definition
En [maskin](https://www.typescriptlang.org/) definitionsfilen för Clipper hittar [här](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
Det här avsnittet beskrivs API-ytan som tillhandahålls av Clipper.

- `ready(handler)`: ger ett sätt att köra JavaScript så snart Clipper har lästs in och är redo att användas.
- `load(assets)`: läser in en lista över tillgångar i en widget tidslinje (inte ska användas tillsammans med assetsPanelLoaderCallback). Se den här [artikeln](media-services-azure-media-clipper-load-assets.md) information om hur du läser in tillgångar i Clipper.
- `setLogLevel(level)`: Anger loggningsnivån som ska visas i webbläsarens konsol. Möjliga värden är: `info`, `warn`, `error`.
- `setHeight(height)`: Anger widgeten totala höjd i bildpunkter (minimihöjden är 600 bildpunkter utan tillgångar rutan och 850 px med fönstret tillgångar).
- `version`: hämtar widget-version.

## <a name="next-steps"></a>Nästa steg
Se nästa steg för att konfigurera Azure Media Clipper:
- [Läsa in tillgångar i Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurera anpassade kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Skickar urklippet jobb från Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md)