---
title: Uppspelning med Azure Media Player – Azure | Microsoft-dokument
description: Azure Media Player är en webbvideospelare som är byggd för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673586"
---
# <a name="azure-media-player-overview"></a>Azure Media Player-översikt

Azure Media Player är en webbvideospelare som är byggd för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter. Azure Media Player använder branschstandarder, till exempel HTML5, MSE (Media Source Extensions) och EME (Encrypted Media Extensions) för att ge en berikad adaptiv direktuppspelningsupplevelse. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare använder Azure Media Player Flash och Silverlight som reservteknik. Oavsett vilken uppspelningsteknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för åtkomst till API:er. Detta gör det möjligt att spela upp innehåll som betjänas av Azure Media Services på en mängd olika enheter och webbläsare utan extra ansträngning.

Med Microsoft Azure Media Services kan innehåll visas med HLS-, DASH-, Smooth Streaming-streamingformat för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt den bästa länken baserat på plattforms-/webbläsarfunktionerna. Media Services möjliggör också dynamisk kryptering av tillgångar med PlayReady-kryptering eller AES-128-bitars kuvertkryptering. Azure Media Player möjliggör dekryptering av PlayReady och AES-128-bitars krypterat innehåll när det är korrekt konfigurerat. 

> [!NOTE]
> HTTPS-uppspelning krävs för Widevine-krypterat innehåll.

## <a name="use-azure-media-player-demo-page"></a>Använd demosidan för Azure Media Player

### <a name="start-using"></a>Börja använda

Du kan använda [demosidan](https://aka.ms/azuremediaplayer) för Azure Media Player för att spela upp Exempel på Azure Media Services eller din egen ström.  

Om du vill spela upp en ny video klistrar du in en annan webbadress och trycker på **Uppdatera**.

Om du vill konfigurera olika uppspelningsalternativ (till exempel teknik, språk eller kryptering) trycker du på **Avancerade alternativ**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Övervaka diagnostik av en videoström

Du kan använda [demosidan](https://aka.ms/azuremediaplayer) för Azure Media Player för att övervaka diagnostik av en videoström. 

![Diagnostik i Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurera Azure Media Player i HTML-koden

Azure Media Player är enkelt att konfigurera. Det tar bara en liten stund att få grundläggande uppspelning av medieinnehåll från ditt Media Services-konto. Mer information om hur du konfigurerar och konfigurerar Azure Media Player finns i Dokumentationen till [Azure Media Player.](https://aka.ms/ampdocs) 

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

- [Azure Media Player-dokumentation](https://aka.ms/ampdocs)
- [Exempel på Azure Media Player](https://aka.ms/ampsamples)
