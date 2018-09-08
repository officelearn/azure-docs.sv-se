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
ms.openlocfilehash: e7a62a79bdc2e486fb6bfca34eb4addeba2bde0e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158321"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurera din anpassade automatiska förslag
Om du prenumererar på Custom Search på lämplig nivå (finns i den [prissidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), du kan anpassa sökförslag som gjorts i din Custom Search-upplevelse. Anpassade automatiska förslag returnerar en lista över föreslagna frågor baserat på en partiell frågesträng som användaren tillhandahåller. Med anpassade automatiska förslag i, kan du ange anpassade sökförslag som är relevanta för din sökupplevelse. Du anger om du vill returnera endast anpassade förslag eller också ska omfatta Bing förslag. Om du inkluderar förslag i Bing, visas anpassade förslag innan förslag för Bing. Förslag i Bing är begränsade till sammanhanget på din anpassade Sökinstans.

## <a name="configure-custom-autosuggest"></a>Konfigurera anpassade automatiska förslag
Använd följande instruktioner om du vill konfigurera anpassade automatiska förslag för din anpassade Sökinstans.

1.  Logga in på [anpassad sökning i](https://customsearch.ai).
2.  Klicka på en anpassad Sökinstans. Om du vill skapa en instans, [skapar din första Bing Custom Search-instans](quick-start.md).
3.  Klicka på den **automatiska förslag** fliken.

## <a name="enable-bing-suggestions"></a>Aktivera förslag för Bing
Om du vill aktivera förslag i Bing, växlar den **automatisk Bing förslag** skjutreglaget till aktivt läge. Skjutreglaget blir blå.

## <a name="add-suggestions"></a>Lägg till förslag
Lägg till ett förslag genom att ange den i textrutan. Tryck på RETUR eller klicka på den **+** ikon. Anpassade förslag kan vara på valfritt språk och visas innan förslag för Bing.

## <a name="upload-suggestions"></a>Ladda upp förslag
Du kan ladda upp en lista med förslag från en fil. Placera varje förslag på en separat rad. Klicka på ikonen ladda upp och välj din fil.

## <a name="remove-suggestions"></a>Ta bort förslag
Klicka på ikonen bredvid förslag som du vill ta bort för att ta bort ett förslag.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Det kan ta upp till 24 timmar för anpassade automatiska förslag konfigurationsändringarna ska börja gälla.

## <a name="next-steps"></a>Nästa steg

- [Få anpassade förslag](./get-custom-suggestions.md)
- [Sök efter din anpassade instans](./search-your-custom-view.md)
- [Konfigurera och använda anpassade värdbaserade UI](./hosted-ui.md)