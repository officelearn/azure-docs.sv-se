---
title: Plugingalleri för Azure Media Player
description: Den här artikeln innehåller en lista över plugins som är tillgängliga för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726482"
---
# <a name="azure-media-player-plugin-gallery"></a>Plugingalleri för Azure Media Player #

## <a name="plugins"></a>Plugin-program ##

| Plugin namn                         | Demo URL                    | Källkod                | Beskrivning    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Ytterligare funktioner                 | | | |
| **Nya!** AMP360Video                | [Demonstration](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Insticksprogrammet låter dig visualisera 360-video i Amp antingen på skrivbordet eller i VR-kompatibla enheter. Den fullständiga dokumentationen finns [här:](https://doc\.babylonjs\.com/extensions/amp360video) |
|  Sprite Tips                         | [Demonstration](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Player (AMP) plugin för tidslinje rendering av en video miniatyrbild sprite som genereras från Azure Media Services (AMS) Media Encoder Standard (MES). |
| Överlägg för diagnostik                 | [Demonstration](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Den här insticksprogrammet visar: Alla nyckelparametrar, statistik för video, alla händelser i livscykeln för videouppspelning och DRM-skyddsinformation, till exempel nyckel-ID, licensförvärv webbadresser, om de är skyddade.                                                                                                                                                                      |
| Bildrutehastighet och tidskodkalkylator | [Demonstration](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Denna plugin beräknar bildfrekvensen för `tfhd` / `trun` video baserat på MP4-rutorna i det första MPEG-DASH-videofragmentet, tolkar tidsskalevärdet från MPEG-DASH-klientmanifestet, och ger också ett sätt att generera tidskoden för en viss absolut tid från spelaren (samt ger spelaren absolut tid med tanke på tidskoden) |
| <strike>Uppspelningshastighet</strike>                      | [Demonstration](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Denna plugin gör det möjligt för tittarna att styra vilken hastighet av videon. *Observera att den här funktionen är automatiskt tillgänglig i version AMP v2.0.0+ men inaktiveras som standard.* Om du vill veta hur du aktiverar det, kolla in våra prover [här](https://github.com/Azure-Samples/azure-media-player-samples) |
| Tips för hovringstid                      | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Visar ett tidstips över förloppsindikatorn på mushovst för korrekt tidssökning. *Obs: Denna plugin är redan integrerad i AMP* men om du är intresserad av att se hur det är programmerad gärna ta en titt.                                                                                                                       |
| Rubriköverlägg                       | [Demonstration](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Överlägg konfigurerbara videotitel över skärmen |
| Tidslinje markörer                    | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Denna plugin tar i en rad gånger och överlägg små markörer över förloppsindikatorn vid dessa tillfällen. |
| Analytics                           | | | |
| Application Insights                | [Blogginlägg](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plugin som spårar dina spelarmått och portar den till Power BI för en intuitiv grafisk representation av tittarnas spelarupplevelse. |
| Google Analytics                    | Ej tillämpligt                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics-plugin för Azure Media Player |
| Diagnostik                         | | | |
| Diagnostikutdata                  | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Denna plugin utdata en rad diagnostik från din spelare, för att se det i aktion gå till demo länken och öppna upp din JavaScript-konsol. |
| Enkel åtkomst                      | | | |
| Zooma in                             | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Den här insticksprogrammet visar en dra-kunna zoom-in skala på spelarens skärm så att tittarna kan zooma in på ditt innehåll |
| Live Bildtexter                       | [Azure blogginlägg](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[SubPly Post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | Ej tillämpligt | *Se inlägget för mer info.* End to End-arbetsflödet utformat för live-textningsbyggd plugin för Azure Media Player, klicka på den vänstra linn för att gå till SubPlys webbplats och lära dig mer om lösningen |
| Snabbtangenter                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Snabbtangenterna plugin gör det möjligt för tittarna att styra olika aspekter av spelaren med generiska plugin kombinationer som F för fullscreen, M för mute och piltangenterna för förloppsindikator kontroll. *Obs: Denna plugin har redan integrerats i AMP men gärna använda den som en resurs* |
| Sociala medier                              | | | |
| Dela                               | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Den här insticksprogrammet lägger till en delningsknapp i spelarens kontrollfält så att tittarna kan dela videon de tittar på med sina vänner via Facebook, Twitter eller Linkedin. |

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)