---
title: Använda Azure Media Clipper i portalen | Microsoft Docs
description: Skapa klipp med Azure Media Clipper från Azure Portal
services: media-services
keywords: klipp; subclip; encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685002"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Skapa klipp med Azure Media Clipper i portalen  

Du kan använda Azure Media Clipper i portalen för att skapa klipp från till gångar i dina Media Services-konton. Kom igång genom att gå till ditt Media Services-konto i portalen. Välj sedan fliken under **klipp** .

På fliken under **klipp** kan du börja skapa klipp. I portalen laddar Clipper en enbits-hastigheter, hastigheter med flera bit hastigheter och Live-Arkiv som publiceras med en giltig strömmande positionerare. Opublicerade till gångar har inte lästs in.

Clipper är för närvarande en offentlig för hands version. Om du vill komma åt Clipper i Azure Portal går du till den här [offentliga förhands gransknings sidan](https://portal.azure.com/?feature.subclipper=true).

Följande bild illustrerar Clipper-landnings sidan i ditt Media Services-konto: ![Azure Media Clipper i Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Genererar klipp
Om du vill skapa ett klipp drar du och släpper en till gång till Clip-gränssnittet. Om markerings tiderna är kända kan du ange dem manuellt i gränssnittet. Annars kan du spela upp till gången eller dra spel huvudet för att hitta önskad markerings-och markerings tid. Om ett märke eller en markerings slut tid inte anges, börjar klippet från början eller fortsätter till slutet av indata-till gången.

Använd knapparna Frame-Forward/GOP-forward eller Frame-baklänges/GOP-baklänges för att navigera med ram-exakthet/GOP-exakthet. Dra och släpp flera till gångar i Clip-gränssnittet från panelen till gångs urval för att klippa ut mot flera till gångar. Du kan välja och ändra ordning till till gångar i gränssnittet till önskad ordning. Panelen till gångs val innehåller metadata för till gång, typ och upplösning för varje till gång. När du sammanfogar flera resurser tillsammans bör du tänka på käll upplösningen för varje indatafil. Om käll upplösningen skiljer sig, expanderas lägre upplösnings inläsningar för att uppfylla upplösningen för den högsta matchnings till gången. Om du vill förhandsgranska resultatet av urklipps jobbet väljer du knappen Förhandsgranska och klippet spelas upp från den markerade markerings tiden.

## <a name="producing-dynamic-manifest-filters"></a>Genererar dynamiska manifest filter
[Dynamiska manifest filter](https://azure.microsoft.com/blog/dynamic-manifest/) beskriver en uppsättning regler som baseras på manifest-attribut och till gångs tids linje. De här reglerna avgör hur den strömmande slut punkten manipulerar spelnings listan för utdata (manifest). Filtret kan användas för att ändra vilka segment som strömmas för uppspelning. Filtren som genereras av Clipper är lokala filter och är bara för käll till gången. Till skillnad från renderade klipp är filter inte nya till gångar och kräver inte att ett kodnings jobb skapas. De kan snabbt skapas via [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest). de är dock bara GOP-korrekta. Normalt har till gångar som är kodade för strömning en GOP storlek på två sekunder.

Om du vill skapa ett dynamiskt manifest filter navigerar du till fliken **till gångar** och väljer önskad till gång. Välj under **klipp** knappen för den översta menyn. Välj dynamiskt manifest filter som urklipps läge på menyn Avancerade inställningar. Du kan sedan följa samma process för att skapa ett renderat klipp för att skapa filtret. Filter kan bara skapas från en enda till gång.

Följande bild illustrerar Clipper i dynamiskt manifest filter läge i Azure Portal: ![Azure Media Clipper i dynamiskt manifest filter läge i Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Skicka urklipps jobb
När du är färdig med att skriva klippet väljer du knappen Skicka jobb för att initiera motsvarande urklipps jobb eller dynamiskt manifest anrop.

## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med Azure Media Clipper läser du artikeln [komma igång](media-services-azure-media-clipper-getting-started.md) för att få mer information om hur du distribuerar widgeten.
