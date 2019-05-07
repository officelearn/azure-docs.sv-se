---
title: 'API-versionshantering för .NET SDK och REST API: er – Azure Search'
description: 'Princip för programversion för Azure Search REST API: er och klientbiblioteket i .NET SDK.'
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 21374e20a2ba2c9134ed12e4b1072ff5c320c75e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149784"
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
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Allmänt tillgänglig, är maj 2019 |
| [Förhandsversion av .NET SDK](https://aka.ms/search-sdk-preview) |8.0.0-Preview |Förhandsversion, är April 2019 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Allmänt tillgänglig |
| [Tjänsten REST API 2019-05-06-förhandsversion](search-api-preview.md) |2019-05-06-förhandsversion |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Allmänt tillgänglig |
| [Hantering av REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allmänt tillgänglig |

För REST API: erna, inklusive den `api-version` på varje anrop krävs. Med hjälp av `api-version` gör det enkelt att fokusera på en specifik version, till exempel en förhandsversionen av API. I följande exempel visas hur `api-version` parameter har angetts:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Även om varje begäran har en `api-version`, rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner introducera attribut eller de åtgärder som inte kan identifieras av tidigare versioner. Blanda API-versioner kan ha oönskade konsekvenser och bör inte användas.
>
> Tjänsten REST API och REST-API Management är version oberoende av varandra. Alla likheter i versionsnummer är sammanhanget.

Allmänt tillgänglig (eller allmän tillgänglighet) API: er kan användas i produktion och omfattas av serviceavtal för Azure. Förhandsversioner har experimentella funktioner som inte alltid har migrerats till en GA-version. **Starkt bör du undvika att använda förhandsversionen av API: er i produktionsprogram.**

## <a name="about-preview-and-generally-available-versions"></a>Om versioner som förhandsversion och är nu allmänt tillgänglig
Azure Search Frigör alltid före experimentella funktioner via REST API först, sedan via förhandsversioner av .NET SDK.

Förhandsversionsfunktioner är tillgängliga för testning och experimentering, med målet att samla in feedback om funktionen design och implementering. Därför kan förhandsversionsfunktioner ändras med tiden, möjligen på ett sätt som bakåtkompatibilitet bryta kompatibiliteten. Detta skiljer sig från funktionerna i en GA-version som är sannolikt inte kommer att ändra med undantag för små bakåtkompatibla korrigeringar och förbättringar. Dessutom gör förhandsversionsfunktioner inte alltid det till en GA-version.

Därmed behöver rekommenderar vi mot att skriva kod som tar ett beroende på förhandsversioner. Om du använder en äldre förhandsversionen rekommenderar vi att migrera till den allmänt tillgängliga versionen (GA).

För .NET-SDK: Vägledning för migrering av kod finns på [uppgradera .NET SDK](search-dotnet-sdk-migration-version-9.md).

Allmän tillgänglighet innebär att Azure Search är nu under servicenivåavtalet (SLA). SERVICEAVTALET finns på [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
