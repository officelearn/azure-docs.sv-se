---
title: API-versioner av Azure Search | Microsoft Docs
description: 'Princip för programversion för Azure Search REST API: er och klientbiblioteket i .NET SDK.'
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.openlocfilehash: 8d1e30b0bca3c63fe4528c06e5389d8cbe27a7e6
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113613"
---
# <a name="api-versions-in-azure-search"></a>API-versioner i Azure Search
Azure Search samlar funktionen uppdateras regelbundet. Ibland, men inte alltid kräver dessa uppdateringar en ny version av API för att bevara bakåtkompatibilitet. En ny version kan du styra hur och när du integrerar tjänstuppdateringar för sökning i koden.

Som regel publicerar Azure Search-teamet nya versioner bara när det behövs, eftersom den kan omfatta möda för att uppgradera din kod för att använda en ny API-version. En ny version krävs endast om någon del av API: et har ändrats på ett sätt som bryter bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-ytan.

Samma sak gäller för SDK-uppdateringar. Azure Search SDK följer den [semantiska versionshantering](http://semver.org/) regler, vilket innebär att dess version består av tre delar: högre och lägre build-nummer (till exempel 1.1.0). En ny högre version av SDK släpps endast för ändringar som bryter bakåtkompatibilitet. Hårt funktionsuppdateringar ökar värdet för den lägre versionen och felkorrigeringar endast ökar versionsnumret.

> [!NOTE]
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST-API, måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK, anger versionen av du använder SDK motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

## <a name="snapshot-of-current-versions"></a>Ögonblicksbilden av aktuella versioner
Nedan en ögonblicksbild av de aktuella versionerna av alla programmeringsgränssnitt för Azure Search.

| Gränssnitt | Senaste huvudversion | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Allmänt tillgänglig, publicerat April 2018 |
| [Förhandsversion av .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Förhandsgranskning, publicerat maj 2017 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Allmänt tillgänglig |
| [Tjänsten REST API-2017-11-11-Preview](search-api-2017-11-11-preview.md) |2017-11-11-Preview |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Allmänt tillgänglig |
| [REST API för hantering](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allmänt tillgänglig |

För REST API: erna, inklusive den `api-version` på varje anrop krävs. Med hjälp av `api-version` gör det enkelt att fokusera på en viss version, till exempel en förhandsgranskning API. Följande exempel visar hur `api-version` parameter har angetts:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Även om varje begäran har en `api-version`, rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner införa attribut eller funktioner som inte känns igen av tidigare versioner. Blanda API-versioner kan ha oönskade konsekvenser och bör undvikas.
>
> Tjänsten REST-API och Management REST API är versionshanterade oberoende av varandra. Alla likheter i versionsnummer är sammanhanget.

Allmänt tillgänglig (eller GA) API: er som kan användas i produktion och regleras Azure servicenivåavtal. Förhandsversioner har experiment funktioner som inte alltid har migrerats till en GA-version. **Du rekommenderas starkt att undvika att använda Förhandsgranska API: er i program i produktion.**

## <a name="about-preview-and-generally-available-versions"></a>Om förhandsversionen och allmänt tillgängliga versioner
Azure Search Frigör alltid före experiment funktioner via REST API först, sedan via förhandsversioner av .NET SDK.

Funktioner är tillgängliga för testning och experiment, med målet att samla in feedback om funktionen designen och implementeringen. Därför kan förhandsgranskningsfunktionerna ändras med tiden, möjligen på ett sätt som bakåtkompatibilitet bryta kompatibiliteten. Detta skiljer sig från funktionerna i en GA-version som är sannolikt inte kommer att ändra med undantag för små bakåtkompatibla korrigeringar och förbättringar. Dessutom gör förhandsgranskningsfunktioner alltid den till en GA-version.

Därför rekommenderar vi mot skriva kod som tar ett beroende på förhandsversioner. Om du använder en äldre version av preview rekommenderar vi migrering till den allmänt tillgängliga versionen (GA).

För .NET SDK: vägledning för migrering av kod finns på [uppgradera .NET SDK](search-dotnet-sdk-migration.md).

Allmän tillgänglighet innebär att Azure Search är nu under servicenivåavtal (SLA). SLA finns på [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
