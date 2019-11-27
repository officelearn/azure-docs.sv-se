---
title: Använd tids växling och Live-utdata för att skapa video uppspelning på begäran
titleSuffix: Azure Media Services
description: Lär dig hur du använder Time-Shift och Live-utdata för att registrera Live-strömmar och skapa uppspelning på begäran.
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
ms.openlocfilehash: acba251a57f39c07d690d0c55665b8914feaf06c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186234"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Använd tids växling och Live-utdata för att skapa video uppspelning på begäran

I Azure Media Services är ett [Live-utmatnings](https://docs.microsoft.com/rest/api/media/liveoutputs) objekt som en digital videobandspelare som fångar upp och registrerar din Live-dataström till en till gång i ditt Media Services-konto. Det inspelade innehållet sparas i den behållare som definieras av [resurs resursen (](https://docs.microsoft.com/rest/api/media/assets) behållaren är i det Azure Storage konto som är kopplat till ditt konto). Med Live-utdata kan du också styra vissa egenskaper för utgående Live-dataström, t. ex. hur mycket av data strömmen som behålls i Arkiv registreringen (till exempel kapaciteten för moln-DVR) eller när läsarna kan börja titta på den aktiva strömmen. Arkivet på disken är ett cirkulärt Arkiv "fönster" som endast innehåller den mängd innehåll som anges i egenskapen **archiveWindowLength** för Live-utdata. Innehåll som faller utanför det här fönstret tas automatiskt bort från lagrings behållaren och går inte att återskapa. ArchiveWindowLength-värdet representerar en varaktighet för ISO-8601 TimeSpan (till exempel PTHH: MM: SS), som anger kapaciteten för DVR. Värdet kan anges från minst tre minuter till högst 25 timmar.

Förhållandet mellan en Live-händelse och dess direktsända utdata liknar traditionell TV-sändning, i så att en kanal (live event) representerar en konstant ström med video och en inspelning (direktsända utdata) är begränsad till ett särskilt tids segment (till exempel kvälls nyheter från 6:17.30 till 7:12:00). När strömmen flödar in i Live-evenemanget kan du starta den strömmande händelsen genom att skapa en till gång, en Live-utgång och en strömmande positionerare. Live-utdata kommer att arkivera strömmen och göra den tillgänglig för användare via [slut punkten för direkt uppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints). Du kan skapa flera Live-utdata (högst tre) för en Live-händelse med olika arkiv längder och inställningar. Information om Live streaming-arbetsflödet finns i avsnittet [allmänna steg](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Använda en DVR under en händelse

I det här avsnittet beskrivs hur du använder en DVR under en händelse för att kontrol lera vilka delar av data strömmen som är tillgängliga för "spola tillbaka".

`archiveWindowLength` svärdet avgör hur långt tillbaka i tiden ett visnings program kan gå från den nuvarande aktiva positionen. `archiveWindowLength` svärdet avgör också hur länge klient manifesten kan växa.

Anta att du spelar upp ett fotbolls spel och att det har en `ArchiveWindowLength` på bara 30 minuter. Ett visnings program som börjar titta på evenemanget 45 minuter efter att spelet har startats kan gå tillbaka till maximalt 15-minuters tecken. Dina direktsända utdata för spelet fortsätter tills direkt sändningen stoppas. Innehåll som faller utanför archiveWindowLength tas kontinuerligt bort från lagrings utrymmet och går inte att återvinna. I det här exemplet skulle videon mellan början av händelsen och 15-minuters märket ha rensats från din DVR och från behållaren i blob-lagring för till gången. Arkivet är inte återställnings Bart och tas bort från behållaren i Azure Blob Storage.

En Live-händelse har stöd för upp till tre pågående Live-utdata (du kan skapa högst 3 inspelningar/arkiv från en Live-dataström samtidigt). Med det här stödet kan du publicera och arkivera olika delar av en händelse vid behov. Anta att du behöver sända en Live linjär matning dygnet runt och skapa "inspelningar" av de olika programmen under dagen för att erbjuda kunder som innehåll på begäran för att komma igång. I det här scenariot skapar du först en primär Live-utmatning med ett kort Arkiv-fönster på 1 timme eller mindre – det här är den primära Live-strömmen som dina användare skulle justera in i. Du skulle skapa en strömmande positionerare för den här Live-utdatan och publicera den på din app eller webbplats som "Live"-feed. När direkt sändnings händelsen körs kan du program mässigt skapa en andra samtidig Live-utdata i början av ett program (eller 5 minuter tidigt för att ge några handtag för att beskära senare). Den andra Live-utdatan kan tas bort 5 minuter efter att programmet har avslut ATS. Med den här andra till gången kan du skapa en ny plats för strömning för att publicera programmet som en till gång på begäran i appens katalog. Du kan upprepa den här processen flera gånger för andra program gränser eller markeringar som du vill dela som videor på begäran, allt medan Live-flödet från det första Live-resultatet fortsätter att sända det linjära flödet.

## <a name="creating-an-archive-for-on-demand-playback"></a>Skapa ett Arkiv för uppspelning på begäran

Till gången som Live-utdata arkiveras till blir automatiskt en till gång på begäran när Live-utdata tas bort. Du måste ta bort alla Live-utdata innan en Live-händelse kan stoppas. Du kan använda en valfri flagga [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) för att automatiskt ta bort Live-utdata vid stopp.

Även efter att du har stoppat och tagit bort händelsen kan användarna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort till gången. En till gång bör inte tas bort om den används av en händelse. händelsen måste tas bort först.

Om du har publicerat till gången till din Live-utmatning med hjälp av en strömmande positionerare, fortsätter Live-händelsen (upp till fönstret för DVR-fönster) att visas tills den går ut eller ta bort streaming-adressen, beroende på vilket som kommer först.

Mer information finns i:

- [Översikt över direkt uppspelning](live-streaming-overview.md)
- [Själv studie kurs om Live-direktuppspelning](stream-live-tutorial-with-api.md)

> [!NOTE]
> När du tar bort Live-utdata tar du inte bort den underliggande till gången och innehållet i till gången.

## <a name="next-steps"></a>Nästa steg

* [Klipp dina videor](subclip-video-rest-howto.md).
* [Definiera filter för dina till gångar](filters-dynamic-manifest-rest-howto.md).
