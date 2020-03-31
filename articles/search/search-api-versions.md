---
title: API-versionshantering för .NET och REST
titleSuffix: Azure Cognitive Search
description: Versionsprincip för AZURE Cognitive Search REST API:er och klientbiblioteket i .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137296"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versioner i Azure Cognitive Search

Azure Cognitive Search distribuerar funktionsuppdateringar regelbundet. Ibland, men inte alltid, kräver dessa uppdateringar en ny version av API:et för att bevara bakåtkompatibilitet. Genom att publicera en ny version kan du styra när och hur du integrerar söktjänstuppdateringar i koden.

Som regel publicerar Azure Cognitive Search-teamet nya versioner endast när det behövs, eftersom det kan innebära vissa ansträngningar för att uppgradera din kod för att använda en ny API-version. En ny version behövs bara om någon aspekt av API:et har ändrats på ett sätt som bryter bakåtkompatibiliteten. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintlig API-yta.

Samma regel gäller för SDK-uppdateringar. Azure Cognitive Search SDK följer de [semantiska versionsreglerna,](https://semver.org/) vilket innebär att dess version har tre delar: större, mindre och byggnummer (till exempel 1.1.0). En ny huvudversion av SDK släpps endast för ändringar som bryter bakåtkompatibilitet. Icke-brytande funktionsuppdateringar ökar delversionen och buggfixar ökar bara build-versionen.

> [!NOTE]
> Din Azure Cognitive Search-tjänstinstans stöder flera REST API-versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar koden för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK avgör den version av SDK du använder motsvarande version av REST API.When using the .NET SDK, the version of the SDK you're using determines the corresponding version of the REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas för att stödja en nyare API-version.

## <a name="snapshot-of-current-versions"></a>Ögonblicksbild av aktuella versioner
Nedan finns en ögonblicksbild av de aktuella versionerna av alla programmeringsgränssnitt till Azure Cognitive Search.


| Gränssnitt | Senaste större versionen | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Allmänt tillgänglig, släppt maj 2019 |
| [Förhandsversionen av .NET SDK](https://aka.ms/search-sdk-preview) |8.0-förhandsvisning |Preview, släppt april 2019 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Allmänt tillgängliga |
| [REST-API för tjänst 2019-05-06-förhandsversion](search-api-preview.md) |2019-05-06-Förhandsvisning |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Allmänt tillgängliga |
| [REST API för hantering](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Allmänt tillgängliga |

För REST-API:erna `api-version` krävs även på varje samtal. Med `api-version` hjälp av gör det enkelt att rikta in sig på en viss version, till exempel ett förhandsversions-API. I följande exempel visas `api-version` hur parametern anges:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Även om varje `api-version`begäran har en rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner introducerar attribut eller åtgärder som inte känns igen av tidigare versioner. Blandning API-versioner kan få oavsiktliga konsekvenser och bör undvikas.
>
> REST API och management REST API är versionsförande oberoende av varandra. Alla likheter i versionsnummer är en tillfällighet.

Allmänt tillgängliga (eller GA) API:er kan användas i produktion och omfattas av Azure-servicenivåavtal. Förhandsversioner har experimentella funktioner som inte alltid migreras till en GA-version. **Du rekommenderas starkt att undvika att använda förhandsversioner API:er i produktionsprogram.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Uppdatering till den senaste versionen av REST API senast den 15 oktober 2020
Följande versioner av AZURE Cognitive Search REST API kommer att dras tillbaka och stöds inte längre från och med den 15 oktober 2020: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**och **2015-02-28**. Dessutom kommer versioner av Azure Cognitive Search .NET SDK som är äldre än **3.0.0-rc** också att dras tillbaka eftersom de riktar sig till en av dessa REST API-versioner. Efter detta datum fungerar inte längre program som använder någon av de inaktuella REST API- eller SDK-versionerna och måste uppgraderas. Som med alla förändringar av denna typ, ger vi 12 månaders varsel, så du har tillräckligt med tid att justera.  Om du vill fortsätta använda Azure Cognitive Search migrerar du befintlig kod som riktar sig till [REST API](search-api-migration.md) till [REST API-version 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) eller nyare eller .NET SDK till [version 3.0](search-dotnet-sdk-migration.md) eller nyare senast den 15 oktober 2020.  Om du har några frågor om uppdatering till azuresearch_contact@microsoft.com den senaste versionen, vänligen skicka e-post till senast den 15 maj 2020 för att säkerställa att du har tillräckligt med tid att uppdatera koden.

## <a name="about-preview-and-generally-available-versions"></a>Om förhandsversioner och allmänt tillgängliga versioner
Azure Cognitive Search släpper alltid experimentella funktioner i förhandsversioner via REST API först och sedan via förhandsversioner av .NET SDK.

Förhandsgranskningsfunktioner är tillgängliga för testning och experiment, med målet att samla in feedback om funktionsdesign och implementering. Därför kan förhandsgranskningsfunktionerna ändras med tiden, eventuellt på ett sätt som bryter bakåtkompatibiliteten. Detta står i kontrast till funktioner i en GA-version, som är stabila och osannolikt att förändras med undantag för små bakåtkompatibla korrigeringar och förbättringar. Dessutom gör förhandsgranskningsfunktioner inte alltid det till en GA-version.

Av dessa skäl rekommenderar vi att du inte skriver produktionskod som är beroende av förhandsgranskningsversioner. Om du använder en äldre förhandsversion rekommenderar vi att du migrerar till den allmänt tillgängliga versionen (GA).

För .NET SDK: Vägledning för kodmigrering finns på [Uppgradera .NET SDK](search-dotnet-sdk-migration-version-9.md).

Allmän tillgänglighet innebär att Azure Cognitive Search nu omfattas av servicenivåavtalet (SLA). Serviceavtalet finns på [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
