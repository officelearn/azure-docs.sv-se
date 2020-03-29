---
title: LiveEvent-tillstånd och fakturering i Azure Media Services | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över Azure Media Services LiveEvent-tillstånd och fakturering.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543083"
---
# <a name="live-event-states-and-billing"></a>Tillstånd och fakturering för liveevenemang

I Azure Media Services börjar en livehändelse fakturering så snart tillståndet övergår till **Att köra**. Du kommer att faktureras även om det inte finns någon video som flödar genom tjänsten. Om du vill förhindra att Live Event faktureras måste du stoppa livehändelsen. Live Transkription faktureras på samma sätt som Live Event.

När **LiveEventEncodingType** på din [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) är inställt på Standard eller Premium1080p stängs Media Services automatiskt av alla Live Event som fortfarande är i **körtillståndet** 12 timmar efter att inmatningsflödet har gått förlorat och det inte körs några **liveutdata.** Du kommer dock fortfarande att faktureras för den **Running** tid livehändelsen var i körtillståndet.

> [!NOTE]
> Direktupppassningshändelser stängs inte av automatiskt och måste uttryckligen stoppas via API:et för att undvika överdriven fakturering. 

## <a name="states"></a>Tillstånd

Livehändelsen kan vara i något av följande tillstånd.

|Status|Beskrivning|
|---|---|
|**Stoppad**| Detta är det ursprungliga tillståndet för Live Event efter skapandet (om inte automatisk start har ställts in på true.) Ingen fakturering sker i det här tillståndet. I det här läget kan egenskaperna Live Event uppdateras men direktuppspelning är inte tillåtet.|
|**Start**| Livehändelsen startas och resurser fördelas. Ingen fakturering sker i det här tillståndet. Uppdateringar eller direktuppspelning är inte tillåtna under det här tillståndet. Om ett fel inträffar återgår livehändelsen till tillståndet Stoppad.|
|**Körs**| Live Event-resurserna har allokerats, intjest och förhandsgranska webbadresser har genererats och kan ta emot livestreams. Nu är faktureringen aktiv. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering.|
|**Stoppas**| Live-händelsen stoppas och resurser avetablering. Ingen fakturering sker i det här tillfälliga tillståndet. Uppdateringar eller direktuppspelning är inte tillåtna under det här tillståndet.|
|**Ta bort**| Livehändelsen tas bort. Ingen fakturering sker i det här tillfälliga tillståndet. Uppdateringar eller direktuppspelning är inte tillåtna under det här tillståndet.|

Du kan välja att aktivera live transkriptioner när du skapar livehändelsen. Om du gör det debiteras du för live transkriptioner när livehändelsen är i **körtillstånd.** Observera att du kommer att faktureras även om det inte finns något ljud som flödar genom Live Event.

## <a name="next-steps"></a>Nästa steg

- [Översikt över livestreaming](live-streaming-overview.md)
- [Självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)
