---
title: 'Bing anpassad sökning: Definiera anpassade Autosuggest förslag | Microsoft Docs'
description: Beskriver hur du konfigurerar anpassade Autosuggest med anpassade förslag
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352848"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurera din anpassade automatiska förslag
Om du prenumererar på anpassad sökning på lämplig nivå (finns i [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), kan du anpassa sökförslag som gjorts i din upplevelse för anpassad sökning. Anpassade automatiska förslag returnerar en lista över föreslagna frågor baserat på en partiell frågesträng som användaren anger. Med anpassade Autosuggest kan du ange anpassade sökförslag som är relevanta för dina sökinställningar. Du ange om du vill returnera endast anpassade förslag eller också ska omfatta Bing förslag. Om du inkluderar Bing förslag visas anpassade förslag före Bing förslag. Bing förslag är begränsade till kontexten för anpassad sökning-instans.

## <a name="configure-custom-autosuggest"></a>Konfigurera anpassade Autosuggest
Använd följande instruktioner om du vill konfigurera anpassade Autosuggest för anpassad sökning-instans.

1.  Logga in på [anpassad sökning](https://customsearch.ai).
2.  Klicka på en anpassad sökning-instans. För att skapa en instans, se [skapa din första Bing anpassad sökning instans](quick-start.md).
3.  Klicka på den **automatiska förslag** fliken.

## <a name="enable-bing-suggestions"></a>Aktivera Bing förslag
Om du vill aktivera Bing förslag växla den **automatisk Bing förslag** skjutreglaget till på plats. Skjutreglaget blir blå.

## <a name="add-suggestions"></a>Lägg till förslag
Om du vill lägga till ett namn, anger du den i textrutan. Tryck på RETUR eller klicka på den **+** ikon. Anpassade förslag kan vara i valfritt språk och visas innan Bing förslag.

## <a name="upload-suggestions"></a>Överför förslag
Du kan ladda upp en lista med förslag från en fil. Placera varje förslag på en separat rad. Klicka på ikonen ladda upp och välj din fil.

## <a name="remove-suggestions"></a>Ta bort förslag
Klicka på ikonen bredvid förslag som du vill ta bort om du vill ta bort ett förslag.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Det kan ta upp till 24 timmar för anpassad Autosuggest konfigurationsändringarna ska börja gälla.

## <a name="next-steps"></a>Nästa steg

- [Hämta anpassade förslag](./get-custom-suggestions.md)
- [Sök din anpassade instans](./search-your-custom-view.md)
- [Konfigurera och använda anpassade värdbaserade UI](./hosted-ui.md)