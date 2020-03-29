---
title: Så här använder du dekorationsmarkörer för att markera text - API för webbsökning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder textdekorationer och markerar i sökresultaten med hjälp av API:et för webbsökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854022"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Använda dekorationsmarkörer för att markera text

Bing stöder träffmarkering, som markerar frågetermer (eller andra termer som Bing finner relevanta) i visningssträngarna för vissa svar. Ett webbsidesresultat och `name` `displayUrl` `snippet` fält kan till exempel innehålla markerade frågetermer. 

Som standard innehåller Bing inte markeringsmarkörer i visningssträngar. Om du vill aktivera `textDecorations` markörerna tar du med `true`frågeparametern i begäran och ställer in den på .

## <a name="hit-highlighting-example"></a>Exempel på träffmarkering

I följande exempel visas `Sailing Dinghy`ett webbresultat för . Bing markerade början och slutet av frågetermen med unicode-tecknen E000 och E001.
  
![Tryck på markering](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Innan du visar resultatet i användargränssnittet ska du ersätta Unicode-tecknen med de som är lämpliga för visningsformatet.

## <a name="marker-formatting"></a>Markörformatering

Bing ger möjlighet att använda unicode-tecken eller HTML-taggar som markörer. Om du vill ange vilka markörer som ska användas inkluderar du frågeparametern [textFormat:](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| Värde             | Markör                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-tecken (standard) |
| `textFormat=HTML` | HTML-tecken              |

## <a name="additional-text-decorations"></a>Ytterligare textdekorationer

Bing kan returnera flera olika textdekorationer. Ett `Computation` svar kan till exempel innehålla nedsänkta `log(2)` markörer `expression` för frågetermen i fältet .

![beräkningsmarkörer](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Om begäran inte angav dekorationer, skulle fältet `expression` innehålla `log10(2)`. 

Om `textDecorations` `true`är , Bing kan innehålla följande markörer i visningssträngar av svar. Om det inte finns någon motsvarande HTML-tagg är tabellcellen tom.

|Unicode|HTML|Beskrivning
|-|-|-
|U+E000|\<b>|Markerar början av frågetermen (träffmarkering)
|U+E001|\</b>|Markerar slutet på frågetermen
|U+E002|\<Jag>|Markerar början på kursivt innehåll 
|U+E003|\</i>|Markerar slutet på kursivt innehåll
|U+E004|\<br/>|Markerar en radbrytning
|U+E005||Markerar början på ett telefonnummer
|U+E006||Markerar slutet på ett telefonnummer
|U+E007||Markerar början på en adress
|U+E008||Markerar slutet på en adress
|U+E009|\&nbsp;|Markerar ett utrymme som inte går sönder
|U+E00C|\<stark>|Markerar början på fetstil
|U+E00D|\</stark>|Markerar slutet på fetstil
|U+E00E||Markerar början på innehåll vars bakgrund ska vara ljusare än den omgivande bakgrunden
|U+E00F||Markerar slutet på innehåll vars bakgrund ska vara ljusare än den omgivande bakgrunden
|U+E010||Markerar början på innehåll vars bakgrund ska vara mörkare än den omgivande bakgrunden
|U+E011||Markerar slutet på innehåll vars bakgrund ska vara mörkare än den omgivande bakgrunden
|U+E012|\<del>|Markerar början på innehåll som ska slås igenom
|U+E013|\</del>|Markerar slutet på innehållet som ska slås igenom
|U+E016|\<sub>|Markerar början på nedsänkt innehåll
|U+E017|\</sub>|Markerar slutet på nedsänkt innehåll
|U+E018|\<sup>|Markerar början på upphöjd innehåll
|U+E019|\</sup>|Markerar slutet på upphöjd innehåll

## <a name="next-steps"></a>Nästa steg

* [Vad är API för webbsökning i Bing?](overview.md) 
* [Ändra storlek på och beskär miniatyrer](resize-and-crop-thumbnails.md)