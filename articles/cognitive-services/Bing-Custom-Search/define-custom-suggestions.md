---
title: Definiera förslag med anpassade förslag för automatiska förslag – Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: Anpassad autosuggest returnerar en lista över föreslagna sökfrågesträngar som är relevanta för din sökupplevelse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072810"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurera din anpassade upplevelse för automatiska förslag

Anpassad autosuggest returnerar en lista över föreslagna sökfrågesträngar som är relevanta för din sökupplevelse. De föreslagna frågesträngarna baseras på en partiell frågesträng som användaren tillhandahåller i sökrutan. Listan kommer att innehålla högst 10 förslag. 

Du anger om du bara vill returnera anpassade förslag eller även inkludera Bing-förslag. Om du inkluderar Bing-förslag visas anpassade förslag före Bing-förslagen. Om du ger tillräckligt med relevanta förslag är det möjligt att den returnerade listan med förslag inte innehåller Bing-förslag. Bing-förslag är alltid i samband med din Custom Search-instans. 

Om du vill konfigurera sökfrågeförslag för din instans klickar du på fliken **Autosuggest.**  

> [!NOTE]
> Om du vill använda den här funktionen måste du prenumerera på Anpassad sökning på lämplig nivå (se [priser).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Det kan ta upp till 24 timmar innan förslag återspeglas i serveringsslutpunkten (API eller värdgränssnitt).

## <a name="enable-bing-suggestions"></a>Aktivera Bing-förslag

Om du vill aktivera Bing-förslag växlar du reglaget **Automatisk Bing-förslag** till på-positionen. Skjutreglaget blir blått.

## <a name="add-your-own-suggestions"></a>Lägg till egna förslag

Om du vill lägga till egna förslag på frågesträng lägger du till dem i listan under **Användardefinierade förslag**. När du har lagt till ett förslag i **+** listan trycker du på enter-tangenten eller klickar på ikonen. Du kan ange förslaget på vilket språk som helst. Du kan lägga till högst 5 000 frågesträngförslag.

## <a name="upload-suggestions"></a>Ladda upp förslag

Som ett alternativ kan du ladda upp en lista med förslag från en fil. Filen måste innehålla en sökfrågesträng per rad. Om du vill ladda upp filen klickar du på uppladdningsikonen och väljer den fil som ska laddas upp. Tjänsten extraherar förslagen från filen och lägger till dem i listan.

## <a name="remove-suggestions"></a>Ta bort förslag

Om du vill ta bort ett förslag på frågesträng klickar du på ikonen ta bort bredvid det förslag som du vill ta bort.

## <a name="block-suggestions"></a>Blockera förslag

Om du inkluderar Bing-förslag kan du lägga till en lista med sökfrågesträngar som du inte vill att Bing ska returnera. Om du vill lägga till blockerade frågesträngar klickar du på **Visa blockerade förslag**. Lägg till frågesträngen i listan och tryck **+** på returen eller klicka på ikonen. Du kan lägga till högst 50 blockerade frågesträngar.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Det kan ta upp till 24 timmar innan konfigurationsändringarna för anpassade automatiska förslag börjar gälla.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Aktivera autosuggest i värdgränssnittet

Om du vill aktivera frågesträngförslag för det värdbaserade användargränssnittet klickar du på **Värdgränssnittet**. Bläddra ned till avsnittet **Ytterligare konfiguration.** Under **Webbsökning**väljer du **På** för **Aktivera autosuggest**. Om du vill aktivera Autosuggest måste du välja en layout som innehåller en sökruta.


## <a name="calling-the-autosuggest-api"></a>Anropa Api:et för autosuggest

Om du vill hämta föreslagna frågesträngar med `GET` api:et för anpassad sökning i Bing skickar du en begäran till följande slutpunkt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Svaret innehåller en lista `SearchAction` över objekt som innehåller de föreslagna frågesträngarna.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Varje förslag `displayText` innehåller `query` a och fält. Fältet `displayText` innehåller den föreslagna frågesträngen som du använder för att fylla i listrutan i sökrutan.

Om användaren väljer en föreslagen frågesträng i listrutan använder `query` du frågesträngen i fältet när du [anropar API:et för anpassad sökning i Bing](overview.md).


## <a name="next-steps"></a>Nästa steg

- [Få anpassade förslag](./get-custom-suggestions.md)
- [Sök i din anpassade instans](./search-your-custom-view.md)
- [Konfigurera och använda anpassat värdgränssnitt](./hosted-ui.md)
