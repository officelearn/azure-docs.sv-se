---
title: Konfigurera ett värdgränssnitt för anpassad Bing-sökning | Microsoft-dokument
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill konfigurera och integrera ett värdgränssnitt för anpassad Bing-sökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854073"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera värdbaserad UI-upplevelse

Bing Custom Search innehåller ett värdgränssnitt som du enkelt kan integrera i dina webbsidor och webbapplikationer som ett JavaScript-kodavsnitt. Med hjälp av portalen Anpassad Bing-sökning kan du konfigurera layout-, färg- och sökalternativen för användargränssnittet.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurera det anpassade värdbaserade användargränssnittet

Så här konfigurerar du ett värdgränssnitt för webbprogrammen. När du gör ändringar kommer fönstret till höger att ge dig en förhandsgranskning av användargränssnittet. Sökresultaten som visas är inte faktiska resultat för din instans.

1. Logga in på Bings anpassade [sökportal](https://customsearch.ai).  
  
2. Välj din anpassade Bing-sökningsinstans.

3. Klicka på fliken för **värdbaserat användargränssnitt**.  
  
4. Välj en layout.

    |  |  |
    |---------|---------|
    |Sökfält och resultat (standard)    | Visar en sökruta med sökresultat under.         |
    |Endast resultat     | Visar endast sökresultat utan en sökruta. När du använder den här layouten`&q=<query string>`måste du ange sökfrågan ( ). Lägg till frågeparametern i begäran-URL:en i JavaScript-kodavsnittet eller HTML-slutpunktslänken.        |
    |Pop-over     | Innehåller en sökruta och visar sökresultaten i ett glidande överlägg.        |
    
5. Välj ett färgtema. Du kan anpassa färgerna så att de passar ditt program genom att klicka på **Anpassa tema**. Om du vill ändra en färg anger du antingen färgens `#366eb8`RGB HEX-värde (till exempel ) eller klickar på färgförhandsgranskningen.

   Du kan förhandsgranska ändringarna till höger på portalen. Om du klickar på **Återställ till standard** återställs ändringarna till standardfärgerna för det valda temat.

   > [!NOTE]
   > Tänk på hjälpmedel när du väljer färger.

6. Under **Ytterligare konfigurationer**anger du värden som är lämpliga för din app. Dessa inställningar är valfria. Om du vill se effekten av att använda eller ta bort dem läser du förhandsgranskningsfönstret till höger. Tillgängliga konfigurationsalternativ är:  

7. Ange sökprenumerationsnyckeln eller välj en i listrutan. Listrutan fylls med nycklar från ditt Azure-kontos prenumerationer. Se [Api-kontot för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Om du har aktiverat autosuggest anger du prenumerationsnyckeln för automatiska förslag eller väljer en i listrutan. Listrutan fylls med nycklar från ditt Azure-kontos prenumerationer. Anpassad autosuggest kräver en specifik prenumerationsnivå, se [prissättningen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Använda anpassat användargränssnitt

Så här använder du det värdbaserade användargränssnittet antingen: 

- Inkludera skriptet på webbsidan  
  
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

- Du kan också använda följande URL i en webbläsare.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Lägg till följande frågeparametrar i URL:en efter behov. Information om dessa parametrar finns i [Api-referens för anpassad sökning.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
  >
  > - q
  > - mkt
  > - Safesearch
  > - setlang (setlang)

  > [!IMPORTANT]
  > Sidan kan inte visa din sekretesspolicy eller andra meddelanden och villkor. Lämpligheten för din användning kan variera.  

Mer information, inklusive ditt anpassade konfigurations-ID, går till **Slutpunkter** under fliken **Produktion.**

## <a name="configuration-options"></a>Konfigurationsalternativ

Du kan konfigurera beteendet för det värdbaserade användargränssnittet genom att klicka på **Ytterligare konfigurationer**och ange värden. Dessa inställningar är valfria. Om du vill se effekten av att använda eller ta bort dem läser du förhandsgranskningsfönstret till höger. 

### <a name="web-search-configurations"></a>Konfigurationer för webbsökning

|  |  |
|---------|---------|
|Webbresultat aktiverade    | Avgör om webbsökning är aktiverat (du ser en webbflik högst upp på sidan)        |
|Aktivera autosuggest     | Avgör om anpassade autosuggest är aktiverat (se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) för extra kostnad).        |
|Webbresultat per sida    | Antal webbsökresultat som ska visas åt gången (det högsta antalet är 50 resultat per sida).        |
|Bildtext   | Avgör om bilder visas med sökresultat.|


Följande konfigurationer visas om du klickar på **Visa avancerade konfigurationer:**


|  | |
|---------|---------|
|Markera ord     | Avgör om resultaten visas med sökord i fetstil.         |
|Länka mål    |  Avgör om webbsidan öppnas på en ny webbläsarflik (Tom) eller samma webbläsarflik (själv) när användaren klickar på ett sökresultat.        |

### <a name="image-search-configurations"></a>Konfigurationer för bildsökning

| | |
|---------|---------|
|Bildresultat aktiverat     | Avgör om bildsökning är aktiverat (fliken Bilder visas högst upp på sidan).            |
|Bildresultat per sida     | Antal bildsökresultat som ska visas åt gången (det högsta antalet är 150 resultat per sida).          |

Följande konfiguration visas om du klickar på **Visa avancerade konfigurationer**.  
  
| | |
|---------|---------|
| Aktivera filter     | Lägger till filter som användaren kan använda för att filtrera de bilder som Bing returnerar. Användaren kan till exempel filtrera resultaten för endast animerade GIF-filer.|

### <a name="video-search-configurations"></a>Konfigurationer för videosökning

|  | |
|---------|---------|
|Videoresultat aktiverat     | Avgör om videosökning är aktiverad (fliken Videor visas högst upp på sidan).           |
|Videoresultat per sida   | Antal videosökresultat som ska visas åt gången (det högsta antalet är 150 resultat per sida).        |

Följande konfiguration visas om du klickar på **Visa avancerade konfigurationer**.  
  
|  | |
|---------|---------|
|Aktivera filter    | Lägger till filter som användaren kan använda för att filtrera de videor som Bing returnerar. Användaren kan till exempel filtrera resultaten för videor med en viss upplösning eller videor som upptäckts under de senaste 24 timmarna.          |

### <a name="miscellaneous-configurations"></a>Diverse konfigurationer


| |  |
|---------|---------|
|Sidrubrik   | Text som visas i rubrikområdet på sökresultatsidan (inte för popup-layout).        |
|Verktygsfält tema    | Bestämmer bakgrundsfärgen för rubrikområdet på sökresultatsidan. |

Följande konfigurationer visas om du klickar på **Visa avancerade konfigurationer**.  

|Column1  |Column2  |
|---------|---------|
|Platshållare för sökrutatext   | Text som visas i sökrutan före inmatning.        |
|Url till titellänk    |Mål för titellänken.         |
|URL för logotyp     | Bilden visas bredvid titeln.         |
|Favicon    | Ikonen som visas i webbläsarens namnlist.          |

Följande konfigurationer gäller endast om du använder användargränssnittet Värddator via HTML-slutpunkten (de gäller inte om du använder JavaScript-kodavsnittet).

- Sidrubrik
- Verktygsfält tema
- URL till rubriklänk
- URL för logotyp
- Faviicon URL  

## <a name="next-steps"></a>Nästa steg

- [Använda dekorationsmarkörer för att markera text](../bing-web-search/hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
