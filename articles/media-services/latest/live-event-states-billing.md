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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585583"
---
# <a name="liveevent-states-and-billing"></a>LiveEvent tillstånd och fakturering

I Azure Media Services, börjar en LiveEvent fakturering när dess status övergår till **kör**. Du måste stoppa LiveEvent om du vill stoppa LiveEvent från fakturering.

När **LiveEventEncodingType** på din [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) till Standard (grundläggande), Media Services automatiskt stängs av alla LiveEvent som är fortfarande i den **kör** tillstånd 12 timmar När indata feeden går förlorad och det finns inga **LiveOutput**datorer som körs. Men du kommer fortfarande att debiteras för den tid som LiveEvent var i den **kör** tillstånd.

## <a name="states"></a>Tillstånd

LiveEvent kan vara i något av följande tillstånd.

|Status|Beskrivning|
|---|---|
|**Stoppad**| Det här är starttillståndet för LiveEvent när du har skapat (såvida inte autostart har angetts till true.) Ingen debitering sker i det här tillståndet. I det här tillståndet LiveEvent egenskaper kan uppdateras, men strömning är inte tillåtet.|
|**Startar**| LiveEvent startas och resurser allokeras. Ingen debitering sker i det här tillståndet. Uppdateringar eller strömning tillåts inte i det här tillståndet. Om ett fel inträffar återgår LiveEvent till tillståndet stoppad.|
|**Kör**| LiveEvent resurser som har allokerats, mata in och förhandsgranska URL: er har genererats och är det kan ta emot direktsända strömmar. Fakturering är nu aktiv. Du måste explicit anropa Stop på LiveEvent resursen för att stoppa ytterligare fakturering.|
|**Stoppar**| LiveEvent stoppas och resurser som ska enhetstiden. Ingen debitering sker i den här tillfälligt tillstånd. Uppdateringar eller strömning tillåts inte i det här tillståndet.|
|**Tar bort**| LiveEvent tas bort. Ingen debitering sker i den här tillfälligt tillstånd. Uppdateringar eller strömning tillåts inte i det här tillståndet.|

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
