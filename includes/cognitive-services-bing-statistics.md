---
title: ta med fil
description: ta med fil
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313718"
---
Bing statistik innehåller analytics för API: er för Bing Search. Analytics innehåller samtalsvolym, övre frågesträngar, geografisk fördelning med mera. Om du vill aktivera Bing statistik i sökningen Bing betald prenumeration, navigera till din [Azure instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), Välj din betalda prenumeration och klicka på Aktivera Bing-statistik. Om du aktiverar Bing statistik ökar din prenumeration hastighet något (se [priser](https://aka.ms/bingstatisticspricing)).


> [!NOTE]
> Bing statistik är tillgänglig med betalda prenumerationer - är inte tillgänglig med kostnadsfria utvärderingsversioner. 

> [!NOTE]
> Du får inte använda några data som är tillgängliga via Bing statistik instrumentpanelen för att skapa program för distribution till tredje part.

Bing och uppdaterar analysdata var 24: e timme och underhåller upp till 13 månad som historik.

## <a name="accessing-your-analytics"></a>Åtkomst till din analytics

Om du vill komma åt instrumentpanelen analytics, gå till https://bingapistatistics.com. Kontrollera att du har loggat in med samma Microsoft-konto (MSA) används för att hämta en betald prenumeration.


## <a name="filtering-the-data"></a>Filtrera data

Som standard visar tabeller och diagram du alla mått data som du har åtkomst till. Du kan filtrera informationen som visas i tabeller och diagram genom att välja resurser, marknader, slutpunkter och rapporteringsperioden du är intresserad av. Tabeller och diagram ändras efter filter. Nedan beskrivs de filter som du kan ändra.

- **Resurs-ID**: unik resurs-ID som identifierar din Azure-prenumeration. Listan innehåller flera ID: N om du prenumererar på mer än en Bing Search API-nivå. Som standard markeras alla resurser.  
  
- **Marknader**: marknader var resultatet ska hämtas. Till exempel en-us (engelska, USA). Som standard markeras alla marknader. Observera att en WW marknaden marknaden med Bing om anropet inte anger en marknad och Bing kan inte fastställa användarens marknaden.  
  
- **Slutpunkter**: slutpunkter för Bing Search API: N. Listan innehåller alla slutpunkter som du har en betald prenumeration. Som standard markeras alla slutpunkter.  

- **Tidsintervall**: rapporteringsperioden. Du kan ange:  
  
  - Alla&mdash;innehåller upp till 13 månad kan du se  
  - Senaste 24 timmarna&mdash;innehåller analytics från de senaste 24 timmarna  
  - Föregående vecka&mdash;innehåller analytics från de senaste sju dagarna  
  - Senaste månaden&mdash;innehåller analytics från de senaste 30 dagarna  
  - Datumintervall&mdash;innehåller analytics från det angivna datumintervallet, om tillgängligt  
  
  > [!NOTE]  
  > Det kan ta upp till 24 timmar för mått att på instrumentpanelen. Instrumentpanelen visar datum och tid datan uppdaterades senast.  
  
  > [!NOTE]  
  > Mått är tillgängliga från den tidpunkt som du aktiverar tillägget Bing statistik. 


## <a name="charts-and-graphs"></a>Tabeller och diagram

Instrumentpanelen visar tabeller och diagram för mätvärdena som är tillgängliga för den valda slutpunkten. Inte alla mått är tillgängliga för alla slutpunkter. Tabeller och diagram för varje slutpunkt är statiska (du kan inte välja tabeller och diagram ska visas). Instrumentpanelen visar endast tabeller och diagram där det finns data. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Följande är möjliga mått. Varje mått anteckningar endpoint begränsningar. 

- **Anropa volym**: Visar antalet anrop som görs under rapporteringsperioden. Om rapporteringsperioden är för en dag, visar diagrammet antalet anrop per timme. Annars visar diagrammet antalet anrop per dag av rapporteringsperioden.  
  
  > [!NOTE]
  > Anropet volymen kan skilja sig från fakturering rapporter som innehåller vanligtvis bara antal samtal. 
  
-  **Vanliga frågor**: Visar de vanligaste frågorna och antalet förekomster av varje fråga under rapporteringsperioden. Du kan konfigurera antalet frågor som visas. Du kan till exempel visa översta 25, 50 eller 75-frågor. De vanligaste frågorna är inte tillgänglig för följande slutpunkter:  
  
  - /Images/trending
  - / bilder/information
  - / bilder/visualsearch
  - /videos/trending
  - information om-videor
  - /News
  - Nyheter-trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Vissa sökord kan förhindras om du vill ta bort konfidentiell information, till exempel e-postmeddelanden, telefonnummer, SSN, osv.  

- **Geografisk fördelning**: marknader var resultatet ska hämtas. Till exempel en-us (engelska, USA). Använder Bing den `mkt` Frågeparametern för att fastställa marknaden, om anges. Annars använder Bing signalerar till exempel anroparens IP-adress för att avgöra marknaden.  
  
- **Svaret kod Distribution**: HTTP-statuskoder för alla anrop under rapporteringsperioden.  
  
- **Anropa ursprung Distribution**: typer av webbläsare som används av användarna. Till exempel gräns, Chrome, Safari och FireFox. Anrop från utanför en webbläsare, till exempel robotar, Postman eller använder curl från en konsolapp grupperas under bibliotek. Ursprung bestäms med hjälp av en begäran användaragent huvudets värde. Om begäran inte innehåller rubriken användaragent, försöker Bing härledd ursprung från andra signaler.  
  
- **Säker sökning Distribution**: distribution av säker sökning värden. Till exempel avstängd, Måttlig eller strict. Den `safeSearch` frågan parametern innehåller värdet om anges. Annars Bing som standard värdet till måttlig.  
  
- **Svar begärt Distribution**: The Web Sök API besvarar du efterfrågas i det `responseFilter` Frågeparametern.  
  
- **Svar returnerades Distribution**: svaren Web Sök API returnerades i svaret.  
  
- **Svaret Server Distribution**: programservern som hanteras av din API-begäranden. Möjliga värden är Bing.com (för trafik från stationära och bärbara enheter) och Bing.com mobile (för trafik från mobila enheter). Servern bestäms med hjälp av en begäran användaragent huvudets värde. Om begäran inte innehåller rubriken användaragent, försöker Bing härledd servern från andra signaler.  
  


Nedan visas analytics som är tillgängliga för varje slutpunkt.

![Distribution av stödmatrisen för slutpunkten](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


