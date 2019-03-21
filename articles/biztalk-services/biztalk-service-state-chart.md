---
title: Uppgifter som tillåts i olika tillstånd eller statusar i BizTalk Services | Microsoft Docs
description: 'Åtgärder/åtgärder som tillåts i olika MABS-status: stoppa, starta, starta om, pausa, återuppta, ta bort, skala och uppdatera konfiguration och säkerhetskopiering upp'
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
ms.openlocfilehash: 71463b523f0afdf63a7c89ff2b3137ae8d091b3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086708"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Vad du kan och inte kan göra med BizTalk Service-tillstånd

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Det finns åtgärder som du kan eller kan inte utföra på BizTalk-tjänst beroende på det aktuella tillståndet för BizTalk-tjänsten.

Exempelvis kan etablera du en ny BizTalk-tjänst. När processen är klar, BizTalk-tjänsten är i `active` tillstånd. Aktiv, kan du stoppa, pausa och ta bort BizTalk-tjänsten. Om du stoppar BizTalk-tjänsten och stoppa misslyckas och BizTalk-tjänst går sedan till en `StopFailed` tillstånd. I den `StopFailed` tillstånd, kan du starta om BizTalk-tjänsten. Om du försöker en åtgärd som inte är tillåtet, resume t.ex., inträffar följande fel:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Visa möjliga tillstånd

I tabellerna nedan listas åtgärder eller de åtgärder som kan göras när BizTalk Service finns i ett visst tillstånd. En ✔ innebär att åtgärden är tillåten i det aktuella tillståndet. Lämnas tomt innebär att det går inte att utföra åtgärden i det aktuella tillståndet.

| Tjänsttillstånd | Start | Stoppa | Starta om | Inaktivera | Återuppta | Ta bort | Skala | Uppdatering <br/> Konfiguration | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Active |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Disabled |  |  |  |  |  | ✔ | |  |  | 
| Tillfälligt avbruten |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Stoppad | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Tjänstuppdateringen misslyckades |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |



## <a name="see-also"></a>Se även
* [Vad du kan göra på flikarna instrumentpanel, Övervakare och skalning i BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Vad du får med Developer, Basic, Standard och Premium-versionerna i BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Hur du säkerhetskopierar och återställer en BizTalk Service](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Begränsning förklaras i BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Hämta Service Bus och Access Control Utfärdarens namn och nyckelvärdena för BizTalk Service](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](https://go.microsoft.com/fwlink/p/?LinkID=302335)

