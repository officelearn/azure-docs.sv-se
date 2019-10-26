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
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933685"
---
# <a name="live-event-states-and-billing"></a>Live händelse tillstånd och fakturering

I Azure Media Services börjar en Live-händelse att faktureras så fort dess tillstånds över gångar **körs**. Om du vill stoppa direkt sändningen från fakturering måste du stoppa Live-händelsen.

När **LiveEventEncodingType** på din [Live-händelse](https://docs.microsoft.com/rest/api/media/liveevents) är inställt på standard eller Premium1080p, stänger Media Services automatiskt av all direkt händelse som fortfarande är i **körnings** tillstånd 12 timmar efter att inmatnings flödet förloras och det inte finns några **direktsända utdata** s körs. Men du kommer fortfarande att faktureras för den tidpunkt då Live-händelsen var i **körnings** tillstånd.

> [!NOTE]
> Direkt sändnings händelser stängs inte av automatiskt och måste stoppas explicit genom API: et för att undvika överdriven fakturering. 

## <a name="states"></a>Tillstånd

Live-händelsen kan vara i något av följande tillstånd.

|Status|Beskrivning|
|---|---|
|**Stoppats**| Det här är start läget för Live-händelsen när den har skapats (om inte Autostart har angetts till true.) Ingen fakturering sker i det här läget. I det här tillståndet kan direktsändningens egenskaper uppdateras, men strömning är inte tillåtet.|
|**Leds**| Direktsänd händelse startas och resurserna allokeras. Ingen fakturering sker i det här läget. Uppdateringar eller strömmande tillåts inte i det här läget. Om ett fel inträffar återgår Live-händelsen till stoppat tillstånd.|
|**Körs**| Live Event-resurserna har allokerats, inmatnings-och för hands-URL: er har genererats och kan ta emot Live-strömmar. Faktureringen är nu aktiv. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering.|
|**Stoppas**| Direkt sändningen stoppas och resurserna etableras. Ingen fakturering sker i det här tillfälliga läget. Uppdateringar eller strömmande tillåts inte i det här läget.|
|**Rader**| Direktsändningen tas bort. Ingen fakturering sker i det här tillfälliga läget. Uppdateringar eller strömmande tillåts inte i det här läget.|

## <a name="next-steps"></a>Nästa steg

- [Översikt över direkt uppspelning](live-streaming-overview.md)
- [Själv studie kurs om Live-direktuppspelning](stream-live-tutorial-with-api.md)
