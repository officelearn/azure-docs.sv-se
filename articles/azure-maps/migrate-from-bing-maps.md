---
title: 'Självstudie: Migrera från Bing Maps till Azure Maps | Microsoft Azure Maps'
description: 'En själv studie kurs om hur du migrerar från Bing Maps till Microsoft Azure Maps. Vägledningen vägleder dig genom hur du växlar till Azure Maps API: er och SDK: er.'
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643e49bdba76051c873ed549d5f6c21487f34056
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108924"
---
# <a name="migrate-from-bing-maps-to-azure-maps"></a>Migrera från Bing Maps till Azure Maps

Den här guiden innehåller information om hur du migrerar webb-, mobil-och serverbaserade program från Bing Maps till Azure Maps-plattformen. Den här guiden innehåller jämför ande kod exempel, förslag på migrering och metod tips för migrering till Azure Maps.

## <a name="azure-maps-platform-overview"></a>Översikt över Azure Maps plattform

Azure Maps ger utvecklare från alla branscher kraftfulla geospatiala funktioner, förpackade med de nya mappnings data som är tillgängliga för att tillhandahålla geografisk kontext för webb-och mobil program. Azure Maps är en Azure One API-kompatibel uppsättning REST API: er för kartor, sökning, routning, trafik, tids zoner, inhägnad, mappa data, väder data och många fler tjänster som åtföljs av både webb-och Android-SDK: er för att göra utvecklingen enkel, flexibel och portabel över flera plattformar. [Azure Maps finns också i Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Plattforms jämförelse på hög nivå

I följande tabell finns en lista över Bing Maps-funktioner på hög nivå och det relativa stödet för dessa funktioner i Azure Maps. Den här listan innehåller inte ytterligare Azure Maps funktioner som tillgänglighet, avgränsnings-API: er, trafik tjänster, spatiala åtgärder, åtkomst till direkt kart panel och batch Services.

| Bing Maps-funktion                     | Azure Maps support |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Planerad            |
| UWP SDK                               | Planerad            |
| WPF SDK                               | Planerad            |
| API: er för REST service                     | ✓                  |
| Automatiska förslag i                           | ✓                  |
| Vägvisningar (inklusive Truck)          | ✓                  |
| Avstånds mat ris                       | ✓                  |
| Utökade privilegier                            | Planerad            |
| Bilder – statisk karta                  | ✓                  |
| Bilder metadata                      | ✓                  |
| ISO Kron                            | ✓                  |
| Lokala insikter                        | ✓                  |
| Lokal sökning                          | ✓                  |
| Plats igenkänning                  | ✓                  |
| Platser (omvänt/omvända landskod) | ✓                  |
| Optimerade res vägar            | Planerad            |
| Fäst vid vägar                         | ✓                  |
| Avstånds Data Services (SDS)           | Delvis            |
| Tidszon                             | ✓                  |
| Trafik incidenter                     | ✓                  |
| Konfigurations drivna kartor             | E.t.                |

Bing Maps innehåller grundläggande nyckelbaserad autentisering. Azure Maps tillhandahåller både grundläggande nyckelbaserad autentisering och mycket säker Azure Active Directory autentisering.

## <a name="licensing-considerations"></a>Licens överväganden

När du migrerar till Azure Maps från Bing Maps bör följande beaktas med avseende på licensiering.

-   Azure Maps avgifter för användning av interaktiva kartor baserat på antalet inlästa kart paneler, medan Bing Maps debiteras för inläsningen av kart kontrollen (sessioner). På Azure Maps cachelagras kart paneler automatiskt för att minska kostnaderna för utvecklaren. En Azure Maps transaktion skapas för varje 15 kart paneler som läses in. I de interaktiva Azure Maps SDK: erna används 512-pixel-paneler och i genomsnitt genereras en eller färre transaktioner per sid-vy.

-   Azure Maps tillåter att data från sin plattform lagras i Azure. Den kan också cachelagras på andra platser i upp till sex månader enligt [användnings villkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Här följer några licensierings resurser för Azure Maps:

-   [Sidan Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps användnings villkor](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (ingår i villkoren för Microsoft Online Services)
-   [Välj rätt pris nivå i Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Föreslagen migrations plan

Följande är en uppgraderings plan med höga nivåer.

1.  Ta reda på vad Bing Maps SDK: er och tjänster som ditt program använder och kontrol lera att Azure Maps tillhandahåller alternativa SDK: er och tjänster som du kan migrera till.
2.  Skapa en Azure-prenumeration (om du inte redan har en) på <https://azure.com> .
3.  Skapa ett Azure Maps konto ([dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) och en autentiseringsnyckel eller Azure Active Directory ([dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4.  Migrera din program kod.
5.  Testa det migrerade programmet.
6.  Distribuera det migrerade programmet till produktion.

## <a name="azure-maps-technical-resources"></a>Azure Maps tekniska resurser

Här är en lista över användbara tekniska resurser för Azure Maps.

-   Översikt: https://azure.com/maps
-   Handlingar <https://aka.ms/AzureMapsDocs>
-   Kod exempel för webb-SDK: <https://aka.ms/AzureMapsSamples>
-   Forum för utvecklare: <https://aka.ms/AzureMapsForums>
-   Videos <https://aka.ms/AzureMapsVideos>
-   Blogg <https://aka.ms/AzureMapsBlog>
-   Azure Maps feedback (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Stöd för migrering

Utvecklare kan söka efter stöd för migrering via [forumen](https://aka.ms/AzureMapsForums) eller via ett av de många support alternativen för Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Ny terminologi 

Följande är en lista över vanliga Bing Maps-termer och som är kända med en annan term i Azure Maps.

| Villkor för Bing Maps                    | Azure Mapss period                                                |
|-----------------------------------|----------------------------------------------------------------|
| Satellitvy                            | Satellit eller antenn                                            |
| Anvisningar                        | Kan även kallas routning                             |
| Entiteter                          | Geometries eller funktioner                                         |
| `EntityCollection`                | Data källa eller lager                                           |
| `Geopoint`                        | Position                                                       |
| `GeoXML`                          | XML-filer i den spatiala IO-modulen                             |
| Mark överlägg                    | Bild skikt                                                    |
| Hybrid (i referens till mappnings typ) | Satellit med vägar                                           |
| Inforuta                           | Popup                                                          |
| Plats                          | Position                                                       |
| `LocationRect`                    | Avgränsnings ruta                                                   |
| Map Type                          | Kart stil                                                      |
| Navigeringsfält                    | Kartans stil väljare, zoomnings kontroll, bredd kontroll, kompass kontroll |
| Kartnålsikon                           | Bubbel lager, symbol lager eller HTML-markör                      |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du migrerar ditt Bing Maps-program med följande artiklar:

> [!div class="nextstepaction"]
> [Migrera en webbapp](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrera en webbtjänst](migrate-from-bing-maps-web-services.md)
