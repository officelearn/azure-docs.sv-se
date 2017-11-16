---
title: "Använd Azure Media Clipper i portalen | Microsoft Docs"
description: "Skapa klipp med hjälp av Azure Media Clipper från Azure Portal"
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: faaae7edbc2fb62ae219dd963f405e7246c982d4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Skapa klipp med Azure Media Clipper i portalen
Du kan använda Azure Media Clipper i portalen för att skapa klipp från tillgångar i ditt media services-konton. Kom igång genom att gå till media services-konto i portalen. Välj sedan den **underklipp** fliken.

På den **underklipp** fliken verket kan du börja skriva klipp. I portalen laddas Clipper enkel bithastighet MP4s, multibithastighet MP4s och live Arkiv som publiceras med en giltig strömningslokaliserare. Avpublicera tillgångar har inte lästs in.

## <a name="producing-clips"></a>Producerar klipp
Dra och släpp en tillgång på clip-gränssnittet för att skapa en bild. Om markering-gånger är kända, kan du manuellt ange dem i gränssnittet. Annars uppspelning tillgången eller dra spelhuvudet om du vill hitta önskad markering in och slut tid. Om en markering i eller markera ut tid inte tillhandahålls, klippet börjar från början eller fortsätter till slutet av inkommande tillgången respektive.

Använd knapparna ram framåt/GOP-vidarebefordring eller ram-bakåt/GOP-bakåt för att navigera med ram-Precision/GOP-noggrannhet. För urklippet mot flera tillgångar dra och släpp flera resurser i gränssnittet clip från panelen tillgången val. Du kan välja och ordna om tillgångar i gränssnittet till önskad ordning. Panelen tillgången markeringen innehåller tillgångsinformation varaktighet, typ och upplösning metadata för varje tillgång. Tänk Källidentifiering för varje indatafilen när sammanbinda flera resurser tillsammans. Om käll-lösningar skiljer sig åt upscaled lägre upplösning indata för att uppfylla lösning av högsta upplösning tillgången. Välj knappen förhandsgranskning och klippet spelas upp markeringen gånger om du vill förhandsgranska utdata för urklippet jobb.

## <a name="producing-dynamic-manifest-filters"></a>Producerar dynamiska manifestet filter
[Dynamiska manifestet filter](https://azure.microsoft.com/blog/dynamic-manifest/) beskriver en uppsättning regler baserat på manifestet attribut och tidslinje tillgångsinformation. Reglerna avgör hur din strömningsslutpunkt manipulerar listan utdata (manifest). Filtret kan användas för att ändra vilka segment strömmas för uppspelning. Filter som produceras av Clipper är lokala filter och är specifika för tillgången källa. Till skillnad från renderade klipp filter är inte nya tillgångar och kräver inte ett kodningsjobb att skapa. De kan skapas snabbt den [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), de är dock endast GOP korrekt. Tillgångar kodad för strömning har vanligtvis en GOP storlek på två sekunderna.

Om du vill skapa ett manifest dynamiska filter, väljer du dynamiska manifestet filter som urklippet-läge från menyn Avancerade inställningar. Du kan följa samma process för att skapa en bild för att skapa filtret. Filter kan endast genereras mot ett enskilt objekt.

## <a name="submitting-clipping-jobs"></a>Skickar urklippet jobb
När du har skrivit klippet Välj knappen skicka jobbet för initiera motsvarande urklippet jobb eller dynamiska manifestet anrop.