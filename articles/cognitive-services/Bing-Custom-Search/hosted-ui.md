---
title: 'Anpassad sökning i Bing: Webbplatssökning | Microsoft Docs'
description: Beskriver hur du konfigurerar värdbaserade UI
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158439"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera din värdbaserade användargränssnitt
När du har konfigurerat din anpassade Sökinstans, kan du anropa den anpassade API: et Search för att hämta sökresultaten och visa dem i din app. Eller, om din app är en webbapp, kan du använda det värdbaserade användargränssnitt som ger anpassad sökning.   

## <a name="configure-custom-hosted-ui"></a>Konfigurera anpassat värdbaserade gränssnitt
Använd följande instruktioner för att konfigurera en värdbaserad Användargränssnittet ska ingå i din webbapp.
1.  Logga in på Custom Search [portal](https://customsearch.ai).
2.  Klicka på en anpassad Sökinstans. Om du vill skapa en instans, [skapar din första Bing Custom Search-instans](quick-start.md).
3.  Klicka på den **finns Användargränssnittet** fliken.
4.  Välj en layout.
    - Sökfältet och resultat (standard) &mdash; visa en sökruta och sökresultat
    - Resulterar endast &mdash; inte visa en sökruta, visa endast resultat
    - POP-over &mdash; inte visa en sökruta, visa endast resultat i ett glidande överlägg
    
   > [!IMPORTANT]
   > Se till att inkludera Frågeparametern customConfig när du väljer den **resulterar endast** layout, se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  Under **ytterligare konfigurationer**, ange värden för din app. De här inställningarna är valfria. Effekten av att använda eller ta bort dem finns i förhandsgranskningsfönstret till höger.  Tillgängliga alternativ är:
    - Web search konfigurationer:
        - Webb-resultat aktiverat &mdash; avgör om sökresultaten returneras
        - Aktivera automatiska förslag i &mdash; avgör om anpassade automatiska förslag är aktiverad
        - Webb-resultat per sida &mdash; antalet webbsökningar att visa i taget
        - Bildtexten &mdash; avgör om avbildningar visas med sökresultat
        - Markera ord &mdash; avgör om resultatet visas med sökvillkoren visas med fet stil
    - Bild sökkonfigurationer:
        - Bild resultat aktiverat &mdash; avgör om bildsökningsresultat returneras
    - Övriga konfigurationer:
        - Sidtitel &mdash; Text som visas i rubrikområdet
        - Verktygsfältet tema &mdash; anger bakgrundsfärgen för rubrikområdet
        - Sök textplatshållare &mdash; Text som visas i Sök-rutan innan du börjar indata
        - Rubrik länka url &mdash; mål för länken rubrik
        - URL: en logotyp &mdash; bild som visas bredvid rubriken 
        - Favicon url &mdash; ikonen som visas i webbläsarens namnlist

   > [!IMPORTANT]
   > Minst en av bildsökning eller webbsökning måste aktiveras.

6.  Ange prenumerationsnyckeln search eller välj en från listrutan. I listrutan är ifylld med nycklar från ditt konto Azure-prenumerationer. Se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Om du har aktiverat Automatiska förslag prenumerationsnyckel för automatiska förslag eller välj en från listrutan. I listrutan är ifylld med nycklar från ditt konto Azure-prenumerationer. Anpassade automatiska förslag kräver en angivna prenumeration nivå, finns i den [prissidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> När du gör ändringar i anpassade värdbaserade UI-konfigurationen ger till höger en visuell referens för ändringar som görs. Sökresultaten visas är inte de faktiska resultaten för din instans

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Använd anpassat gränssnitt
Använda värdbaserad användargränssnitt, antingen: 

- Inkludera skriptet i din webbsida
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

- Använd av Webbadressen som tillhandahålls `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Sidan kan inte visa din sekretesspolicy eller andra meddelanden och villkor. Din lämplighet kan variera.  

För ytterligare information, inklusive anpassade konfigurations-ID går du till **slutpunkter** under den **produktion** fliken.

## <a name="next-steps"></a>Nästa steg
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)