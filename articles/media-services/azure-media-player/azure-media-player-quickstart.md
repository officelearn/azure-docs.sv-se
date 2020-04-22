---
title: Snabbstart för Azure Media Player
description: Lär dig de grundläggande stegen för att konfigurera Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726468"
---
# <a name="azure-media-player-quickstart"></a>Snabbstart i Azure Media Player
Azure Media Player är enkelt att konfigurera. Det tar bara några minuter att få grundläggande uppspelning av medieinnehåll från ditt Azure Media Services-konto. I det här avsnittet visas de grundläggande stegen utan mer detaljerad information. De avsnitt som följer ger dig detaljer om hur du konfigurerar och konfigurerar Azure Media Player.  Lägg bara till följande inkluderingar i dokumentets `<head>`:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Du **NOT** bör INTE `latest` använda den version i produktion, eftersom detta kan ändras på begäran. Ersätt `latest` med en version av Azure Media Player; till exempel `latest` `1.0.0`ersätta med . Azure Media Player-versioner kan efterfrågas [härifrån](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Använda videoelementet

Använd sedan bara `<video>` elementet som vanligt, men `data-setup` med ytterligare ett attribut som innehåller eventuella alternativ. Dessa alternativ kan inkludera alla Azure Media Services-alternativ i ett giltigt JSON-objekt.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Om du inte vill använda automatisk konfiguration kan du `data-setup` utelämna attributet och initiera ett videoelement manuellt.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)