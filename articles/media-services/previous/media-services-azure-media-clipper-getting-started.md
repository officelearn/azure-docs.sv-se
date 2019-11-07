---
title: Komma igång med Azure Media Clipper | Microsoft Docs
description: Komma igång med Azure Media Clipper, ett verktyg för att skapa video klipp från AMS-tillgångar
services: media-services
keywords: klipp; subclip; encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685078"
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper
I det här avsnittet visas de grundläggande stegen för att komma igång med Azure Media Clipper. Avsnitt som följer innehåller information om hur du konfigurerar Azure Media Clipper.

- Lägg först till följande länkar för Azure Media Player och Azure Media Clipper i dokumentets huvud. Vi rekommenderar att uttryckligen ange en version av Clipper och Azure Media Player i URL: erna. Använd inte den senaste versionen av dessa resurser i produktion eftersom de kan komma att ändras på begäran.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Lägg sedan till följande klasser i DIV-elementet där du vill instansiera Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Om du vill aktivera det mörka temat lägger du till klassen mörk-skal:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Sedan instansierar du Clipper med följande API-anrop:

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

Parametrarna för initierings metod anropet är:
- `selector` {REQUIREd, String}: CSS-väljare för det matchande HTML-element där widgeten ska återges.
- `restVersion` {REQUIREd, String}: Azure Media Services REST API version till mål. REST-versionen definierar formatet på utdata som genereras av widgeten. För närvarande stöds endast 2,0.
- `submitSubclipCallback` {REQUIREd} återanrops funktionen anropades när användaren klickar på knappen Skicka för widgeten. Motringningsfunktionen ska förvänta sig utdata som genereras av widgeten (en rendering Job-konfiguration eller en filter definition). Mer information finns i skicka återanrop från under klipp.
- `logLevel` {valfritt, {"info", "warn", "error '}}: den loggnings nivå som ska visas i webbläsarens konsol. Standardvärde: fel
- `minimumMarkerGap` {valfri, int}: minsta storlek på ett under klipp (i sekunder). Obs! värdet måste vara större än eller lika med 6, vilket även är standardvärdet.
- `singleBitrateMp4Profile` {valfritt, JSON-objekt} en MP4-profil med enkel bit hastighet som används för att rendera jobb konfigurationen som genereras av widgeten. Om detta inte anges används [standard-MP4-profilen med enkel bit hastighet](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {valfritt, JSON-objekt} MP4-profilen med flera bit hastigheter som används för att rendera jobb konfigurationen som genereras av widgeten. Om detta inte anges används [standard-MP4-profilen med flera bit](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)hastigheter.
- `keymap` {valfritt, JSON-objekt} gör det möjligt att anpassa tangent bords gen vägarna i widgeten. Mer information finns i [anpassningsbara](media-services-azure-media-clipper-keyboard-shortcuts.md)kortkommandon.
- `assetsPanelLoaderCallback` {valfritt, Promise} motringningsfunktionen som anropades för att läsa in (asynkront) en ny sida med till gångar i fönstret till gångar varje gång användaren rullar nedåt längst ned i fönstret. Mer information finns i till gångs fönstret inläsnings återanrop.
- `height` {valfritt, Number} den totala höjden för widgeten (minsta höjd är 600 BPT utan till gångs fönstret och 850 px med fönstret till gångar).
- `subclippingMode` (valfritt, {' all ', ' rendera ', ' filter '}): under urklipps läge (s) tillåts. Standardvärdet är alla.
- `filterAssetsTypes` (valfritt, bool): filterAssetsTypes låter dig Visa eller dölja List rutan filter från fönstret till gångar. Standardvärdet är true.
- `speedLevels` (valfritt, matris): speedLevels gör det möjligt att ange olika hastighets nivåer för videos pelaren. mer information finns i [Azure Media Player-dokumentationen](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) .
- `resetOnJobDone` (valfritt, bool): resetOnJobDone tillåter att Clipper återställer under Urklipp till ett ursprungligt tillstånd när ett jobb har skickats.
- `autoplayVideo` (valfritt, bool): autoplayVideo gör att Clipper kan spela upp videon vid inläsningen. Standardvärdet är true.
- `language` {valfritt, String}: språket anger språket för widgeten. Om inget anges försöker widgeten lokalisera meddelandena baserat på webbläsarens språk. Om inget språk identifieras i webbläsaren, använder widgeten standardvärdet engelska. Mer information finns i avsnittet [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md) .
- `languages` {valfritt, JSON}: parametern languages ersätter standard ord listan med språk med en anpassad ord lista som definierats av användaren. Mer information finns i avsnittet [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md) .
- `extraLanguages` (valfritt, JSON): parametern extraLanguages lägger till nya språk i standard ord listan. Mer information finns i avsnittet [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md) .

## <a name="typescript-definition"></a>TypeScript-definition
En definitions fil för [typescript](https://www.typescriptlang.org/) för Clipper hittar du [här](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper-API
I det här avsnittet dokumenteras API-ytan som tillhandahålls av Clipper.

- `ready(handler)`: är ett sätt att köra Java Script så snart som Clipper är fullständigt inläst och redo att användas.
- `load(assets)`: läser in en lista över till gångar i tids linjen för widgeten (ska inte användas tillsammans med assetsPanelLoaderCallback). I den här [artikeln](media-services-azure-media-clipper-load-assets.md) finns mer information om hur du läser in till gångar i Clipper.
- `setLogLevel(level)`: anger den loggnings nivå som ska visas i webbläsarens konsol. Möjliga värden är: `info`, `warn``error`.
- `setHeight(height)`: ställer in den totala höjden för widgeten i bild punkter (den minsta höjden är 600 BPT utan till gångs fönstret och 850 px med fönstret till gångar).
- `version`: hämtar widgeten version.

## <a name="next-steps"></a>Nästa steg
Se nästa steg för att konfigurera Azure Media Clipper:
- [Läsa in till gångar i Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurera anpassade kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Skicka urklipps jobb från Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurera lokalisering](media-services-azure-media-clipper-localization.md)
