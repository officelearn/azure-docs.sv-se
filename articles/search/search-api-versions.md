---
title: 'API-versionshantering för .NET SDK och REST API: er – Azure Search'
description: 'Princip för programversion för Azure Search REST API: er och klientbiblioteket i .NET SDK.'
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c07a0c8f5440033455c69fe40806adf9b548c16f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126971"
---
# <a name="api-versions-in-azure-search"></a>API-versioner i Azure Search
Azure Search sprider funktionsuppdateringar regelbundet. Ibland, men inte alltid kräver dessa uppdateringar en ny version av API för att bevara bakåtkompatibilitet. Publicera en ny version kan du styra när och hur du integrerar du uppdateringar av search service i din kod.

Som en regel publicerar i Azure Search-teamet nya versioner vid behov, eftersom det kan omfatta vissa arbete för att uppgradera din kod för att använda en ny API-version. En ny version krävs endast om någon aspekt av API: et har ändrats på ett sätt som bryter bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-ytan.

Samma sak gäller för SDK-uppdateringar. Azure Search SDK följer den [semantiska versionshantering](https://semver.org/) regler, vilket innebär att dess version består av tre delar: större, mindre och build-nummer (till exempel 1.1.0). En ny högre version av SDK släpps endast för ändringar som dela bakåtkompatibilitet. Fast funktionsuppdateringar ökar värdet för den lägre versionen och felkorrigeringar endast ökar build-versionen.

> [!NOTE]
> Din Azure Search-tjänstinstans har stöd för flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när den inte längre det senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API, måste du ange API-versionen i varje begäran via parametern api-versionen. När du använder .NET SDK, avgör version av SDK: N som du använder motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

## <a name="snapshot-of-current-versions"></a>Ögonblicksbild av aktuella versioner
Nedan en ögonblicksbild av de aktuella versionerna av alla programmeringsgränssnitt till Azure Search.

| Gränssnitt | Senaste huvudversion | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Allmänt tillgänglig, är April 2018 |
| [Förhandsversion av .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Förhandsgranskning, publicerat maj 2017 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Allmänt tillgänglig |
| [Tjänsten REST API-2017-11-11-förhandsversion](search-api-2017-11-11-preview.md) |2017-11-11-Preview |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Allmänt tillgänglig |
| [Hantering av REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allmänt tillgänglig |

För REST API: erna, inklusive den `api-version` på varje anrop krävs. Med hjälp av `api-version` gör det enkelt att fokusera på en specifik version, till exempel en förhandsversionen av API. I följande exempel visas hur `api-version` parameter har angetts:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Även om varje begäran har en `api-version`, rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner introducera attribut eller de åtgärder som inte kan identifieras av tidigare versioner. Blanda API-versioner kan ha oönskade konsekvenser och bör inte användas.
>
> Tjänsten REST API och REST-API Management är version oberoende av varandra. Alla likheter i versionsnummer är sammanhanget.

Allmänt tillgänglig (eller allmän tillgänglighet) API: er kan användas i produktion och omfattas av serviceavtal för Azure. Förhandsversioner har experimentella funktioner som inte alltid har migrerats till en GA-version. **Starkt bör du undvika att använda förhandsversionen av API: er i produktionsprogram.**

## <a name="about-preview-and-generally-available-versions"></a>Om versioner som förhandsversion och är nu allmänt tillgänglig
Azure Search Frigör alltid före experimentella funktioner via REST API först, sedan via förhandsversioner av .NET SDK.

Förhandsversionsfunktioner är tillgängliga för testning och experimentering, med målet att samla in feedback om funktionen design och implementering. Därför kan förhandsversionsfunktioner ändras med tiden, möjligen på ett sätt som bakåtkompatibilitet bryta kompatibiliteten. Detta skiljer sig från funktionerna i en GA-version som är sannolikt inte kommer att ändra med undantag för små bakåtkompatibla korrigeringar och förbättringar. Dessutom gör förhandsversionsfunktioner inte alltid det till en GA-version.

Därmed behöver rekommenderar vi mot att skriva kod som tar ett beroende på förhandsversioner. Om du använder en äldre förhandsversionen rekommenderar vi att migrera till den allmänt tillgängliga versionen (GA).

För .NET-SDK: Vägledning för migrering av kod finns på [uppgradera .NET SDK](search-dotnet-sdk-migration.md).

Allmän tillgänglighet innebär att Azure Search är nu under servicenivåavtalet (SLA). SERVICEAVTALET finns på [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
