---
title: 'Anpassad sökning i Bing: Definiera anpassade automatiska förslag förslag | Microsoft Docs'
description: Beskriver hur du konfigurerar anpassade automatiska förslag med egna förslag
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 11d3b1c2d98caa8d6527c52bec1cc65ba22c6c3b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958755"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurera din anpassade automatiska förslag

Anpassade automatiska förslag returnerar en lista över föreslagna search frågesträngar som är relevanta för din sökupplevelse. Föreslagna frågesträngar baseras på en partiell frågesträng som användaren anger i sökrutan. Listan innehåller högst 10 förslag. 

Du anger om du vill returnera endast anpassade förslag eller också ska omfatta Bing förslag. Om du inkluderar förslag i Bing, visas anpassade förslag innan förslag för Bing. Om du ger tillräckligt med relevanta förslag så är det möjligt att den returnerade listan med förslag som inte innehåller förslag för Bing. Förslag i Bing är alltid i samband med din anpassade Sökinstans. 

Om du vill konfigurera frågan sökförslag för din instans, klickar du på den **automatiska förslag** fliken.  

> [!NOTE]
> Om du vill använda den här funktionen måste du prenumerera Custom Search på lämplig nivå (se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Det kan ta upp till 24 timmar innan förslag återspeglas i betjänar slutpunkt (API: et eller värdbaserade UI).

## <a name="enable-bing-suggestions"></a>Aktivera förslag för Bing

Om du vill aktivera förslag i Bing, växlar den **automatisk Bing förslag** skjutreglaget till aktivt läge. Skjutreglaget blir blå.

## <a name="add-your-own-suggestions"></a>Lägga till egna förslag

Om du vill lägga till en egen sträng frågeförslag, lägga till dem i listan under **användardefinierade förslag**. Tryck på RETUR-tangenten när du lägger till ett förslag i listan eller klicka på den **+** ikon. Du kan ange förslaget på valfritt språk. Du kan lägga till upp till 5 000 frågeförslag sträng.

## <a name="upload-suggestions"></a>Ladda upp förslag

Du kan också ladda upp en lista med förslag från en fil. Filen måste innehålla en frågesträng för sökning per rad. Om du vill ladda upp filen, klickar du på ikonen ladda upp och välj filen som ska överföras. Tjänsten extraherar förslagen från filen och lägger till dem i listan.

## <a name="remove-suggestions"></a>Ta bort förslag

Klicka på ikonen bredvid förslag som du vill ta bort för att ta bort en fråga sträng förslag.

## <a name="block-suggestions"></a>Blockera förslag

Om du inkluderar förslag i Bing, kan du lägga till en lista över search frågesträngar som du inte vill att Bing ska returneras. Lägg till blockerade frågesträngar, klicka på **Show blockeras förslag**. Lägg till frågesträngen i listan och tryck på RETUR eller klicka på den **+** ikon. Du kan lägga till upp till 50 blockerade frågesträngar.



[!INCLUDE[publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Det kan ta upp till 24 timmar för anpassade automatiska förslag konfigurationsändringarna ska börja gälla.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Aktivera automatiska förslag i Användargränssnittet för installation

Om du vill aktivera frågeförslag sträng för värdbaserade Användargränssnittet, klickar du på **finns Användargränssnittet**. Rulla ned till den **ytterligare konfiguration** avsnittet. Under **webbsökning**väljer **på** för **aktivera automatiska förslag i**. Om du vill aktivera automatiska förslag, måste du välja en layout som innehåller en sökruta.


## <a name="calling-the-autosuggest-api"></a>Anropa de API: et för automatiska förslag

För att få föreslagna frågesträngar med hjälp av API: et för Bing Custom Search kan skicka en `GET` begäran till följande slutpunkt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Svaret innehåller en lista över `SearchAction` objekt som innehåller de föreslagna frågesträngarna.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Varje förslag innehåller en `displayText` och `query` fält. Den `displayText` fältet innehåller föreslagna frågesträngen som används för att fylla i listrutan för din Sök-rutan.

Om användaren väljer en föreslagna frågesträng från den nedrullningsbara listan, använda frågesträngen i den `query` fältet när du anropar den [Bing Custom Search API](overview.md).


## <a name="next-steps"></a>Nästa steg

- [Få anpassade förslag](./get-custom-suggestions.md)
- [Sök efter din anpassade instans](./search-your-custom-view.md)
- [Konfigurera och använda anpassade värdbaserade UI](./hosted-ui.md)