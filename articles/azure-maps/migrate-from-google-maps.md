---
title: 'Självstudiekurs: Migrera från Google Maps till Azure Maps | Microsoft Azure Maps'
description: En handledning om hur du migrerar från Google Maps till Microsoft Azure Maps. Vägledning går igenom hur du växlar till Azure Maps API:er och SDK:er.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77913709"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrera från Google Maps till Azure Maps

Den här självstudien innehåller insikter om hur du migrerar webb-, mobil- och serverbaserade program från Google Maps till Microsoft Azure Maps-plattformen. Den här självstudien innehåller jämförande kodexempel, migreringsförslag och metodtips för att migrera till Azure Maps.

## <a name="azure-maps-platform-overview"></a>Översikt över Azure Maps-plattformen

Azure Maps ger utvecklare från alla branscher kraftfulla geospatiala funktioner. Funktionerna är packade med regelbundet uppdaterade kartdata för att ge geografisk kontext för webb- och mobilappar. Azure Maps har en Azure One API-kompatibel uppsättning REST API:er. REST-API:erna erbjuder kartor, sökning, routning, trafik, tidszoner, geolocation, geofencing, kartdata, väder, mobilitet och rumsliga operationer. Verksamheten åtföljs av både Webb- och Android-SDK:er för att göra utvecklingen enkel, flexibel och bärbar på flera plattformar.

## <a name="high-level-platform-comparison"></a>Jämförelse av plattform på hög nivå

Tabellen innehåller en lista över Azure Maps-funktioner på hög nivå som motsvarar Google Maps-funktioner. Den här listan visar inte alla Azure Maps-funktioner. Ytterligare Azure Maps-funktioner inkluderar: hjälpmedel, geofencing, isochrones, rumsliga åtgärder, direkt åtkomst till kartpaneler, batchtjänster och jämförelser av datatäckning (det vill än bildtäckning).

| Google Maps-funktionen         | Support för Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Planerad                                |
| API:er för REST-tjänst           | ✓                                      |
| Vägbeskrivning (routning)        | ✓                                      |
| Avstånd Matrix             | ✓                                      |
| Höjd                   | Planerad                                |
| Geokodning (framåt/bakåt) | ✓                                      |
| Geolocation                 | Ej tillämpligt                                    |
| Närmaste vägar               | ✓                                      |
| Platser Sök               | ✓                                      |
| Platser Detaljer              | Saknas – webbplats & telefonnummer tillgängligt |
| Platser Bilder               | Ej tillämpligt                                    |
| Placera Komplettera automatiskt          | ✓                                      |
| Fäst mot väg                | ✓                                      |
| Hastighetsgränser                | ✓                                      |
| Statiska kartor                 | ✓                                      |
| Statisk gatuvy          | Ej tillämpligt                                    |
| Tidszon                   | ✓                                      |
| Api för inbäddade kartor           | Ej tillämpligt                                    |
| Mappa webbadresser                    | Ej tillämpligt                                    |

Google Maps ger grundläggande nyckelbaserad autentisering. Azure Maps tillhandahåller både grundläggande nyckelbaserad autentisering och Azure Active Directory-autentisering. Azure Active Directory-autentisering ger fler säkerhetsfunktioner jämfört med den grundläggande nyckelbaserade autentiseringen.

## <a name="licensing-considerations"></a>Tillståndsöverväganden

När du migrerar till Azure Maps från Google Maps bör du tänka på följande punkter om licensiering.

- Azure Maps avgifter för användning av interaktiva kartor, som baseras på antalet inlästa kartpaneler. Å andra sidan, Google Maps avgifter för att ladda kartkontrollen. I de interaktiva Azure Maps-SDK:erna cachelagras kartpaneler automatiskt för att minska utvecklingskostnaden. En Azure Maps-transaktion genereras för varje 15 kartpaneler som läses in. De interaktiva Azure Maps-SDK:erna använder 512-pixelpaneler, och i genomsnitt genererar den en eller mindre transaktioner per sidvy.
- Ofta är det mer kostnadseffektivt att ersätta statiska kartbilder från Google Maps webbtjänster med Azure Maps Web SDK. Azure Maps Web SDK använder kartpaneler. Om inte användaren panorerar och zoomar kartan genererar tjänsten ofta bara en bråkdel av en transaktion per kartbelastning. Azure Maps web SDK har alternativ för att inaktivera panorering och zoomning, om så önskas. Dessutom ger Azure Maps web SDK mycket mer visualiseringsalternativ än den statiska kartwebbtjänsten.
- Med Azure Maps kan data från plattformen lagras i Azure. Dessutom kan data cachelagras någon annanstans i upp till sex månader enligt [användarvillkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Här är några relaterade resurser för Azure Maps:

- [Prissida för Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure priskalkylator](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps användningsvillkor](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (ingår i villkoren för Microsoft Online Services)
- [Välj rätt prisnivå i Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Föreslagen migreringsplan

Följande är en övergångsplan på hög nivå.

1. Inventera Google Maps SDK:er och tjänster som programmet använder. Kontrollera att Azure Maps tillhandahåller alternativa SDK:er och tjänster.
2. Om du inte redan har en, skapa [https://azure.com](https://azure.com)en Azure-prenumeration på .
3. Skapa ett Azure Maps-konto ([dokumentation)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)och autentiseringsnyckel eller Azure Active Directory ([dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrera programkoden.
5. Testa det migrerade programmet.
6. Distribuera det migrerade programmet till produktion.

## <a name="azure-maps-technical-resources"></a>Tekniska resurser för Azure Maps

Här är en lista över användbara tekniska resurser för Azure Maps.

- Översikt:[https://azure.com/maps](https://azure.com/maps)
- Dokumentation:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Exempel på webbkod:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Utvecklare Forum:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videor:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blogg:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Tech Blogg:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Feedback från Azure Maps (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Stöd för migrering

Utvecklare kan söka [migreringsstöd](https://aka.ms/AzureMapsForums) via forumen eller via något av de många Azure-supportalternativen:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du migrerar google maps-programmet med följande artiklar:

> [!div class="nextstepaction"]
> [Migrera en webbapp](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrera en Android-app](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrera en webbtjänst](migrate-from-google-maps-web-services.md)
