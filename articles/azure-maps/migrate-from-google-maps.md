---
title: Migrera från Google Maps | Microsoft Docs
description: 'En själv studie kurs om hur du migrerar från Google Maps till Microsoft Azure Maps. Vägledningen vägleder dig genom hur du växlar till Azure Maps API: er och SDK: er.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770313"
---
# <a name="migrate-from-google-maps"></a>Migrera från Google Maps

I den här självstudien får du information om hur du migrerar webb-, mobil-och serverbaserade program från Google Maps till Microsoft Azure Maps-plattformen. Den här självstudien innehåller jämför ande kod exempel, förslag på migrering och metod tips för migrering till Azure Maps.

## <a name="azure-maps-platform-overview"></a>Översikt över Azure Maps plattform

Azure Maps ger utvecklare från alla branscher kraftfulla geospatiala funktioner som packas med regelbundna uppdaterade kart data för att tillhandahålla geografisk kontext för webb-och mobil program. Azure Maps har en Azure One API-kompatibel uppsättning REST API: er för kartor, sökning, routning, trafik, tids zoner, geolokalisering, inhägnad, kart data, väder, rörlighet och spatiala åtgärder som åtföljs av både webb-och Android-SDK: er för att utveckla enkla, flexibla och bärbart över flera plattformar.

## <a name="high-level-platform-comparison"></a>Plattforms jämförelse på hög nivå

I följande tabell finns en lista över Google Maps-funktioner på hög nivå och det relativa stödet för dessa funktioner i Azure Maps. Den här listan innehåller inte ytterligare Azure Maps funktioner, till exempel tillgänglighet, polyinhägnads-API: er, ISO Kron, spatiala åtgärder, direkt kart panels åtkomst, batch Services och data täcknings jämförelser (t. ex. bilder-täckning).

| Google Maps-funktion         | Azure Maps support                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Planerad                                |
| API: er för REST service           | ✓                                      |
| Vägvisningar (routning)        | ✓                                      |
| Avståndsmatris             | ✓                                      |
| Höjning                   | Planerad                                |
| Landskod (framåt/omvänd) | ✓                                      |
| Geoplats                 | Gäller inte                                    |
| Sök efter platser               | ✓                                      |
| Plats information              | Saknas – webbplats & telefonnummer är tillgängligt |
| Placerar foton               | Gäller inte                                    |
| Placera Autoavsluta          | ✓                                      |
| Statiska Maps                 | ✓                                      |
| Statisk gata vy          | Gäller inte                                    |
| Tidszon                   | ✓                                      |
| Mappar inbäddat API           | Gäller inte                                    |
| Kart-URL: er                    | Gäller inte                                    |

Google Maps innehåller grundläggande nyckelbaserad autentisering. Azure Maps tillhandahåller både grundläggande nyckelbaserad autentisering och mycket säker Azure Active Directory autentisering.

## <a name="licensing-considerations"></a>Licens överväganden

När du migrerar till Azure Maps från Google Maps bör följande punkter beaktas med avseende på licensiering.

- Azure Maps avgifter för användning av interaktiva kartor baserat på antalet inlästa kart paneler, medan Google Maps debiteras för inläsningen av kart kontrollen. I de interaktiva Azure Maps SDK: erna cachelagras kartor automatiskt för att minska kostnaderna för utvecklaren. En Azure Maps transaktion skapas för varje 15 kart paneler som läses in. I de interaktiva Azure Maps SDK: erna används 512-pixel-paneler och i genomsnitt genereras en eller färre transaktioner per sid-vy.
- Det är ofta mycket mer kostnads effektivt att ersätta statiska kart bilder från Google Maps-webbtjänster med Azure Maps-webbsdk eftersom detta använder kart paneler och om användaren inte Pans och zoomar kartan, kommer de ofta bara att generera en bråkdel av en transaktion per kart belastning. Azure Maps Web SDK har alternativ för att inaktivera panorering och zoomning. Dessutom tillhandahåller Azure Maps Web SDK ett mycket mer visualiserings alternativ än en statisk kart webb tjänst.
- Azure Maps tillåter att data från sin plattform lagras i Azure. Den kan också cachelagras på andra platser i upp till sex månader enligt [användnings villkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Här följer några relaterade resurser för Azure Maps:

- [Sidan Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/)
- [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps användnings villkor](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (ingår i villkoren för Microsoft Online Services)
- [Välj rätt pris nivå i Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Föreslagen migrations plan

Följande är en uppgraderings plan med höga nivåer.

1. Ta reda på vad Google Maps SDK: er och tjänster ditt program använder och kontrol lera att Azure Maps tillhandahåller alternativa SDK: er och tjänster som du kan migrera till.
2. Skapa en Azure-prenumeration (om du inte redan har en) på [https://azure.com](https://azure.com).
3. Skapa ett Azure Maps konto ([dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) och en autentiseringsnyckel eller Azure Active Directory ([dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrera din program kod.
5. Testa det migrerade programmet.
6. Distribuera det migrerade programmet till produktion.

## <a name="azure-maps-technical-resources"></a>Azure Maps tekniska resurser

Här är en lista över användbara tekniska resurser för Azure Maps.

- Översikt: [https://azure.com/maps](https://azure.com/maps)
- Dokumentation: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Kod exempel för webb-SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Forum för utvecklare: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videor: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blogg: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps feedback (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Migreringsstöd

Utvecklare kan söka efter stöd för migrering via [forumen](https://aka.ms/AzureMapsForums) eller via ett av de många support alternativen för Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du migrerar ditt Google Maps-program med följande artiklar:

> [!div class="nextstepaction"]
> [Migrera en webbapp](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrera en Android-app](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrera en webb tjänst](migrate-from-google-maps-web-services.md)
