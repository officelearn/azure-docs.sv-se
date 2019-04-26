---
title: Azure Media Services Live-händelse och ett moln-DVR | Microsoft Docs
description: Den här artikeln förklarar vad Live utdata är och hur du använder en molnbaserad DVR.
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322343"
---
# <a name="using-a-cloud-dvr"></a>Använda en moln-DVR

En [Live utdata](https://docs.microsoft.com/rest/api/media/liveoutputs) låter dig kontrollera egenskaperna för den utgående direktsända dataströmmen, till exempel hur mycket av strömmen registreras (t.ex, kapaciteten för moln-DVR) och om huruvida användarna kan börjar titta på live stream. Förhållandet mellan en **direktsänd händelse** och dess **Live utdata**s liknar traditionella TV-sändning, där en kanal (**direktsänd händelse**) representerar en konstant strömma video och en inspelning (**Live utdata**) är begränsad till en viss tidpunkt-segment (till exempel kvällar nyheter från 18:30:00 till 19:00:00). Du kan spela TV med hjälp av en Digital Video Recorder (DVR) – den motsvarande funktionen i Live-händelser hanteras via egenskapen ArchiveWindowLength. Det är en ISO 8601-timespan varaktighet (till exempel PTHH:MM:SS) som anger kapaciteten för DVR och kan anges från minst 3 minuter till högst 25 timmar.

## <a name="live-output"></a>Live-utdata

Den **ArchiveWindowLength** värdet anger hur långt tillbaka i gång visningsprogrammet kan söka från den aktuella direktsända positionen.  **ArchiveWindowLength** avgör också hur länge klientmanifesten kan växa.

Anta att du streaming football spel och den har en **ArchiveWindowLength** bara 30 minuter. Ett visningsprogram som startar titta på evenemanget 45 minuter efter spelet igång kan försöka tillbaka högst 15 minuters märket. Din **Live utdata**s för spelet fortsätter tills den **direktsänd händelse** stoppas, men innehåll som faller utanför **ArchiveWindowLength** ignoreras kontinuerligt från lagring och kan inte återställas. I det här exemplet videon mellan början av händelsen och 15 minuters mark skulle som har tagits bort från din DVR och behållare i blob storage för den [tillgången](https://docs.microsoft.com/rest/api/media/assets). Arkivet kan inte återställas och tas bort från behållaren i Azure blob storage.

Varje **Live utdata** är associerad med en **tillgången**, som används för att spela in video i den associerade Azure blob storage-behållaren. Om du vill publicera Live-utdata, måste du skapa en **Strömningspositionerare** för som **tillgången**. När du har skapat en [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators), du kan skapa en strömnings-URL som du kan tillhandahålla till dina användare.

En **direktsänd händelse** har stöd för upp till tre som körs samtidigt **Live utdata**s så att du kan skapa högst 3 inspelningar/Arkiv från en direktsänd dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Anta att du behöver att sända en 24 x 7 live linjär feed och skapa ”inspelningar” olika program per dag för att erbjuda kunder som innehåll på begäran för catch-up visning. Det här scenariot kan du först skapa en primära utdata Live med en kort arkivfönster 1 timme eller mindre – det här är den primära direktsända dataströmmen som användarna skulle lyssna på. Du skapar en **Strömningspositionerare** för den här **Live utdata** och publicera den till ditt program eller en webbplats som ”Live”-feed. Medan den **direktsänd händelse** är kör kan du programmässigt kan skapa en andra samtidiga **Live utdata** i början av ett program (eller 5 minuter för tidigt att tillhandahålla vissa handtag för att trimma senare). Den här sekunden **Live utdata** kan tas bort 5 minuter när programmet har avslutats. Med den här sekunden **tillgången**, du kan skapa en ny **Strömningspositionerare** att publicera det här programmet som en på begäran-tillgång i katalogen för ditt program. Du kan upprepa den här processen flera gånger för andra programgränser eller viktiga funktioner som du vill dela som videor på begäran när ”Live” flödet från först **Live utdata** fortsätter att sända linjär feeden. 

> [!NOTE]
> **Live utdata**s börjar vid skapandet och avbryts när tas bort. När du tar bort den **Live utdata**, du inte tar bort den underliggande **tillgången** och innehåll i tillgången. 
>
> Om du har publicerat den **Live utdata** tillgången med hjälp av en **Strömningspositionerare**, **direktsänd händelse** (upp till DVR fönstret längd) fortsätter att vara synliga tills **Strömningspositionerare**'s slutar att gälla eller tas bort som inträffar först.

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)

