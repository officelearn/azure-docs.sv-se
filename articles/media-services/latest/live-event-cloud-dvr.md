---
title: Använda tidsväxling och liveutdata för att skapa videouppspelning på begäran
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs hur du använder tidsväxling och liveutdata för att spela in liveströmmar och skapa uppspelning på begäran.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899785"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Använda tidsväxling och liveutdata för att skapa videouppspelning på begäran

I Azure Media Services är ett [Live Output-objekt](https://docs.microsoft.com/rest/api/media/liveoutputs) som en digital videobandspelare som fångar och spelar in din livestream till en tillgång i ditt Media Services-konto. Det inspelade innehållet sparas i behållaren som definieras av [tillgångsresursen](https://docs.microsoft.com/rest/api/media/assets) (behållaren finns i Azure Storage-kontot som är kopplat till ditt konto). Live Output kan du också styra vissa egenskaper för den utgående livestreamen, till exempel hur mycket av strömmen som sparas i arkivinspelningen (till exempel kapaciteten för moln-DVR) eller när tittarna kan börja titta på livestreamen. Arkivet på disken är ett cirkulärt arkiv "fönster" som bara innehåller mängden innehåll som anges i **arkivetWindowLength** egenskapen live output. Innehåll som faller utanför det här fönstret ignoreras automatiskt från lagringsbehållaren och kan inte återställas. ArkivWindowLength-värdet representerar en ISO-8601-tidsspannatidslängd (till exempel PTHH:MM:SS), som anger dvr-värdets kapacitet. Värdet kan ställas in från minst tre minuter till högst 25 timmar.

Förhållandet mellan en livehändelse och dess liveutdata liknar traditionell TV-sändning, eftersom en kanal (Live Event) representerar en konstant ström av video och en inspelning (Live Output) är begränsad till ett visst tidssegment (till exempel kvällsnyheter från 18:30 till 19:00). När du har strömmen flödar in i Live Event kan du börja direktuppspelningshändelsen genom att skapa en tillgång, Live Output och streaming locator. Live Output arkiverar strömmen och gör den tillgänglig för tittare via [slutpunkten för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints). Du kan skapa flera liveutdata (upp till högst tre) på en livehändelse med olika arkivlängder och inställningar. Information om arbetsflödet för direktuppspelning finns i avsnittet [allmänna steg.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Använda en DVR under en händelse

I det här avsnittet beskrivs hur du använder en DVR under en händelse för att styra vilka delar av strömmen som är tillgängliga för "spola tillbaka".

Värdet `archiveWindowLength` avgör hur långt tillbaka i tiden en tittare kan gå från den aktuella live-positionen. Värdet `archiveWindowLength` bestämmer också hur länge klientmanifesten kan växa.

Anta att du streamar en fotbollsmatch, `ArchiveWindowLength` och den har bara 30 minuter. En tittare som börjar titta på ditt evenemang 45 minuter efter att spelet började kan söka tillbaka till högst 15-minuters märket. Dina liveutdata för spelet fortsätter tills livehändelsen stoppas. Innehåll som faller utanför arkivetWindowLength ignoreras kontinuerligt från lagring och kan inte återställas. I det här exemplet skulle videon mellan början av händelsen och 15-minutersmarkeringen ha rensats från din DVR och från behållaren i blob-lagring för tillgången. Arkivet kan inte återställas och tas bort från behållaren i Azure blob storage.

En Live Event stöder upp till tre samtidigt köra Live Outputs (du kan skapa högst 3 inspelningar / arkiv från en livestream samtidigt). Med det här stödet kan du publicera och arkivera olika delar av en händelse efter behov. Anta att du behöver sända en 24x7 live linjär feed, och skapa "inspelningar" av de olika programmen under hela dagen för att erbjuda kunderna som on-demand innehåll för catch-up visning. I det här scenariot skapar du först en primär Live Output med ett kort arkivfönster på 1 timme eller mindre – det här är den primära livestreamen som tittarna skulle ställa in. Du skulle skapa en streaming locator för denna Live Output och publicera den på din app eller webbplats som "Live" foder. Medan Live Event körs kan du programmässigt skapa en andra samtidig Live Output i början av ett program (eller 5 minuter för tidigt för att ge några handtag för att trimma senare). Denna andra Live Output kan tas bort 5 minuter efter att programmet avslutats. Med den här andra tillgången kan du skapa en ny streamingpositionerare för att publicera det här programmet som en on-demand-tillgång i appens katalog. Du kan upprepa den här processen flera gånger för andra programgränser eller höjdpunkter som du vill dela som videor på begäran, allt medan "Live"-flödet från den första Live Output fortsätter att sända den linjära feeden.

## <a name="creating-an-archive-for-on-demand-playback"></a>Skapa ett arkiv för uppspelning på begäran

Den tillgång som Live Output arkiverar för att automatiskt blir en on-demand-tillgång när Live Output tas bort. Du måste ta bort alla liveutdata innan en livehändelse kan stoppas. Du kan använda en valfri flagga [som tar bortOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) för att automatiskt ta bort liveutdata vid stopp.

Även efter att du har stannat och tagit bort händelsen kan användarna strömma det arkiverade innehållet som en video på begäran, så länge du inte tar bort tillgången. En tillgång bör inte tas bort om den används av en händelse. Händelsen måste tas bort först.

Om du har publicerat tillgången för din Live Output med hjälp av en streaming locator, livehändelsen (upp till DVR-fönstrets längd) kommer att fortsätta att vara synlig tills streaming locator löper ut eller borttagning, beroende på vilket som inträffar först.

Mer information finns i:

- [Översikt över livestreaming](live-streaming-overview.md)
- [Självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)

> [!NOTE]
> När du tar bort Live Output tar du inte bort den underliggande tillgången och innehållet i tillgången.

## <a name="next-steps"></a>Nästa steg

* [Subclip dina videor](subclip-video-rest-howto.md).
* [Definiera filter för dina tillgångar](filters-dynamic-manifest-rest-howto.md).
