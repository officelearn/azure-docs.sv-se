---
title: "Komma igång med Azure Media Clipper | Microsoft Docs"
description: "Komma igång med Azure Media Clipper, klipp ett verktyg för att skapa media från tillgångar"
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
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
- `language`{VALFRI sträng}: språk ställer in språket för validering. Om den inte anges försöker widgeten localize meddelanden utifrån språkinställningar för webbläsaren. Om inget språk har identifierats i webbläsaren, standard widgeten engelska. Mer information finns i avsnittet språk som stöds.
- `languages`{VALFRITT, JSON}: parametern språk ersätter standardordlistan språk med en ordlista som definierats av användaren. Mer information finns i avsnittet språk som stöds.
- `extraLanguages`(Valfritt, JSON): parametern extraLanaguages lägger till nya språk i standardordlista. Mer information finns i avsnittet språk som stöds.

## <a name="typescript-definition"></a>Maskin-definition
En [maskin](https://www.typescriptlang.org/) definitionsfilen för Clipper hittar [här](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
Det här avsnittet beskrivs API-ytan som tillhandahålls av Clipper.

- `load(assets)`: läser in en lista över tillgångar i fönstret tillgångar (ska inte användas tillsammans med `assetsPanelLoaderCallback`). Se den här [artikeln](media-services-azure-media-clipper-load-assets.md) information om hur du läser in tillgångar i Clipper.
- `setLogLevel(level)`: Anger loggningsnivån som ska visas i webbläsarens konsol. Möjliga värden är: `info`, `warn`, `error`.
- `setHeight(height)`: Anger widgeten totala höjd i bildpunkter (minimihöjden är 600 bildpunkter utan tillgångar rutan och 850 px med fönstret tillgångar).
- `version`: hämtar widget-version.

## <a name="configuring-azure-media-clipper"></a>Konfigurera Azure Media Clipper
Se nästa steg för att konfigurera Azure Media Clipper:
- [Läsa in tillgångar i Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurera anpassade kortkommandon](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Skickar urklippet jobb från Clipper](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>Språk som stöds
Widgeten Clipper är tillgänglig i 18 språk. Om du vill ange widget språk måste du definiera den `language` parametern under initieringen. Skicka in önskat språk kodsträngen i listan nedan:
- Kinesiska (förenklad): zh-hans
- Kinesiska (traditionell): zh-hant
- Tjeckiska: cs
- Nederländska, Flemish: nl
- Engelska: en
- Franska: fr
- Tyska: Tyskland
- Ungerska: hu
- Italienska: den
- Japanska: ja
- Koreanska: ko
- Polska: pl
- Portugisiska (Brasilien): pt-br
- Portugisiska (Portugal): pt-pt
- Ryska: ru
- Spanska: es
- Svenska: SA
- Turkiska: tr

Om du vill ange en anpassad ordlista eller utöka standardspråk och ordlista, måste du definiera den `languages` eller `extraLanguages` parametern respektive. Skicka in en ordlista med hjälp av följande JSON-format:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Till exempel definierar i följande exempel lokaliserade engelska strängar:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```