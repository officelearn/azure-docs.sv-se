---
title: LiveEvent-tillstånd och fakturering i Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services LiveEvent-tillstånd och fakturering.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543083"
---
# <a name="live-event-states-and-billing"></a>Live händelse tillstånd och fakturering

I Azure Media Services börjar en Live-händelse att faktureras så fort dess tillstånds över gångar **körs**. Du debiteras även om det inte finns någon video som går igenom tjänsten. Om du vill stoppa direkt sändningen från fakturering måste du stoppa Live-händelsen. Direkt avskrift debiteras på samma sätt som direkt sändningen.

När **LiveEventEncodingType** på din [Live-händelse](https://docs.microsoft.com/rest/api/media/liveevents) är inställt på Standard eller Premium1080p stänger Media Services automatiskt av alla aktiva händelser som fortfarande är i **körnings** tillstånd 12 timmar efter att inmatnings flödet förloras och det inte finns någon **Live-utdata**som körs. Men du kommer fortfarande att faktureras för den tidpunkt då Live-händelsen var i **körnings** tillstånd.

> [!NOTE]
> Direkt sändnings händelser stängs inte av automatiskt och måste stoppas explicit genom API: et för att undvika överdriven fakturering. 

## <a name="states"></a>Tillstånd

Live-händelsen kan vara i något av följande tillstånd.

|Status|Beskrivning|
|---|---|
|**Stoppad**| Det här är start läget för Live-händelsen när den har skapats (om inte Autostart har angetts till true.) Ingen fakturering sker i det här läget. I det här läget kan egenskaperna för Live-händelser uppdateras, men strömning är inte tillåtet.|
|**Leds**| Direktsänd händelse startas och resurserna allokeras. Ingen fakturering sker i det här läget. Uppdateringar eller strömmande tillåts inte i det här läget. Om ett fel inträffar återgår Live-händelsen till stoppat tillstånd.|
|**Körs**| Live Event-resurserna har allokerats, inmatnings-och för hands-URL: er har genererats och kan ta emot Live-strömmar. Faktureringen är nu aktiv. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering.|
|**Stoppas**| Direkt sändningen stoppas och resurserna etableras. Ingen fakturering sker i det här tillfälliga läget. Uppdateringar eller strömmande tillåts inte i det här läget.|
|**Rader**| Live-händelsen tas bort. Ingen fakturering sker i det här tillfälliga läget. Uppdateringar eller strömmande tillåts inte i det här läget.|

Du kan välja att aktivera Live-avskrifter när du skapar en Live-händelse. Om du gör det debiteras du för direkt avskrifter när Live-händelsen är i **körnings** tillstånd. Observera att du kommer att faktureras även om det inte finns något ljud som passerar direkt evenemanget.

## <a name="next-steps"></a>Nästa steg

- [Översikt över direkt uppspelning](live-streaming-overview.md)
- [Själv studie kurs om Live-direktuppspelning](stream-live-tutorial-with-api.md)
