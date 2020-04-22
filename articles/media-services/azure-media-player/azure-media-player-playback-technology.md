---
title: Uppspelningsteknik för Azure Media Player
description: Läs mer om uppspelningstekniken som används för att spela upp videon eller ljudet.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726489"
---
# <a name="playback-technology-tech"></a>Uppspelningsteknik ("tech") #

Uppspelningsteknik avser den specifika webbläsar- eller plugin-teknik som används för att spela upp videon eller ljudet.

- **azureHtml5JS**: använder MSE- och EME-standarder tillsammans med videoelementet för plugin-mindre baserad uppspelning av DASH-innehåll med stöd för AES-128-bitars kuvertkrypterat innehåll eller DRM gemensamt krypterat innehåll (via PlayReady och Widevine när webbläsaren stöder det) från Azure Media Services
- **flashSS:** använder Flash Player-teknik för att spela upp Smooth-innehåll med stöd för AES-128-bitars kuvertdekrypning från Azure Media Services - kräver Flash-version av 11,4 eller senare
- **html5FairPlayHLS**: använder Safari specifikt i webbläsarbaserad uppspelningsteknik via HLS med videoelementet. Den här tekniken kräver att du spelar upp FairPlay-skyddat innehåll från Azure Media Services och lades till i techOrder från och med 10/19/16
- **silverlightSS**: använder silverlight-teknik för att spela upp Smooth-innehåll med stöd för PlayReady-skyddat innehåll från Azure Media Services.
- **html5**: använder i webbläsarbaserad uppspelningsteknik med videoelementet.  När du är på en Apple iOS- eller Android-enhet tillåter den här tekniken uppspelning av HLS-strömmar med visst grundläggande stöd för AES-128-bitars kuvertkryptering eller DRM-innehåll (via FairPlay när webbläsaren stöder det).

## <a name="tech-order"></a>Teknisk order ##

För att säkerställa att din tillgång kan spelas på en mängd olika enheter rekommenderas följande `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` tekniska ordning och `<video>` är standard om: och kan ställas in direkt på eller programatiskt i alternativen:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

eller

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Kompatibilitetsmatris ##

Med tanke på den rekommenderade tekniska ordern med strömmande innehåll från Azure Media Services förväntas följande kompatibilitetsuppspelningsmatris

| Webbläsare        | Operativsystem                                                       | Förväntad teknik (klar)  | Förväntad teknik (AES)  | Förväntad teknik (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11 (På andra)      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| Dvs 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | blinkAR                | blinkAR              | silverlightSS (PlayReady)    |
| Internet Explorer 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| Edge           | Xbox One<sup>1</sup> (uppdatering nov 2015)                   | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | blinkAR                | blinkAR              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5 (på andra)                  | html5 (ingen token)3    | stöds inte                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | blinkAR                | blinkAR              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5 (på andra)                  | html5 (ingen token)<sup>3</sup>    | stöds inte                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| Dvs 8           | Windows                                                  | stöds inte          | stöds inte        | stöds inte                |

<sup>1</sup> Konfiguration stöds eller testas inte; som referens för slutförande.

<sup>2</sup> Lyckad uppspelning på Android-enheter kräver en kombination av enhetsfunktioner, grafikstöd, codec-rendering, OS-stöd med mera. Eftersom Android är en plattform med öppen källkod som gör det möjligt för telefontillverkare att ändra Vanilla Android OS som tillhandahålls av Google, orsakar detta viss fragmentering i Android-utrymmet, och vissa enheter kanske inte stöds på grund av brist på funktioner. Dessutom har vissa Android-enheter inte stöd för alla codec-enheter.  

<sup>3</sup> I de fall där det inte finns något stöd för token kan en proxy användas för att lägga till den här funktionen. Kolla in den här [bloggen](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) för att lära dig mer om den här lösningen.

> [!NOTE]
> Om den förväntade teknik som valts kräver att en plugin installeras, som Flash, och som inte är installerad på användarens dator, kommer AMP att fortsätta att kontrollera funktionerna i nästa teknik, tillsammans med källtyper och skyddsinformation, i tekniklistan. Om du till exempel försöker visa en oskyddad ström på begäran i Safari 8 på OS X Yosemite och både Flash och Silverlight inte är installerade, väljer AMP den inbyggda Html5-tekniken för uppspelning.<br/><br/>Ny webbläsarteknik växer fram dagligen, och som sådan kan påverka denna matris.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)