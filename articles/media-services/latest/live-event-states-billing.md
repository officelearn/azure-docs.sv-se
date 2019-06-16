---
title: LiveEvent tillstånd och fakturering i Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services LiveEvent tillstånd och fakturering.
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2907b5be7f8d5fda3d510484179e80b065ab64b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074900"
---
# <a name="live-event-states-and-billing"></a>Live-händelse tillstånd och fakturering

I Azure Media Services, en direktsänd händelse börjar fakturering när dess status övergår till **kör**. Du måste stoppa Live-händelsen för att stoppa Live-händelse från fakturering.

När **LiveEventEncodingType** på din [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) är inställd på Standard- eller Premium1080p, Media Services automatiskt stängs av en Live-händelse som fortfarande är i den **kör** tillstånd 12 timmar efter inkommande flödet går förlorad och det finns inga **Live utdata**datorer som körs. Men du kommer fortfarande att debiteras för tiden Live-händelsen som fanns i den **kör** tillstånd.

## <a name="states"></a>Tillstånd

Live-händelsen kan vara i något av följande tillstånd.

|Status|Beskrivning|
|---|---|
|**Stoppad**| Det här är starttillståndet för Live-händelsen när du har skapat (såvida inte autostart har angetts till true.) Ingen debitering sker i det här tillståndet. Egenskaper för Live-händelse kan uppdateras men strömning är inte tillåtet i det här tillståndet.|
|**Startar**| Live-händelsen startas och resurser allokeras. Ingen debitering sker i det här tillståndet. Uppdateringar eller strömning tillåts inte i det här tillståndet. Om ett fel inträffar återgår Live-händelse till tillståndet stoppad.|
|**Körs**| Live-händelsen resurser som har allokerats, mata in och förhandsgranska URL: er har genererats och är det kan ta emot direktsända strömmar. Fakturering är nu aktiv. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering.|
|**Stoppar**| Live-händelsen stoppas och resurser som ska enhetstiden. Ingen debitering sker i den här tillfälligt tillstånd. Uppdateringar eller strömning tillåts inte i det här tillståndet.|
|**Tar bort**| Live-händelse tas bort. Ingen debitering sker i den här tillfälligt tillstånd. Uppdateringar eller strömning tillåts inte i det här tillståndet.|

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
