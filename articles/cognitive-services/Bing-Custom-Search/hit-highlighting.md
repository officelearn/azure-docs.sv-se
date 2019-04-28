---
title: Använda decoration markörer för att markera text – Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Beskriver hur du aktiverar textutsmyckning i sökningssvar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bf87db232484a8ba4fa1e3b8e4de921f152e2345
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110126"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Med decoration markörer för att markera text

Bing stöder markering som markerar sökord av träffar (eller andra villkor som Bing hittar relevanta) i visningssträngar av några av svaren. Till exempel en webbsida `name`, `displayUrl`, och `snippet` fält kan definiera frågetermen.

Som standard omfattar Bing inte markering markörer i visningssträngar. För att inkludera markörerna, innehåller den `textDecorations` frågeparameter i din begäran och ge den värdet **SANT**. Bing markerar frågetermen med E000 och E001 Unicode-tecken till markerar början och slutet av perioden. Till exempel om frågetermen är färdas tala och antingen termen finns i fältet, omges termen av träffar fokus tecken som visas i följande exempel:  
  
![Träffmarkering](./media/bing-hit-highlighting.PNG) 

Innan den visas strängen i användargränssnittet, ska du ersätta Unicode-tecken med tecken som är lämpliga för din visningsformatet. Exempel: Om du visar texten som HTML, du kan markera frågetermen genom att ersätta E000 med < b\> och E001 med < /b\>. Om du inte vill tillämpa formatering, kan du ta bort markörerna från strängen. 

Bing kan du använda Unicode-tecken eller HTML-taggar som markörer. Om du vill ange vilka markörer att använda inkluderar den `textFormat` frågeparameter. Om du vill markera innehåll med Unicode-tecken, ange `textFormat` till Raw (standard), och markera innehåll med HTML-taggar, ange `textFormat` till HTML. 
  
Om `textDecorations` är **SANT**, Bing kan innehålla följande markörer i visningssträngar av svar. Om det finns ingen HTML-motsvarighet, är HTML-tabellcell tom.

|Unicode|HTML|Beskrivning
|-|-|-
|U+E000|\<b>|Markerar starten av frågetermen (markering av träffar)
|U+E001|\</b>|Markerar slutet av frågetermen
|U+E002|\<i>|Markerar starten av kursiv innehåll 
|U+E003|\</i>|Markerar slutet av kursiv innehåll
|U+E004|\<br/>|Markerar en radbrytning
|U+E005||Markerar starten av ett telefonnummer
|U+E006||Markerar slutet av ett telefonnummer
|U+E007||Markerar starten av en adress
|U+E008||Markerar slutet av en adress
|U+E009|\&nbsp;|Markerar ett hårt blanksteg
|U+E00C|\<strong>|Markerar starten av fet innehåll
|U+E00D|\</strong>|Markerar slutet av fet innehåll
|U+E00E||Markerar starten av innehåll vars bakgrund ska vara lättare än dess omgivande bakgrund
|U+E00F||Markerar slutet av innehåll vars bakgrund ska vara lättare än dess omgivande bakgrund
|U+E010||Markerar starten av innehåll vars bakgrund ska vara mörkare än dess omgivande bakgrund
|U+E011||Markerar slutet av innehåll vars bakgrund ska vara mörkare än dess omgivande bakgrund
|U+E012|\<del>|Markerar starten av innehåll som ska strykas över
|U+E013|\</del>|Markerar slutet av innehåll som ska strykas över
|U+E016|\<sub>|Markerar starten av nedsänkt innehåll
|U+E017|\</sub>|Markerar slutet av nedsänkt innehåll
|U+E018|\<sup>|Markerar starten av upphöjd innehåll
|U+E019|\</sup>|Markerar slutet av upphöjd innehåll

I följande exempel visas en `Computation` svar som innehåller nedsänkt markörer för en log(2) frågeterm. Den `expression` fältet innehåller markörer endast om ”textDecoration är **SANT**.

![beräkningen markörer](./media/bing-markers-computation.PNG) 

Om begäran inte har begärt dekorationer, blir log10(2) i uttrycket. 
  
