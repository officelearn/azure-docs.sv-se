---
title: Använd Azure Media Clipper i portalen | Microsoft Docs
description: Skapa klipp med hjälp av Azure Media Clipper från Azure Portal
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465855"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Skapa klipp med Azure Media Clipper i portalen  

Du kan använda Azure Media Clipper i portalen för att skapa klipp från tillgångar i ditt media services-konton. Kom igång genom att gå till ditt media services-konto i portalen. Välj sedan den **underklipp** fliken.

På den **underklipp** fliken verket kan du börja skapa klipp. I portalen laddas Clipper med enkel bithastighet MP4s, flera bithastigheter MP4s och direktsända strömningar som publiceras med en giltig positionerare för direktuppspelning. Avpublicera tillgångar har inte lästs in.

Clipper är för närvarande i offentlig förhandsversion. För att komma åt Clipper i Azure-portalen, gå till den här [offentlig förhandsgranskningssidan](https://portal.azure.com/?feature.subclipper=true).

Följande bild illustrerar Clipper landningssidan i media services-kontot: ![Azure Media Clipper i Azure-portalen](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Producera klipp
Om du vill skapa ett klipp, dra och släpp en tillgång på clip-gränssnittet. Om mark-tiderna är känt ska ange du dem manuellt inloggningsgränssnittet. Annars uppspelning tillgången eller dra uppspelningsposition hitta önskad mark-modulen och slut tid. Om en markering i eller markera ut tid anges klippet börjar från början eller fortsätter i slutet av indata tillgången, respektive.

Använd knapparna ramens vanlig/GOP-vidarebefordring eller ram-bakåt/GOP-bakåt för att navigera med ram-Precision/GOP-noggrannhet. Dra och släpp flera tillgångar i clip-gränssnittet från panelen för val av tillgången för urklippet mot flera tillgångar. Du kan välja och ordna om tillgångar i gränssnittet på annat sätt. Panelen för val av tillgången ger tillgångens varaktighet, typ och upplösning metadata för varje resurs. Överväg att Källidentifiering för varje indatafil när sammanfoga flera resurser tillsammans. Om käll-lösningar skiljer sig åt upscaled lägre upplösning indata för att uppfylla av lösningen på den högsta upplösning tillgången. Spelar upp från markeringen gånger att Förhandsgranska utdata från jobbet urklippet, Välj förhandsgranskningsknappen och klippet.

## <a name="producing-dynamic-manifest-filters"></a>Producera dynamiska manifestfilter
[Dynamiska manifestfilter](https://azure.microsoft.com/blog/dynamic-manifest/) beskriver en uppsättning regler baserat på manifest attribut och tillgången tidslinje. Reglerna avgör hur din slutpunkt för direktuppspelning manipulerar listan utdata (manifest). Filtret kan användas för att ändra vilka segment strömmas för uppspelning. De filter som produceras av Clipper är lokala filter och är specifika för tillgången källa. Till skillnad från renderade klipp filter är inte nya tillgångar och kräver inte ett kodningsjobb att producera. De snabbt kan skapas den [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), men de är bara GOP-exakta. Tillgångar som är kodade för direktuppspelning har vanligtvis en GOP storlek på två sekunder.

Skapa ett dynamiskt manifestfilter, gå till den **tillgångar** fliken och markera den önskade tillgången. Välj den **underklipp** knappen för den översta menyn. Välj dynamiskt manifestfilter som urklippet-läge från menyn Avancerade inställningar. Sedan kan du följa samma process för att skapa en renderade clip för att skapa filtret. Filter kan endast tillverkas av ett enskilt objekt.

Följande bild illustrerar Clipper i dynamiska manifest filterläget i Azure portal: ![Azure Media Clipper i dynamiska manifest filterläget i Azure-portalen](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Skicka urklippningsjobb
När du har skrivit klippet väljer du knappen Skicka jobb för initiera motsvarande urklippet jobbet eller dynamiska manifest-anrop.

## <a name="next-steps"></a>Nästa steg
Kom igång med Azure Media Clipper, läsa den [komma igång](media-services-azure-media-clipper-getting-started.md) nedan för information om hur du distribuerar widgeten.
