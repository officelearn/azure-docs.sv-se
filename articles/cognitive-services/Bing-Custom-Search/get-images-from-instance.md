---
title: Hämta bilder från din anpassade vy – Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Översikt på hög nivå om hur du använder Anpassad sökning i Bing för att hämta bilder från din anpassade vy av webben.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 886f04d2ea143b7c6c22aa2e746de179fcf7e7dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090655"
---
# <a name="get-images-from-your-custom-view"></a>Hämta bilder från din anpassade vy

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med Bing-anpassade bilder kan du utöka din anpassade Sök upplevelse med bilder. På ett liknande sätt som med webbresultat har anpassad sökning stöd för bildsökning i listan över webbplatser i dina instanser. Du kan hämta avbildningar med hjälp av Bing: s anpassade bilder Sök-API eller via funktionen för värdbaserad gränssnitt. Att använda funktionen för värdbaserade gränssnitt är enkel att använda och rekommendera för att få din Sök upplevelse igång i kort ordning.  Information om hur du konfigurerar det värdbaserade användar gränssnittet för att inkludera avbildningar finns i [Konfigurera den värdbaserade gränssnitts upplevelsen](hosted-ui.md).

Om du vill ha mer kontroll över att Visa Sök resultaten kan du använda Bing: s anpassade bilder Sök-API. Eftersom anrop till API: et liknar att anropa API för bildsökning i Bing, kan du checka [bildsökning i Bing](../Bing-Image-Search/overview.md) för exempel som anropar API: et. Men innan du gör det kan du bekanta dig med de [anpassade avbildningarna Sök-API referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) innehåll. De huvudsakliga skillnaderna är de frågeparametrar som stöds (du måste inkludera Frågeparametern customConfig) och den slut punkt som du skickar begär anden till.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
