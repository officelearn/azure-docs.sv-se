---
title: Konfigurera en värdbaserad Användargränssnittet för anpassad sökning i Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd den här artikeln för att konfigurera och integrera en värdbaserad Användargränssnittet för anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: e30c36cbde3bf112b012526e6268dfc4414f64a9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555480"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera din värdbaserade användargränssnitt

Anpassad sökning i Bing tillhandahåller en värdbaserad Användargränssnittet som du kan enkelt integrera i dina webbsidor och web program som ett JavaScript-kodfragment. Du kan med hjälp av Bing Custom Search-portalen för att konfigurera layouten, färg och alternativ för sökning av Användargränssnittet.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurera anpassade värdbaserade Användargränssnittet

Följ dessa steg om du vill konfigurera en värdbaserad Användargränssnittet för dina webbprogram. När du gör ändringar ger till höger dig en förhandsgranskning av Användargränssnittet. Visade sökresultaten är inte faktiska resultat för din instans.

1. Logga in på Bing Custom Search [portal](https://customsearch.ai).  
  
2. Välj din Bing Custom Search-instans.

3. Klicka på fliken för **värdbaserat användargränssnitt**.  
  
4. Välj en layout.

    |  |  |
    |---------|---------|
    |Sökfältet och resultat (standard)    | Visar en sökruta med sökresultat under den.         |
    |Endast resultat     | Visar sökresultat, utan en sökruta. När du använder den här layouten, måste du ange sökfrågan (`&q=<query string>`). Lägg till fråga-parametern i fråge-URL i JavaScript-kodavsnitt eller HTML-slutpunkt-länk.        |
    |POP-over     | Innehåller en sökruta och visar sökresultatet i ett glidande överlägg.        |
    
5. Välj ett färgtema. Du kan anpassa färger för att passa ditt program genom att klicka på **Anpassa tema**. Om du vill ändra en färg, antingen ange färgvärde RGB HEX (till exempel `#366eb8`), eller klicka på förhandsversionen av färg.

  Du kan förhandsgranska dina ändringar på höger sida av portalen. Klicka på **Återställ till standard** återgår ändringarna till sidan för det valda temat.

  > [!NOTE]
  > Överväg att hjälpmedel när du väljer färger.

6. Under **ytterligare konfigurationer**, ange värden för din app. De här inställningarna är valfria. Effekten av att använda eller ta bort dem finns i förhandsgranskningsfönstret till höger. Tillgängliga alternativ är:  

7. Ange prenumerationsnyckeln search eller välj en från den nedrullningsbara listan. Den nedrullningsbara listan fylls med nycklar från ditt Azure-konto prenumerationer. Se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Om du har aktiverat Automatiska förslag, ange prenumerationsnyckel för automatiska förslag eller välj någon från den nedrullningsbara listan. Den nedrullningsbara listan fylls med nycklar från ditt Azure-konto prenumerationer. Anpassade automatiska förslag kräver en viss prenumeration-nivå, finns i den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

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

## <a name="configuration-options"></a>Konfigurationsalternativ

Du kan konfigurera beteendet för värdbaserade Användargränssnittet genom att klicka på **ytterligare konfigurationer**, och att tillhandahålla värden. De här inställningarna är valfria. Effekten av att använda eller ta bort dem finns i förhandsgranskningsfönstret till höger. 

### <a name="web-search-configurations"></a>Web sökkonfigurationer

|  |  |
|---------|---------|
|Webbresultat aktiverat    | Anger om webbsökning är aktiverad (du kommer se en Web-flik längst ned på sidan)        |
|Aktivera automatiska förslag     | Anger om anpassade automatiska förslag är aktiverad (se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) ytterligare kostnad).        |
|Webbresultat per sida    | Antal webbsökningar att visa i taget (det högsta antalet är 50 resultat per sida).        |
|Bildtext   | Avgör om avbildningar visas med sökresultat.|


Följande konfigurationer visas om du klickar på **visa avancerade konfigurationer**:


|  | |
|---------|---------|
|Markera ord     | Avgör om resultatet visas med sökvillkoren visas med fet stil.         |
|Länkmål    |  Anger om webbsidan öppnas i en ny webbläsarflik (tomt) eller samma webbläsarfliken (själv) när användaren klickar på ett sökresultat.        |

### <a name="image-search-configurations"></a>Bild sökkonfigurationer

| | |
|---------|---------|
|Bildresultat aktiverat     | Anger om bildsökning är aktiverad (en avbildningar fliken visas överst på sidan).            |
|Bildresultat per sida     | Antal bildsökningsresultat att visa i taget (det högsta antalet är 150 resultat per sida).          |

Följande konfiguration visas om du klickar på **visa avancerade konfigurationer**.  
  
| | |
|---------|---------|
| Aktivera filter     | Lägger till filter som användaren kan använda för att filtrera bilder som Bing returnerar. Användaren kan till exempel filtrera resultaten för endast animerade GIF-filer.|

### <a name="video-search-configurations"></a>Videosökning konfigurationer

|  | |
|---------|---------|
|Videoresultat aktiverat     | Anger om videosökning är aktiverad (en videor fliken visas överst på sidan).           |
|Video resultat per sida   | Antal videosökningsresultat att visa i taget (det högsta antalet är 150 resultat per sida).        |

Följande konfiguration visas om du klickar på **visa avancerade konfigurationer**.  
  
|  | |
|---------|---------|
|Aktivera filter    | Lägger till filter som användaren kan använda för att filtrera videor som Bing returnerar. Användaren kan till exempel filtrera resultaten för videor med en viss upplösning eller videofilmer som har identifierats under de senaste 24 timmarna.          |

### <a name="miscellaneous-configurations"></a>Övriga konfigurationer


| |  |
|---------|---------|
|Sidtitel   | Text som visas i avsnittet rubriken på sidan med sökresultat (inte för pop över layout).        |
|Verktygsfältet tema    | Anger bakgrundsfärgen för rubrikområdet på sökresultatsidan. |

Följande konfigurationer visas om du klickar på **visa avancerade konfigurationer**.  

|Kolumn1  |Kolumn2  |
|---------|---------|
|Sök textplatshållare   | Text som visas i Sök-rutan innan du börjar indata.        |
|Rubrik länka url    |Mål för länken rubrik.         |
|URL: en logotyp     | Bild som visas bredvid rubriken.         |
|Favicon    | Ikonen som visas i namnlisten i webbläsaren.          |

Följande konfigurationer gäller bara om du använder Användargränssnittet finns via HTML-slutpunkt (de inte gäller om du använder JavaScript-kodavsnitt).

- Sidtitel
- Verktygsfältet tema
- Rubrik-URL för länk
- URL: en logotyp
- Faviicon URL  

## <a name="next-steps"></a>Nästa steg

- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
