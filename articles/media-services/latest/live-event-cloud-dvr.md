---
title: Azure Media Services LiveEvent och molnbaserade DVR | Microsoft Docs
description: Den här artikeln förklarar vad LiveOutput är och hur du använder en molnbaserad DVR.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642293"
---
# <a name="using-a-cloud-dvr"></a>Med hjälp av ett moln-DVR

En [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) låter dig kontrollera egenskaperna för den utgående direktsända dataströmmen, till exempel hur mycket av strömmen registreras (t.ex, kapaciteten för moln-DVR) och om huruvida användarna kan börjar titta på live stream. Förhållandet mellan en **LiveEvent** och dess **LiveOutput**s relation liknar traditionella TV-sändning, där en kanal (**LiveEvent**) representerar en konstant ström av video och en inspelning (**LiveOutput**) är begränsad till en viss tidpunkt-segment (till exempel kvällar nyheter från 18:30:00 till 19:00:00). Du kan spela TV med hjälp av en Digital Video Recorder (DVR) – den motsvarande funktionen i LiveEvents hanteras via egenskapen ArchiveWindowLength. Det är en ISO 8601-timespan varaktighet (till exempel PTHH:MM:SS) som anger kapaciteten för DVR och kan anges från minst 3 minuter till högst 25 timmar.

## <a name="liveoutput"></a>LiveOutput

Den **ArchiveWindowLength** värdet anger hur långt tillbaka i gång visningsprogrammet kan söka från den aktuella direktsända positionen.  **ArchiveWindowLength** avgör också hur länge klientmanifesten kan växa.

Anta att du streaming football spel och den har en **ArchiveWindowLength** bara 30 minuter. Ett visningsprogram som startar titta på evenemanget 45 minuter efter spelet igång kan försöka tillbaka högst 15 minuters märket. Din **LiveOutput**s för spelet fortsätter tills den **LiveEvent** stoppas, men innehåll som faller utanför **ArchiveWindowLength** ignoreras kontinuerligt från lagring och kan inte återställas. I det här exemplet videon mellan början av händelsen och 15 minuters mark skulle som har tagits bort från din DVR och behållare i blob storage för den [tillgången](https://docs.microsoft.com/rest/api/media/assets). Arkivet kan inte återställas och tas bort från behållaren i Azure blob storage.

Varje **LiveOutput** är associerad med en **tillgången**, som används för att spela in video i den associerade Azure blob storage-behållaren. Om du vill publicera LiveOutput, måste du skapa en **StreamingLocator** för som **tillgången**. När du har skapat en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), du kan skapa en strömnings-URL som du kan tillhandahålla till dina användare.

En **LiveEvent** har stöd för upp till tre som körs samtidigt **LiveOutput**s så att du kan skapa högst 3 inspelningar/Arkiv från en direktsänd dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Anta att du behöver att sända en 24 x 7 live linjär feed och skapa ”inspelningar” olika program per dag för att erbjuda kunder som innehåll på begäran för catch-up visning. Det här scenariot kan du först skapa en primär LiveOutput med en kort arkivfönster 1 timme eller mindre – det här är den primära direktsända dataströmmen som användarna skulle lyssna på. Du skapar en **StreamingLocator** för den här **LiveOutput** och publicera den till ditt program eller en webbplats som ”Live”-feed. Medan den **LiveEvent** är kör kan du programmässigt kan skapa en andra samtidiga **LiveOutput** i början av ett program (eller 5 minuter för tidigt att tillhandahålla vissa handtag för att trimma senare). Den här sekunden **LiveOutput** kan tas bort 5 minuter när programmet har avslutats. Med den här sekunden **tillgången**, du kan skapa en ny **StreamingLocator** att publicera det här programmet som en på begäran-tillgång i katalogen för ditt program. Du kan upprepa den här processen flera gånger för andra programgränser eller viktiga funktioner som du vill dela som videor på begäran när ”Live” flödet från först **LiveOutput** fortsätter att sända linjär feeden. 

> [!NOTE]
> **LiveOutput**s börjar vid skapandet och avbryts när tas bort. När du tar bort den **LiveOutput**, du inte tar bort den underliggande **tillgången** och innehåll i tillgången.  

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)

