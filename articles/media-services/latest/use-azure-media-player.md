---
title: Spela upp med Azure Media Player - Azure | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: 4677fc9a56db3190073b3f310a51632055510bc2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028882"
---
# <a name="play-back-with-azure-media-player"></a>Spela upp med Azure Media Player

[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) är en web videospelare som utformats för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter. Azure Media Player används branschstandarder som HTML5, Media käll-tillägg (MSE) och tillägg EME (Encrypted Media) för att ge en avancerad och anpassningsbar direktuppspelning. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelningsteknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Detta gör innehåll som hanteras av Azure Media Services kan spelas upp på ett brett utbud av enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services kan innehåll tillhandahålls med HLS, DASH, Smooth Streaming strömningsformat att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt den bästa länk beroende på plattform webbläsarkapacitet. Media Services kan även användas för dynamisk kryptering av tillgångar med PlayReady-kryptering eller AES-128 bitars kuvert-kryptering. Azure Media Player möjliggör med PlayReady och AES-128 bitars krypterat innehåll när korrekt konfigurerad. 

[Starta din kostnadsfria utvärderingsversion](http://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="monitor-diagnostics-of-a-video-stream"></a>Övervaka diagnostik av en videoström

Du kan använda den [Demonstrationssida för Azure Media Player](http://aka.ms/amp) att övervaka diagnostik av en videoström. 

! [Azure Media Player diagnostik] [amp_diagnostics]

## <a name="next-steps"></a>Nästa steg

- [Registrera dig för att hålla dig uppdaterad med senast från Azure Media Player](http://azuremediaplayerdemo.azurewebsites.net/amp_signup.html)
- [Dokumentation om Azure Media Player](https://aka.ms/ampdocs)
- [Azure Media Player-exempel](https://aka.ms/ampsamples)
