---
title: API-versioner
titleSuffix: Azure Cognitive Search
description: 'Versions princip för Azure Kognitiv sökning REST API: er och klient biblioteket i .NET SDK.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 5be50453dff9acaf4a9876eec1d95b56abebf745
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029849"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versioner i Azure Kognitiv sökning

Azure Kognitiv sökning samlar ut funktions uppdateringar regelbundet. Ibland, men inte alltid, kräver de här uppdateringarna en ny version av API: n för att bevara bakåtkompatibilitet. Genom att publicera en ny version kan du styra när och hur du integrerar Sök tjänst uppdateringar i din kod.

Som regel publicerar Azure Kognitiv sökning-teamet nya versioner endast när det behövs, eftersom det kan omfatta viss ansträngning för att uppgradera din kod för att använda en ny API-version. En ny version krävs bara om någon aspekt av API: et har ändrats på ett sätt som bryter mot bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-Surface-områden.

Samma regel gäller för SDK-uppdateringar. Azure Kognitiv sökning SDK följer reglerna för [semantisk version](https://semver.org/) , vilket innebär att dess version har tre delar: Major, mindre och build-nummer (till exempel 1.1.0). En ny huvud version av SDK släpps bara för ändringar som bryter mot bakåtkompatibilitet. Icke-brytande funktions uppdateringar kommer att öka den lägre versionen, och fel korrigeringarna ökar bara versions versionen.

> [!Important]
> Azure-SDK: er för .NET, Java, python och Java Script tar bort nya klient bibliotek för Azure Kognitiv sökning. För närvarande har inget av Azure SDK-biblioteken fullständigt stöd för de senaste Sök REST-API: erna (2020-06-30) eller hantering REST API: er (2020-03-13), men det kommer att ändras med tiden. Du kan regelbundet kontrol lera den här sidan eller [vad som är nytt](whats-new.md) för meddelanden om funktionella förbättringar. 

## <a name="rest-apis"></a>REST API:er

En Azure Kognitiv sökning-tjänstinstans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du [migrerar din kod](search-api-migration.md) för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

Följande tabell innehåller versions historiken för aktuella och tidigare utgivna versioner av Search Service REST API. Dokumentationen publiceras bara för de senaste stabila och för hands versions versionerna.

### <a name="search-service-apis"></a>Search Service-API: er

Skapa och hantera innehåll i en Sök tjänst.

| 2.0.1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problem med bakåtkompatibilitet |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | Den senaste stabila versionen av Sök REST-API: er, med framsteg i relevans-Poäng och allmänt tillgängliga för kunskaps lager.|
| [Search 2020-06-30-förhandsversion](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Förhandsgranskning | För hands version som är associerad med stabil version. Innehåller flera för [hands versions funktioner](search-api-preview.md). |
| Sökning 2019-05-06 | Stable | Lägger till [komplexa typer](search-howto-complex-data-types.md). |
| Search 2019-05-06-Preview | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2017-11-11 | Stable  | Lägger till färdighetsuppsättningar och [AI-anrikning](cognitive-search-concept-intro.md). |
| Sök 2017-11-11 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2016-09-01 |Stable | Lägger till [indexerare](search-indexer-overview.md). |
| Sök 2016-09-01 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version.|
| Sök 2015-02-28 | Stable  | Första allmänt tillgängliga version.  |
| Sök 2015-02-28 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2014-10-20 – för hands version | Förhandsgranskning | Andra offentliga för hands versionen. |
| Sök 2014-07-31 – för hands version | Förhandsgranskning | Första offentliga för hands versionen. |

### <a name="management-rest-apis"></a>Management REST API:er

Skapa och konfigurera en Sök tjänst och hantera API-nycklar.

| 2.0.1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problem med bakåtkompatibilitet |
|-------------------------|--------|------------------------------|
| [Hantering 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Stable | Den senaste stabila versionen av hanterings REST-API: er, med framsteg i Endpoint Protection. Lägger till [privat slut punkt](service-create-private-endpoint.md) via privat länk och [nätverks-IP-regler](service-configure-firewall.md) för nya tjänster. |
| [Hantering 2019-10-01-förhandsversion](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Förhandsgranskning  | Trots dess versions nummer är det fortfarande den aktuella för hands versionen av REST-API: erna för hantering. Det finns för närvarande inga för hands versions funktioner. Alla för hands versions funktioner har nyligen övergått till allmän tillgänglighet. |
| Hantering 2015-08-19  | Stable | Den första allmänt tillgängliga versionen av hanterings REST-API: erna. Tillhandahåller tjänst etablering, skalning och hantering av API-nycklar. |
| Hantering 2015-08-19 – för hands version | Förhandsgranskning | Den första för hands versionen av hanterings REST-API: erna. |

## <a name="azure-sdk-for-net"></a>Azure SDK för .NET

Paket versions historik finns på NuGet.org. Den här tabellen innehåller länkar till varje paket sida.


| SDK-version | Status | Beskrivning |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Rikta in the search REST API-version = 2020-06-30 REST API men ännu inte stöder, geo-filter eller [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft. Azure. search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Lanserad maj 2019. Mål för search REST API-version = 2019-05-06.|
| [Microsoft. Azure. search 8,0 – för hands version](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Förhandsgranskning | lanserad 2019 april. Riktar in the search REST API-version = 2019-05 -06 – Preview.|
| [Microsoft. Azure. Management. search 3.0.0](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Rikta in the Management REST API-version = 2015-08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK för Java

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Java Azure-Search-dokument 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2019-05-06. |
| [1.35.0 för Java-hanterings klient](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Rikta in the Management REST API-version = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK för Java Script

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Java Script – Sök 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2016-09-01. |
| [Java Script-arm-search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Rikta in the Management REST API-version = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK för Python

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Python Azure-Search-dokument 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2019-05-06. |
| [Python Azure-MGMT-search 1,0](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Rikta in the Management REST API-version = 2015-08-19. |