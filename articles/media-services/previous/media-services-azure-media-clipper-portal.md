---
title: Använd Azure Media Clipper i portalen | Microsoft Docs
description: Skapa klipp med hjälp av Azure Media Clipper från Azure Portal
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788223"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Skapa klipp med Azure Media Clipper i portalen
Du kan använda Azure Media Clipper i portalen för att skapa klipp från tillgångar i ditt media services-konton. Kom igång genom att gå till media services-konto i portalen. Välj sedan den **underklipp** fliken.

På den **underklipp** fliken verket kan du börja skriva klipp. I portalen laddas Clipper enkel bithastighet MP4s, multibithastighet MP4s och live Arkiv som publiceras med en giltig strömningslokaliserare. Avpublicera tillgångar har inte lästs in.

Clipper är för närvarande i förhandsversion. Om du vill komma åt Clipper i Azure portal, gå till den här [förhandsversion sidan](https://portal.azure.com/?feature.subclipper=true).

Följande bild illustrerar Clipper landningssida i media services-konto: ![Azure Media Clipper i Azure-portalen](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Producerar klipp
Dra och släpp en tillgång på clip-gränssnittet för att skapa en bild. Om markering-gånger är kända, kan du manuellt ange dem i gränssnittet. Annars uppspelning tillgången eller dra spelhuvudet om du vill hitta önskad markering in och slut tid. Om en markering i eller markera ut tid inte tillhandahålls, klippet börjar från början eller fortsätter till slutet av inkommande tillgången respektive.

Använd knapparna ram framåt/GOP-vidarebefordring eller ram-bakåt/GOP-bakåt för att navigera med ram-Precision/GOP-noggrannhet. För urklippet mot flera tillgångar dra och släpp flera resurser i gränssnittet clip från panelen tillgången val. Du kan välja och ordna om tillgångar i gränssnittet till önskad ordning. Panelen tillgången markeringen innehåller tillgångsinformation varaktighet, typ och upplösning metadata för varje tillgång. Tänk Källidentifiering för varje indatafilen när sammanbinda flera resurser tillsammans. Om käll-lösningar skiljer sig åt upscaled lägre upplösning indata för att uppfylla lösning av högsta upplösning tillgången. Välj knappen förhandsgranskning och klippet spelas upp markeringen gånger om du vill förhandsgranska utdata för urklippet jobb.

## <a name="producing-dynamic-manifest-filters"></a>Producerar dynamiska manifestet filter
[Dynamiska manifestet filter](https://azure.microsoft.com/blog/dynamic-manifest/) beskriver en uppsättning regler baserat på manifestet attribut och tidslinje tillgångsinformation. Reglerna avgör hur din strömningsslutpunkt manipulerar listan utdata (manifest). Filtret kan användas för att ändra vilka segment strömmas för uppspelning. Filter som produceras av Clipper är lokala filter och är specifika för tillgången källa. Till skillnad från renderade klipp filter är inte nya tillgångar och kräver inte ett kodningsjobb att skapa. De kan skapas snabbt den [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), de är dock endast GOP korrekt. Tillgångar kodad för strömning har vanligtvis en GOP storlek på två sekunderna.

Om du vill skapa ett manifest dynamiska filter, navigera till den **tillgångar** fliken och markera önskade tillgången. Välj den **underklipp** knapp för den översta menyn. Välj dynamisk manifestet filter som urklippet-läge från menyn Avancerade inställningar. Sedan kan du följa samma process för att skapa ett renderade klipp för att skapa filtret. Filter kan endast genereras från ett enskilt objekt.

Följande bild illustrerar Clipper i dynamiska manifestet Filterläge i Azure-portalen: ![Azure Media Clipper i dynamiska manifestet Filterläge i Azure-portalen](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Skickar urklippet jobb
När du har skrivit klippet Välj knappen skicka jobbet för initiera motsvarande urklippet jobb eller dynamiska manifestet anrop.

## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med Azure Media Clipper, läsa den [komma igång](media-services-azure-media-clipper-getting-started.md) artikeln för information om hur du distribuerar widgeten.