---
title: Använda befintliga spelare för att spela upp ditt innehåll - Azure | Microsoft-dokument
description: I den här artikeln visas befintliga spelare som du kan använda för att spela upp ditt innehåll.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2d3c22e17c37bc46c16a9cc80eb3cf4b9ec93ecf
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686928"
---
# <a name="playing-your-content-with-existing-players"></a>Spela upp ditt innehåll med befintliga spelare
Azure Media Services stöder många populära direktuppspelningsformat, till exempel Smooth Streaming, HTTP Live Streaming och MPEG-Dash. Det här avsnittet pekar dig till befintliga spelare som du kan använda för att testa dina strömmar.

### <a name="the-azure-portal-media-services-content-player"></a>Innehållsspelaren för Azure portal Media Services
**Azure-portalen** tillhandahåller en innehållsspelare som du kan använda för att testa din video.

Klicka på önskad video (se till att den [publicerades)](media-services-portal-publish.md)och klicka på **play-knappen** längst ned på portalen.

Vissa förutsättningar gäller:

* **MEDIA SERVICES-INNEHÅLLSSPELAREN** spelar upp från strömningsslutpunkten som är standard. Använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Använd [Azure Media Player](https://aka.ms/azuremediaplayer) för att spela upp ditt innehåll (rensat eller skyddat) i något av följande format:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressiv MP4

### <a name="flash-player"></a>Flash-spelare

#### <a name="playready-with-token"></a>PlayReady med token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH Spelare

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Annat
Så här testar du HLS-url:er kan du också använda:

* **Safari** på en iOS-enhet eller
* **3ivx HLS Player** på Windows.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
