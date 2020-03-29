---
title: Lägga till analyser i API:et för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Bing Statistics tillhandahåller analyser till API:et för bildsökning i Bing. Analytics inkluderar samtalsvolym, de vanligaste frågesträngarna, geografisk distribution med mera.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882776"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Lägga till analyser i API:erna för Bing-sökning

Bing Statistics tillhandahåller analyser för API:erna för Bing-sökning. Dessa analyser omfattar samtalsvolym, de vanligaste frågesträngarna, geografisk distribution med mera. Du kan aktivera Bing-statistik i [Azure-portalen](https://ms.portal.azure.com) genom att navigera till din Azure-resurs och klicka på **Aktivera Bing-statistik**.

> [!IMPORTANT]
> * Bing Statistik är inte tillgänglig med gratis provprenumerationer, eller resurser på den fria `F0` prisnivån.
> * Du får inte använda några data som är tillgängliga via instrumentpanelen i Bing Statistics för att skapa program för distribution till tredje part.
> * Om du aktiverar Bing Statistics ökar prenumerationsgraden något. Se [priser](https://aka.ms/bingstatisticspricing) för mer information.


Följande bild visar tillgängliga analyser för varje Bing Search API-slutpunkt.

![Stödmatris för distribution efter slutpunkt](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Få tillgång till dina analyser

Bing uppdaterar analysdata var 24:e timme och upprätthåller upp till 13 månaders historik som du kan komma åt från [analytics-instrumentpanelen](https://bingapistatistics.com). Kontrollera att du är inloggad med samma Microsoft-konto (MSA) som du använde för att registrera dig för Bing Statistics.

> [!NOTE]  
> * Det kan ta upp till 24 timmar innan måtten visas på instrumentpanelen. Instrumentpanelen visar datum och tid då data senast uppdaterades.  
> * Mått är tillgängliga från den tidpunkt då du aktiverar tillägget Bing Statistics.

## <a name="filter-the-data"></a>Filtrera data

Som standard visar diagrammen och diagrammen alla mått och data som du har åtkomst till. Du kan filtrera data som visas i diagrammen och diagrammen genom att välja de resurser, marknader, slutpunkter och rapporteringsperiod som du är intresserad av. Du kan ändra följande filter:

- **Resurs-ID:** Det unika resurs-ID som identifierar din Azure-prenumeration. Listan innehåller flera ID:er om du prenumererar på mer än en API-nivå för Bing-sökning. Som standard är alla resurser markerade.  
  
- **Marknader**: De marknader där resultaten kommer ifrån. Till exempel en-us (engelska, USA). Som standard är alla marknader markerade. Marknaden `en-WW` är den marknad som Bing använder om samtalet inte anger en marknad och Bing inte kan fastställa användarens marknad.  
  
- **Slutpunkter**: Slutpunkterna för Bing Search API. Listan innehåller alla slutpunkter som du har en betald prenumeration för. Som standard är alla slutpunkter markerade.  

- **Tidsram**: Rapporteringsperioden. Du kan ange:
  - **Alla**: Inkluderar upp till 13 månaders data  
  - **Senaste 24 timmarna**: Inkluderar analyser från de senaste 24 timmarna  
  - **Förra veckan**: Inkluderar analyser från de föregående sju dagarna  
  - **Föregående månad**: Inkluderar analyser från de föregående 30 dagarna  
  - **Ett anpassat datumintervall:** Inkluderar analyser från det angivna datumintervallet, om tillgängligt  

## <a name="charts-and-graphs"></a>Diagram och diagram

Instrumentpanelen visar diagram och diagram över de mått som är tillgängliga för den valda slutpunkten. Alla mått är inte tillgängliga för alla slutpunkter. Diagrammen och diagrammen för varje slutpunkt är statiska (du kanske inte markerar de diagram och diagram som ska visas). Instrumentpanelen visar bara diagram och diagram som det finns data för.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Följande är möjliga mått och slutpunktsbegränsningar.

- **Samtalsvolym**: Visar antalet samtal som ringts under rapporteringsperioden. Om rapporteringsperioden är för en dag visar diagrammet antalet samtal per timme. Annars visar diagrammet antalet samtal per dag under rapporteringsperioden.  
  
  > [!NOTE]
  > Samtalsvolymen kan skilja sig från faktureringsrapporter, som i allmänhet endast omfattar lyckade samtal.

- **Toppfrågor:** Visar de vanligaste frågorna och antalet förekomster av varje fråga under rapporteringsperioden. Du kan konfigurera antalet frågor som visas. Du kan till exempel visa de 25, 50 eller 75 vanligaste frågorna. De vanligaste frågorna är inte tillgängliga för följande slutpunkter:  

  - /bilder/trender
  - /bilder/detaljer
  - /bilder/visualsearch
  - /videos/trending /videos
  - /videos/detaljer
  - /nyheter
  - /nyheter/trendtopics
  - /förslag  
  
  > [!NOTE]  
  > Vissa frågetermer kan undertryckas för att ta bort konfidentiell information som e-post, telefonnummer, SSN, etc.

- **Geografisk distribution**: De marknader där sökresultaten kommer. Till exempel `en-us` (engelska, USA). Bing använder `mkt` frågeparametern för att bestämma marknaden, om den anges. Annars använder Bing signaler som anroparens IP-adress för att bestämma marknaden.

- **Distribution av svarskod**: HTTP-statuskoder för alla anrop under rapporteringsperioden.

- **Call Origin Distribution**: De typer av webbläsare som används av användarna. Microsoft Edge, Chrome, Safari och Firefox. Samtal som görs utanför en webbläsare (till exempel robotar, Brevbärare eller med curl från en konsolapp) grupperas under Bibliotek. Ursprunget bestäms med hjälp av begärans användaragenthuvudvärde. Om begäran inte innehåller användaragenthuvudet försöker Bing härleda ursprunget från andra signaler.  

- **Säker sökdistribution**: Fördelningen av säkra sökvärden. Till exempel av, måttlig eller strikt. Frågeparametern `safeSearch` innehåller värdet om det anges. Annars standardvärdent till moderera.  

- **Begärd distribution**: Api:et för `responseFilter` webbsökning som du har begärt i frågeparametern.  

- **Svar Returnerad distribution**: Svaren som Webbsökning API returnerade i svaret.

- **Distribution av svarsserver**: Programservern som har hanterat dina API-begäranden. De möjliga värdena är Bing.com (för trafik som betjänas från stationära och bärbara enheter) och Bing.com-mobil (för trafik som betjänas från mobila enheter). Servern bestäms med hjälp av begärans användaragenthuvudvärde. Om begäran inte innehåller användaragenthuvudet försöker Bing härleda servern från andra signaler.

## <a name="next-steps"></a>Nästa steg

* [Vilka är Bing Search API:er?](bing-api-comparison.md)
* [Användnings- och visningskrav för API:er för Bing-sökresultat](use-display-requirements.md)
