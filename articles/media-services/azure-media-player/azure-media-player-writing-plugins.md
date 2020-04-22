---
title: Skriva plugins för Azure Media Player
description: Lär dig hur du skriver en plugin med Azure Media Player med JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727117"
---
# <a name="writing-plugins-for-azure-media-player"></a>Skriva plugins för Azure Media Player #

En plugin är JavaScript skriven för att förlänga eller förbättra spelaren. Du kan skriva plugins som ändrar Azure Media Player utseende, dess funktionalitet eller ens har det gränssnitt med andra tjänster. Du kan göra detta i två enkla steg:

## <a name="step-1"></a>Steg 1 ##

Skriv ditt JavaScript i en funktion som så:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Du kan skriva koden direkt på `<script>` HTML-sidan i taggar eller i en extern JavaScript-fil. Om du gör det senare måste du inkludera `<head>` JavaScript-filen på HTML-sidan *efter* AMP-skriptet.

Exempel:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Steg 2 ##
Initiera plugin med JavaScript på ett av två sätt:

Metod 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Metod 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Plugin alternativ krävs inte, inklusive dem bara tillåter utvecklare som använder din plugin för att konfigurera sitt beteende utan att behöva ändra källkoden.

För inspiration och fler exempel på att skapa en plugin ta en titt på vårt [galleri](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Plugin-kod ändrar dynamiskt objekt i DOM under livslängden för betraktarens spelarupplevelse, gör det aldrig permanenta ändringar i spelarens källkod. Det är här en förståelse för din webbläsares utvecklarverktyg kommer väl till pass. Om du till exempel vill ändra utseendet på ett element i spelaren kan du hitta html-elementet efter klassnamn och sedan lägga till eller ändra attribut därifrån. Här är en bra resurs för [att ändra HTML-attribut.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Integrerade insticksprogram ###

 Det finns för närvarande två plugins bakade i AMP: [time-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) och [snabbtangenter](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Dessa plugins utvecklades ursprungligen för att vara modulära plugins för spelaren men ingår nu i spelarens källkod.

### <a name="plugin-gallery"></a>Plugin Galleri ###

[Plugin-galleriet](http//:aka.ms/ampplugins) har flera plugins som communityn redan har bidragit med för funktioner som tidsbaserade markörer, zoom, analys med mera. Sidan ger åtkomst till plugins och instruktioner om hur man ställer in den samt en demo som visar plugin i aktion. Om du skapar en cool plugin som du tycker ska ingå i vårt galleri, gärna lämna in den så att vi kan kolla upp det.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)