---
title: Uppspelning med Azure Media Player – Azure | Microsoft Docs
description: Azure Media Player är en webb Videos pelare som skapats för att spela upp medie innehåll från Microsoft Azure Media Services på en rad olika webbläsare och enheter.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673586"
---
# <a name="azure-media-player-overview"></a>Översikt över Azure Media Player

Azure Media Player är en webb Videos pelare som skapats för att spela upp medie innehåll från Microsoft Azure Media Services på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att ge en omfattande anpassningsbar strömmande upplevelse. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelnings teknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Detta gör att innehåll som hanteras av Azure Media Services kan spelas upp i en mängd olika enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services gör att innehåll kan hanteras med HLS, bindestreck, Smooth Streaming strömnings format för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt upp den bästa länken utifrån plattforms-och webb läsar funktionerna. Media Services också tillåta dynamisk kryptering av till gångar med PlayReady-kryptering eller AES-128-bitars kryptering. Azure Media Player tillåter dekryptering av PlayReady-och AES-128-bitars krypterat innehåll när det är lämpligt konfigurerat. 

> [!NOTE]
> HTTPS-uppspelning krävs för Widevine-krypterat innehåll.

## <a name="use-azure-media-player-demo-page"></a>Använd Azure Media Player demonstrations sida

### <a name="start-using"></a>Börja använda

Du kan använda [Azure Media Player demonstrations sidan](https://aka.ms/azuremediaplayer) för att spela upp Azure Media Services exempel eller din egen data ström.  

Om du vill spela upp en ny video klistrar du in en annan URL och klickar på **Uppdatera**.

Om du vill konfigurera olika uppspelnings alternativ (till exempel teknik, språk eller kryptering) trycker du på **Avancerade alternativ**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Övervaka diagnostiken för en video ström

Du kan använda [sidan Azure Media Player demonstration](https://aka.ms/azuremediaplayer) för att övervaka diagnostiken av en video ström. 

![Azure Media Player diagnostik](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurera Azure Media Player i din HTML

Azure Media Player är enkelt att konfigurera. Det tar bara en liten stund att få en grundläggande uppspelning av medie innehåll från ditt Media Services-konto. Mer information om hur du konfigurerar och konfigurerar Azure Media Player finns i [Azure Media Player-dokumentationen](https://aka.ms/ampdocs) . 

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

- [Azure Media Player-dokumentation](https://aka.ms/ampdocs)
- [Azure Media Player-exempel](https://aka.ms/ampsamples)
