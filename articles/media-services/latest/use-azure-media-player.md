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
ms.openlocfilehash: 6de626323c82689d0ead4f5aaad2a2e43187ebd0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897001"
---
# <a name="azure-media-player-overview"></a>Översikt över Azure Media Player

Azure Media Player är en web videospelare som utformats för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter. Azure Media Player används branschstandarder som HTML5, Media käll-tillägg (MSE) och tillägg EME (Encrypted Media) för att ge en avancerad och anpassningsbar direktuppspelning. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelningsteknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Detta gör innehåll som hanteras av Azure Media Services kan spelas upp på ett brett utbud av enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services kan innehåll tillhandahålls med HLS, DASH, Smooth Streaming strömningsformat att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt den bästa länk beroende på plattform webbläsarkapacitet. Media Services kan även användas för dynamisk kryptering av tillgångar med PlayReady-kryptering eller AES-128 bitars kuvert-kryptering. Azure Media Player möjliggör med PlayReady och AES-128 bitars krypterat innehåll när korrekt konfigurerad. 

[Starta din kostnadsfria utvärderingsversion](https://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Med hjälp av sidan för Azure Media Player-demon

### <a name="start-using"></a>Börja använda

Du kan använda den [Demonstrationssida för Azure Media Player](https://aka.ms/amp) att spela upp Azure Media Services-exempel eller dina egna stream.  

För att spela upp en ny video, klistra in en annan URL och trycker på **uppdatering**.

För att konfigurera olika alternativ för uppspelning (till exempel teknisk, språk eller kryptering), trycker du på **avancerade alternativ**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Övervaka diagnostik av en videoström

Du kan använda den [Demonstrationssida för Azure Media Player](https://aka.ms/amp) att övervaka diagnostik av en videoström. 

![Azure Media Player-diagnostik](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurera Azure Media Player i din HTML

Azure Media Player är enkla att konfigurera. Det tar bara en liten stund att hämta grundläggande uppspelning av medieinnehåll från ditt Media Services-konto. Se [dokumentation för Azure Media Player](https://aka.ms/ampdocs) för information om hur du skapar och konfigurerar Azure Media Player. 

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure Media Player](https://aka.ms/ampdocs)
- [Azure Media Player-exempel](https://aka.ms/ampsamples)
