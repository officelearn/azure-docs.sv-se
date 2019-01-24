---
title: Komma igång med Azure Media Clipper | Microsoft Docs
description: Komma igång med Azure Media Clipper, ett verktyg för att skapa videoklipp från AMS-tillgångar
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 27a330fa4d4f242a58d15ab3f08b70cef8b66d11
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810745"
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper
Det här avsnittet visas de grundläggande stegen för att komma igång med Azure Media Clipper. Avsnitten som följer ger information om hur du konfigurerar Azure Media Clipper.

- Lägg först till följande länkar för Azure Media Player och Azure Media Clipper dokumentets väljer du. Vi rekommenderar att uttryckligen ange en version av Clipper och Azure Media Player i URL: er. Använd inte den senaste versionen av dessa resurser i produktionen, eftersom de kan komma att ändras på begäran.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Lägg sedan till följande klasser där du vill skapa en instans av Clipper div-elementet.

```javascript
<div id="root" class="azure-subclipper" />
```

Lägg till mörk skal-klassen för att aktivera det mörka temat:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Därefter skapa en instans av Clipper med följande API-anrop:

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
- `selector` {KRÄVS, sträng}: CSS-väljare för matchande HTML-element där widgeten ska återges.
- `restVersion` {KRÄVS, sträng}: Azure Media Services REST API-version till målet. REST-versionen definierar formatet på de utdata som genereras i widgeten. För närvarande stöds endast 2.0.
- `submitSubclipCallback` {KRÄVS, löftet} Den återanropsfunktion som anropas när du klickar på knappen ”Skicka” i widgeten. Återanropsfunktionen förvänta utdata genereras av widgeten (en konfiguration för rendering jobb eller en filterdefinitionen). Mer information finns i Skicka underklipp återanrop.
- `logLevel` {VALFRITT, {'information', 'Varna', 'error'}}: Loggningsnivån som ska visas i webbläsarens konsol. Standardvärde: fel
- `minimumMarkerGap` {VALFRITT, int}: Den minsta storleken på en underklipp (i sekunder). Obs: värdet ska vara större eller lika med 6, vilket är standard.
- `singleBitrateMp4Profile` {VALFRITT JSON-objekt} Enkel bithastighet mp4 profil som används för rendering jobbkonfigurationen genereras i widgeten. Om inte anges så används den [med enkel bithastighet MP4 standardprofil](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {VALFRITT JSON-objekt} Med flera bithastigheter mp4-profil som används för att återge jobbkonfigurationen som genereras i widgeten. Om inte anges så används den [med flera bithastigheter MP4 standardprofil](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {VALFRITT json-objekt} Tillåter anpassning kortkommandon i widgeten. Mer information finns i [anpassningsbara kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {VALFRITT löftet} Den återanropsfunktion som anropas om du vill läsa in (asynkrona) en ny sida med tillgångar i fönstret tillgångar varje gång användaren rullar längst ned i fönstret. Mer information finns i tillgången fönstret inläsaren återanrop.
- `height` {VALFRITT, number} Widgeten totala höjd (minsta höjd är 600 bildpunkter utan tillgångar fönstret och 850 px med fönstret tillgångar).
- `subclippingMode` (Valfritt, {”alla”, ”återge”, ”filtrera”}): Underklippning-läge(n) tillåts. Standardvärdet är alla.
- `filterAssetsTypes` (Valfritt, bool): filterAssetsTypes kan du Visa/Dölj filter listrutan från fönstret tillgångar. Standardvärdet är sant.
- `speedLevels` (Valfritt, matris): speedLevels kan du ange olika hastighet nivåer för videospelaren, se [dokumentation för Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) för mer information.
- `resetOnJobDone` (Valfritt, bool): resetOnJobDone tillåter Clipper att återställa subclipper till ett inledande tillstånd när ett jobb har skickats.
- `autoplayVideo` (Valfritt, bool): autoplayVideo tillåter Clipper att spela upp videon på belastning. Standardvärdet är sant.
- `language` {VALFRI, sträng}: språk anger språket i widgeten. Om inte anges försöker widgeten lokalisera meddelanden baserat på webbläsarens språk. Om inget språk har identifierats i webbläsaren, widgeten som standard till engelska. Mer information finns i den [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnittet.
- `languages` {VALFRITT, JSON}: parametern språk ersätter standardordlista med språk med en ordlista som definierats av användaren. Mer information finns i den [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnittet.
- `extraLanguages` (Valfritt, JSON): parametern extraLanguages lägger till nya språk i standardordlista. Mer information finns i den [konfigurera lokalisering](media-services-azure-media-clipper-localization.md) avsnittet.

## <a name="typescript-definition"></a>TypeScript-definition
En [TypeScript](https://www.typescriptlang.org/) definitionsfilen för Clipper finns [här](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
Det här avsnittet beskrivs de API-yta som tillhandahålls av Clipper.

- `ready(handler)`: ger ett sätt att köra JavaScript så fort Clipper är fullständigt inläst och klar att användas.
- `load(assets)`: läser in en lista över tillgångar i widgeten tidslinjen (inte ska användas tillsammans med assetsPanelLoaderCallback). Se den här [artikeln](media-services-azure-media-clipper-load-assets.md) mer information om hur du läser in tillgångar i Clipper.
- `setLogLevel(level)`: Anger loggningsnivån som ska visas i webbläsarens konsol. Möjliga värden är: `info`, `warn`, `error`.
- `setHeight(height)`: Anger widgeten totala höjd i bildpunkter (minsta höjd är 600 bildpunkter utan tillgångar fönstret och 850 px med fönstret tillgångar).
- `version`: hämtar widget-version.

## <a name="next-steps"></a>Nästa steg
Se nästa steg för att konfigurera Azure Media Clipper:
- [Läsa in tillgångar i Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurera anpassade kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Skicka urklippningsjobb från Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md)