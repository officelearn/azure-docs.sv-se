---
title: Använd befintliga spelare att spela upp ditt innehåll – Azure | Microsoft Docs
description: Det här avsnittet listar befintliga spelare som du kan använda att spela upp ditt innehåll.
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
ms.openlocfilehash: c96710d6dcca9f5ef99b3a02a0bc875d433f814d
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257439"
---
# <a name="playing-your-content-with-existing-players"></a>Spela upp ditt innehåll med befintliga spelare
Azure Media Services har stöd för många populära strömmande format, till exempel Smooth Streaming, HTTP Live Streaming och MPEG-Dash. Det här avsnittet hänvisar till befintliga spelare som du kan använda för att testa dina strömmar.

### <a name="the-azure-portal-media-services-content-player"></a>I Azure portal Media Services-innehållsspelaren
Den **Azure** portal har en innehållsspelare som du kan använda för att testa videon.

Klicka på önskad video (se till att det var [publicerade](media-services-portal-publish.md)) och klicka på den **spela upp** längst ned i portalen.

Vissa förutsättningar gäller:

* **MEDIA SERVICES-INNEHÅLLSSPELAREN** spelar upp från strömningsslutpunkten som är standard. Använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Använd [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) att spela upp ditt innehåll (rensa eller skyddade) i något av följande format:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressiva MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>AES-krypterad med Token
[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Silverlight-spelare

#### <a name="playready-with-token"></a>PlayReady med Token
[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH-spelare
[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Annat
Så här testar HLS-webbadresser som du kan också använda:

* **Safari** på en iOS-enhet eller
* **3ivx HLS Player** på Windows.

## <a name="developing-video-players"></a>Utveckla videospelare
Information om hur du utvecklar dina egna spelare finns i [utveckla videospelare](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
