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
ms.openlocfilehash: a8d588b186652ab86ee1e8152bd9be08f0f1ef04
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978643"
---
Bing statistik tillhandahåller analyser för API: er för Bing-sökresultat. Analytics innehåller samtalsvolym, övre frågesträngar, geografisk fördelning och mycket mer. Om du vill aktivera Bing statistik i din sökning i Bing betald prenumeration, navigera till din [Azure-instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), Välj din betalda prenumeration och klicka på Aktivera Bing statistik. Om du aktiverar Bing statistik ökar din prenumerationstakt något (se [priser](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Bing statistik är tillgänglig med betalda prenumerationer – det är inte tillgängliga med kostnadsfria utvärderingsprenumerationer.

> [!NOTE]
> Du får inte använda några data som är tillgängligt via Bing statistik-instrumentpanelen för att skapa program för distribution till tredje part.

Bing och uppdaterar analysdata var 24: e timme och underhåller upp till 13 månad tillhandahåller historik.

## <a name="accessing-your-analytics"></a>Åtkomst till dina analyser

Du öppnar instrumentpanelen i analytics, gå till https://bingapistatistics.com. Kontrollera att du har loggat in med samma Microsoft-konto (MSA) du använde för att hämta din betalda prenumeration.

## <a name="filtering-the-data"></a>Filtrera data

Som standard visas alla måttdata som du har åtkomst till av tabeller och diagram. Du kan filtrera de data som visas i tabeller och diagram genom att välja resurser, marknader, slutpunkter och rapporteringsperioden du är intresserad av. Tabeller och diagram ändras för att återspegla de filter du använder. Nedan beskrivs de filter som du kan ändra.

- **Resurs-ID**: unika resurs-ID som identifierar din Azure-prenumeration. Listan innehåller flera ID: N om du prenumererar på mer än en Bing Search API-nivå. Som standard markeras alla resurser.  
  
- **Marknader**: marknader där resultatet kommer från. Till exempel en-us (engelska, USA). Som standard markeras alla marknader. Observera att en WW marknaden är på marknaden som Bing använder om anropet inte anger en marknad och Bing kan inte fastställa användarens marknaden.  
  
- **Slutpunkter**: Bing Search API-slutpunkter. Listan innehåller alla slutpunkter som du har en betald prenumeration. Som standard markeras alla slutpunkter.  

- **Tidsram**: rapporteringsperioden. Du kan ange:
  - Alla&mdash;innehåller upp till 13 månad tillhandahåller data  
  - Senaste 24 timmarna&mdash;omfattar analys från de senaste 24 timmarna  
  - Senaste veckan&mdash;omfattar analys från de senaste sju dagarna  
  - Senaste månaden&mdash;omfattar analys från de senaste 30 dagarna  
  - Ett eget datumintervall&mdash;omfattar analys från det angivna datumintervallet, om det är tillgängligt  

  > [!NOTE]  
  > Det kan ta upp till 24 timmar för mått för att lyfta fram på instrumentpanelen. Instrumentpanelen visar datum och tid som data uppdaterades senast.  

  > [!NOTE]  
  > Mått är tillgängliga från den tidpunkt som du aktiverar Bing statistik-tillägget.

## <a name="charts-and-graphs"></a>Tabeller och diagram

Instrumentpanelen visar diagram över mått som är tillgängliga för den valda slutpunkten. Inte alla mått är tillgängliga för alla slutpunkter. Tabeller och diagram för varje slutpunkt är statiska (du kan inte välja tabeller och diagram ska visas). Instrumentpanelen visar endast tabeller och diagram som det finns data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Här följer möjliga mått. Varje mått kommentarer om begränsningar för slutpunkten.

- **Anropa volym**: Visar antalet anrop som görs under rapporteringsperioden. Om rapporteringsperioden är i en dag, visar diagrammet antalet anrop som görs per timme. I annat fall visar diagrammet antalet anrop per dag av perioden.  
  
  > [!NOTE]
  > Anropet volymen kan skilja sig från fakturering rapporter, som vanligtvis innehåller endast samtal.

- **Vanliga frågor**: Visar de viktigaste frågorna och antal förekomster av varje fråga under rapporteringsperioden. Du kan konfigurera antalet frågor som visas. Du kan till exempel visa översta 25, 50 eller 75-frågor. De viktigaste frågorna är inte tillgänglig för följande slutpunkter:  

  - /Images/trending
  - / bilder/information
  - / bilder/visualsearch
  - /videos/trending
  - / filmer/information
  - /News
  - / news/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Vissa sökord kan förhindras om du vill ta bort konfidentiell information, till exempel e-postadresser, telefonnummer, SSN, osv.

- **Geografisk fördelning**: marknader där resultatet kommer från. Till exempel en-us (engelska, USA). Bing använder den `mkt` frågeparameter för att fastställa marknaden, om anges. I annat fall använder Bing signaler, till exempel anroparens IP-adress för att avgöra på marknaden.

- **Svarskod**: HTTP-statuskoder för alla anrop under rapporteringsperioden.

- **Anropa ursprung Distribution**: typer av webbläsare som används av användarna. Till exempel Edge, Chrome, Safari och FireFox. Anrop som görs från utanför en webbläsare, till exempel robotar, Postman eller med curl från en konsolapp är grupperade under bibliotek. Ursprunget bestäms med hjälp av begärandets användaragent huvudets värde. Om begäran inte inkludera användaragent-huvud, försöker Bing att härleda ursprung från andra signaler.  

- **Säker sökning Distribution**: distributionen av värden för säker sökning. Till exempel av, Måttlig, eller strikt. Den `safeSearch` fråga parametern innehåller värdet, om anges. I annat fall standard Bing värdet till begränsad.  

- **Svar begärt Distribution**: API för webbsökning i det svar du har begärt i den `responseFilter` frågeparameter.  

- **Svar returneras Distribution**: de svar som API för webbsökning returnerades i svaret.

- **Svaret Server Distribution**: programservern som hanteras av din API-begäranden. Möjliga värden är Bing.com (för trafik som hämtats från stationära och bärbara enheter) och Bing.com-mobile (för trafik som hämtats från mobila enheter). Servern bestäms med hjälp av begärandets användaragent huvudets värde. Om begäran inte inkludera användaragent-huvud, försöker Bing att härleda servern från andra signaler.

Nedan visas de analyser som är tillgängliga för varje slutpunkt.

![Distribution enligt supportmatris för slutpunkt](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
