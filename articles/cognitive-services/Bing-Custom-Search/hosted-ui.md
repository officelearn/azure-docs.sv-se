---
title: 'Bing anpassad sökning: Sök webbplats | Microsoft Docs'
description: Beskriver hur du konfigurerar värdbaserade UI
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352878"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera din värdbaserade användargränssnitt
När du har konfigurerat anpassade search-instansen, kan du anropa anpassade Sök-API för att få sökresultaten och visa dem i din app. Eller om din app är en webbapp, kan du använda det värdbaserade användargränssnitt som innehåller anpassad sökning.   

## <a name="configure-custom-hosted-ui"></a>Konfigurera anpassade värdbaserade UI
Använd följande instruktioner om du vill konfigurera en värdbaserad Användargränssnittet ska ingå i ditt webbprogram.
1.  Logga in på anpassad sökning [portal](https://customsearch.ai).
2.  Klicka på en anpassad sökning-instans. För att skapa en instans, se [skapa din första Bing anpassad sökning instans](quick-start.md).
3.  Klicka på den **finns UI** fliken.
4.  Välj en layout.
    - Sökfältet och resultat (standard) &mdash; visa en sökrutan och sökresultat
    - Resulterar endast &mdash; inte visa en kryssruta, visa endast resultat
    - POP över &mdash; inte visa en kryssruta, visa endast resultat i ett glidande överlägg
    
   > [!IMPORTANT]
   > Se till att ta Frågeparametern customConfig när du väljer den **resulterar endast** layout finns [fråga parametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  Under **ytterligare konfigurationer**, ange värden för din app. De här inställningarna är valfria. Om du vill se effekten av att använda eller ta bort dem finns i förhandsgranskningsfönstret till höger.  Tillgängliga alternativ är:
    - Web sökkonfigurationer:
        - Web resultat aktiverat &mdash; avgör om sökresultaten returneras
        - Aktivera autosuggest &mdash; avgör om anpassad autosuggest är aktiverad
        - Web resultat per sida &mdash; antalet sökresultaten ska visas i taget
        - Bildtexten &mdash; avgör om bilder visas med sökresultaten
        - Markera ord &mdash; avgör om resultat visas med sökorden i fetstil
    - Bild sökkonfigurationer:
        - Bild resultat aktiverat &mdash; anger om bilden sökresultaten visas
    - Olika konfigurationer:
        - Sidrubrik &mdash; texten som visas i rubrikområdet
        - Verktygsfältet tema &mdash; anger bakgrundsfärgen på sidan rubrikområdet
        - Sök textplatshållare &mdash; texten som visas i rutan Sök föregående som indata
        - Rubrik länka url &mdash; mål för länken rubrik
        - URL: en logotyp &mdash; bild som visas bredvid rubriken 
        - Favicon url &mdash; ikon som visas i webbläsarens namnlist

   > [!IMPORTANT]
   > Minst en av avbildningen sökning eller webbsökning måste aktiveras.

6.  Söknyckeln för prenumerationen eller välj en från listrutan. Listrutan är ifylld med nycklar från Azure-prenumerationer för ditt konto. Se [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Om du har aktiverat Automatiska förslag nyckeln automatiska förslag prenumeration eller välj en från listrutan. Listrutan är ifylld med nycklar från Azure-prenumerationer för ditt konto. Anpassade automatiska förslag kräver en angivna prenumerationen nivå, finns det [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> När du ändrar anpassad värdbaserade UI-konfiguration innehåller rutan till höger en visuell referens för de ändringar som gjorts. Sökresultaten visas är inte faktiska resultat för din instans

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Använda anpassade användargränssnitt
Använda Gränssnittet för värdbaserade antingen: 

- Infoga ett skript på webbsidan
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Använd den Webbadress som anges `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Sidan kan inte visa sekretesspolicyn eller andra meddelanden och villkor. Din lämplighet kan variera.  

För ytterligare information, inklusive din anpassade konfigurations-ID, gå till **slutpunkter** under den **produktion** fliken.

## <a name="next-steps"></a>Nästa steg
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)