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
ms.openlocfilehash: 2aec8ba969fb639f2d785a429441f6ed4bbf7dfb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987687"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera din värdbaserade användargränssnitt

När du har konfigurerat din anpassade Sökinstans, kan du anropa den anpassade API: et Search för att hämta sökresultaten och visa dem i din app. Eller, om din app är en webbapp, kan du använda det värdbaserade användargränssnitt som ger anpassad sökning.   

## <a name="configure-custom-hosted-ui"></a>Konfigurera anpassat värdbaserade gränssnitt

Följ dessa steg om du vill konfigurera en värdbaserad Användargränssnittet för din webbapp:

1. Logga in på Custom Search [portal](https://customsearch.ai).  
  
2. Klicka på en anpassad Sökinstans. Om du vill skapa en instans, [skapar din första Bing Custom Search-instans](quick-start.md).  

3. Klicka på den **finns Användargränssnittet** fliken.  
  
4. Välj en layout.
  
  - Sökfältet och resultat (standard) &mdash; den här layouten är söksidan traditionella med sökrutan och sökresultat.
  - Resulterar endast &mdash; den här layouten visar endast sökresultat. Den här layouten visas inte en sökruta. Du måste ange sökfrågan genom att lägga till Frågeparametern (& q =\<frågesträng >) till URL: en i JavaScript-kodavsnitt eller HTML-slutpunkt-länk.
  - POP-over &mdash; den här layouten innehåller en sökruta och visar sökresultatet i ett glidande överlägg.
      
5. Välj ett färgtema. Det är möjligt teman: 
  
  - Klassisk
  - Mörk
  - Skyline blå

  Klicka på alla teman och se vilket tema som fungerar bäst med ditt webbprogram. Om du behöver att finjustera färgtema vill bättre integrera med din webbapp klickar du på **Anpassa tema**. Inte alla konfigurationer av färg gäller för alla layoutteman. Om du vill ändra en färg, ange färgens RGB HEX-värdet (till exempel #366eb8) i motsvarande textruta. Eller, klicka på färgknappen och klickar på nyans som passar dig. 
  
  När du har ändrat en färg att se hur ändringen påverkar förhandsversion exemplet till höger. Du kan alltid Klicka **Återställ till standard** att gå tillbaka till sidan för det valda temat.

  > [!NOTE]
  > När du ändrar färgteman överväga hjälpmedel när du väljer färger.

5. Under **ytterligare konfigurationer**, ange värden för din app. De här inställningarna är valfria. Effekten av att använda eller ta bort dem finns i förhandsgranskningsfönstret till höger. Tillgängliga alternativ är:  
  
  - Web search konfigurationer:
    - Webb-resultat aktiverat &mdash; anger om webbsökning är aktiverad (du kommer se en Web-flik längst ned på sidan).
    - Aktivera automatiska förslag i &mdash; avgör om anpassade automatiska förslag är aktiverad (se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) ytterligare kostnad).
    - Webb-resultat per sida &mdash; antalet webbsökningar att visa i taget (det högsta antalet är 50 resultat per sida).
    - Bildtexten &mdash; avgör om avbildningar visas med sökresultat.
  
    Följande konfigurationer visas om du klickar på **visa avancerade konfigurationer**.  
  
    - Markera ord &mdash; avgör om resultatet visas med sökvillkoren visas med fet stil. 
    - Länkmål &mdash; anger om webbsidan öppnas i en ny webbläsarflik (tomt) eller samma webbläsarfliken (själv) när användaren klickar på ett sökresultat. 

  - Bild sökkonfigurationer:
    - Bild resultat aktiverat &mdash; anger om bildsökning är aktiverad (en avbildningar fliken visas överst på sidan).   
    - Bild resultat per sida &mdash; antalet bildsökningsresultat att visa i taget (det högsta antalet är 150 resultat per sida).  
  
    Följande konfiguration visas om du klickar på **visa avancerade konfigurationer**.  
  
    - Aktivera filter &mdash; lägger till filter som användaren kan använda för att filtrera bilder som Bing returnerar. Användaren kan till exempel filtrera resultaten för endast animerade GIF-filer.

  - Videosökning konfigurationer:
    - Videoresultat aktiverat &mdash; anger om videosökning är aktiverad (en videor fliken visas överst på sidan).  
    - Video resultat per sida &mdash; antalet videosökningsresultat att visa i taget (det högsta antalet är 150 resultat per sida).
  
    Följande konfiguration visas om du klickar på **visa avancerade konfigurationer**.  
  
    - Aktivera filter &mdash; lägger till filter som användaren kan använda för att filtrera videor som Bing returnerar. Användaren kan till exempel filtrera resultaten för videor med en viss upplösning eller videofilmer som har identifierats under de senaste 24 timmarna.

  - Övriga konfigurationer:
    - Sidtitel &mdash; Text som visas i avsnittet rubriken på sidan med sökresultat (inte för pop över layout).
    - Verktygsfältet tema &mdash; anger bakgrundsfärgen för rubrikområdet på sökresultatsidan.  
  
    Följande konfigurationer visas om du klickar på **visa avancerade konfigurationer**.  
  
    - Sök textplatshållare &mdash; Text som visas i Sök-rutan innan du börjar indata.
    - Rubrik länka url &mdash; mål för rubrik-länken.
    - URL: en logotyp &mdash; bild som visas bredvid rubriken. 
    - Favicon url &mdash; ikonen som visas i namnlisten i webbläsaren.  

    Följande konfigurationer gäller bara om du använder Användargränssnittet finns via HTML-slutpunkt (de inte gäller om du använder JavaScript-kodavsnitt).
    
    - Sidtitel
    - Verktygsfältet tema
    - Rubrik-URL för länk
    - URL: en logotyp
    - Faviicon URL  
  
6. Ange prenumerationsnyckeln search eller välj en från den nedrullningsbara listan. Den nedrullningsbara listan fylls med nycklar från ditt Azure-konto prenumerationer. Se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Om du har aktiverat Automatiska förslag, ange prenumerationsnyckel för automatiska förslag eller välj någon från den nedrullningsbara listan. Den nedrullningsbara listan fylls med nycklar från ditt Azure-konto prenumerationer. Anpassade automatiska förslag kräver en viss prenumeration-nivå, finns i den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> När du gör ändringar i anpassade värdbaserade UI-konfigurationen ger till höger en visuell referens för ändringar som görs. Visade sökresultaten är inte faktiska resultat för din instans.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Använd anpassat gränssnitt

Använda värdbaserad användargränssnitt, antingen: 

- Inkludera skriptet i din webbsida  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Kan också använda följande URL i en webbläsare.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Lägg till följande Frågeparametrar i URL: en efter behov. Information om dessa parametrar finns i [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) referens.
  >
  > - frågor och
  > - mkt
  > - säker sökning
  > - setlang

  > [!IMPORTANT]
  > Sidan kan inte visa din sekretesspolicy eller andra meddelanden och villkor. Din lämplighet kan variera.  

För ytterligare information, inklusive anpassade konfigurations-ID går du till **slutpunkter** under den **produktion** fliken.

## <a name="next-steps"></a>Nästa steg

- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)