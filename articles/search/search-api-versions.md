---
title: API-versioner av Azure Search | Microsoft Docs
description: 'Princip för programversion för Azure Search REST API: er och klientbiblioteket i .NET SDK.'
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3235813708b3c097e7f66610796f75eafd06aa05
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="api-versions-in-azure-search"></a>API-versioner i Azure Search
Azure Search samlar funktionen uppdateras regelbundet. Ibland, men inte alltid kräver dessa uppdateringar oss att publicera en ny version av våra API för att bevara bakåtkompatibilitet. En ny version kan du styra hur och när du integrerar tjänstuppdateringar för sökning i koden.

Som regel som vi försöker publicera nya versioner bara när det behövs, eftersom den kan omfatta möda för att uppgradera din kod för att använda en ny API-version. Vi bara publicerar en ny version om vi behöver ändra vissa delar av API: et på ett sätt som bryter bakåtkompatibilitet. Detta kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-ytan.

Vi följer samma regel för SDK-uppdateringar. Azure Search SDK följer den [semantiska versionshantering](http://semver.org/) regler, vilket innebär att dess version består av tre delar: högre och lägre build-nummer (till exempel 1.1.0). Vi kommer att släppa en ny högre version av SDK endast vid ändringar som bryter bakåtkompatibilitet. Vi kommer att öka den lägre versionen för hårt funktionsuppdateringar och för felkorrigeringar vi bara ökar versionsnumret.

> [!NOTE]
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST-API, måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK, anger versionen av du använder SDK motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

## <a name="snapshot-of-current-versions"></a>Ögonblicksbilden av aktuella versioner
Nedan en ögonblicksbild av de aktuella versionerna av alla programmeringsgränssnitt för Azure Search.

| Gränssnitt | Senaste huvudversion | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Allmänt tillgänglig, publicerat April 2018 |
| [Förhandsversion av .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Förhandsgranskning, publicerat maj 2017 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Allmänt tillgänglig |
| [Tjänsten REST API-förhandsgranskning](search-api-2016-09-01-preview.md) |2016-09-01-preview |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Allmänt tillgänglig |
| [REST API för hantering](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allmänt tillgänglig |

För REST API: erna, inklusive den `api-version` på varje anrop krävs. Detta gör det enkelt att fokusera på en viss version, till exempel en förhandsgranskning API. Följande exempel visar hur `api-version` parameter har angetts:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Även om varje begäran har en `api-version`, rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner införa attribut eller funktioner som inte känns igen av tidigare versioner. Blanda API-versioner kan ha oönskade konsekvenser och bör undvikas.
>
> Tjänsten REST-API och Management REST API är versionshanterade oberoende av varandra. Alla likheter i versionsnummer är sammanhanget.

Allmänt tillgänglig (eller GA) API: er som kan användas i produktion och regleras Azure servicenivåavtal. Förhandsversioner har experiment funktioner som inte alltid har migrerats till en GA-version. **Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.**

## <a name="about-preview-and-generally-available-versions"></a>Om förhandsversionen och allmänt tillgängliga versioner
Azure Search Frigör alltid före experiment funktioner via REST API först, sedan via förhandsversioner av .NET SDK.

Förhandsgranskningsfunktioner är inte säkert att migreras till en GA-version. Funktioner i en GA-versionen är anses sannolikt inte kommer att ändra med undantag för små bakåtkompatibla korrigeringar och förbättringar, är förhandsgranskningsfunktioner tillgängliga för testning och experiment, med målet att samla in feedback om funktionen designen och implementeringen.

Men eftersom förhandsgranskningsfunktionerna kan ändras, rekommenderar vi mot skriva kod som tar ett beroende på förhandsversioner. Om du använder en äldre version av preview rekommenderar vi migrering till den allmänt tillgängliga versionen (GA).

För .NET SDK: vägledning för migrering av kod finns på [uppgradera .NET SDK](search-dotnet-sdk-migration.md).

Allmän tillgänglighet innebär att Azure Search är nu under servicenivåavtal (SLA). SLA finns på [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
