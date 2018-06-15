---
title: Uppgifter som tillåts i olika tillstånd eller status i BizTalk-tjänst | Microsoft Docs
description: 'Åtgärder/operationer som tillåts i olika MABS status: stoppa, starta, starta om, pausa, återuppta, ta bort, skala, uppdatera konfigurationen och säkerhetskopiera upp'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 05470e75fc7b46603c8fce3a98c66ac6a24758a8
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102750"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Vad du kan och inte kan göra med BizTalk Service-tillstånd

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Beroende på aktuell status för BizTalk-tjänst finns som du kan eller inte kan utföra på BizTalk-tjänst.

Exempelvis kan du etablera en ny BizTalk-tjänst. När den har slutförts, BizTalk-tjänst finns i `active` tillstånd. I aktivt läge kan du stoppa, pausa och ta bort BizTalk-tjänst. Om du stoppar tjänsten BizTalk och stoppa misslyckas och BizTalk-tjänst går sedan till en `StopFailed` tillstånd. I den `StopFailed` tillståndet kan du starta om BizTalk-tjänst. Om du försöker en åtgärd som inte tillåts, t.ex. återuppta, inträffar följande fel:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Visa möjliga tillstånd

I följande tabeller beskrivs åtgärder eller de åtgärder som kan göras när BizTalk Service är i ett visst tillstånd. En ✔ innebär åtgärden tillåts i det aktuella tillståndet. En tom post innebär inte att utföra åtgärden i det aktuella tillståndet.

| Tillstånd för tjänsten | Start | Stoppa | Starta om | Pausa | Återuppta | Ta bort | Skala | Uppdatering <br/> Konfiguration | Säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Active |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Disabled |  |  |  |  |  | ✔ | |  |  | 
| avbruten |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Stoppad | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Uppdatering av tjänsten misslyckades |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Se även
* [Vad du kan göra på instrumentpanelen, övervaka och skala flikar i BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Vad du får med utvecklare, Basic, Standard och Premium-versioner i BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Säkerhetskopiera och återställa en BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Begränsning förklaras i BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Hämta Service Bus och åtkomstkontroll Utfärdarens namn och utfärdaren nyckelvärdena för BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

