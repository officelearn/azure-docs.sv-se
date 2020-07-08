---
title: API-versioner
titleSuffix: Azure Cognitive Search
description: 'Versions princip för Azure Kognitiv sökning REST API: er och klient biblioteket i .NET SDK.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830102"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versioner i Azure Kognitiv sökning

Azure Kognitiv sökning samlar ut funktions uppdateringar regelbundet. Ibland, men inte alltid, kräver de här uppdateringarna en ny version av API: n för att bevara bakåtkompatibilitet. Genom att publicera en ny version kan du styra när och hur du integrerar Sök tjänst uppdateringar i din kod.

Som regel publicerar Azure Kognitiv sökning-teamet nya versioner endast när det behövs, eftersom det kan omfatta viss ansträngning för att uppgradera din kod för att använda en ny API-version. En ny version krävs bara om någon aspekt av API: et har ändrats på ett sätt som bryter mot bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-Surface-områden.

Samma regel gäller för SDK-uppdateringar. Azure Kognitiv sökning SDK följer reglerna för [semantisk version](https://semver.org/) , vilket innebär att dess version har tre delar: Major, mindre och build-nummer (till exempel 1.1.0). En ny huvud version av SDK släpps bara för ändringar som bryter mot bakåtkompatibilitet. Icke-brytande funktions uppdateringar kommer att öka den lägre versionen, och fel korrigeringarna ökar bara versions versionen.

> [!NOTE]
> En Azure Kognitiv sökning-tjänstinstans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

## <a name="rest-apis"></a>REST API:er

Den här tabellen innehåller versions historiken för aktuella och tidigare utgivna versioner av Search Service REST API. Dokumentationen publiceras för de aktuella stabila och för hands versionerna.

| 2.0.1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problem med bakåtkompatibilitet |
|-------------|--------|------------------------------|
| [Hantering 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Allmänt tillgänglig | Den senaste stabila versionen av hanterings REST-API: er, med framsteg i Endpoint Protection. Lägger till privat slut punkt, stöd för privat länk och nätverks regler för nya tjänster. |
| [Hantering 2019-10-01-förhandsversion](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Förhandsgranskning  | Trots dess versions nummer är det fortfarande den aktuella för hands versionen av REST-API: erna för hantering. Det finns för närvarande inga för hands versions funktioner. Alla för hands versions funktioner har nyligen övergått till allmän tillgänglighet. |
| Hantering 2015-08-19  | Stable| Den första allmänt tillgängliga versionen av hanterings REST-API: erna. Tillhandahåller tjänst etablering, skalning och hantering av API-nycklar. |
| Hantering 2015-08-19 – för hands version | Förhandsgranskning| Den första för hands versionen av hanterings REST-API: erna. |
| [Search 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | Senaste stabila lanseringen av REST-API: er för Sök sökning, med framsteg i relevans-poängen. |
| [Search 2020-06-30-förhandsversion](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sökning 2019-05-06 | Stable | Lägger till komplexa typer. |
| Search 2019-05-06-Preview | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2017-11-11 | Stable  | Lägger till färdighetsuppsättningar och AI-anrikning. |
| Sök 2017-11-11 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2016-09-01 |Stable | Lägger till indexerare|
| Sök 2016-09-01 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version.|
| Sök 2015-02-28 | Stable  | Första allmänt tillgängliga version.  |
| Sök 2015-02-28 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2014-10-20 – för hands version | Förhandsgranskning | Andra offentliga för hands versionen. |
| Sök 2014-07-31 – för hands version | Förhandsgranskning | Första offentliga för hands versionen. |

## <a name="azure-sdk-for-net"></a>Azure SDK för .NET

Paket versions historik finns på NuGet.org. Den här tabellen innehåller länkar till varje paket sida.

| SDK-version | Status | Beskrivning |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0 – för hands version. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Förhandsgranskning | Nytt klient bibliotek från Azure .NET SDK, lanserat maj 2020. Riktar sig till REST 2020-06-30 API-versionen|
| [**Microsoft. Azure. search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Allmänt tillgänglig, lanserad maj 2019. Riktar sig till REST 2019-05-06 API-versionen.|
| [**Microsoft. Azure. search 8,0 – för hands version**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | För hands version, lanserad april 2019. Riktar sig till REST 2019-05-06-Preview API-versionen.|
| [**Microsoft. Azure. Management. search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Rikta in the Management REST API-version = 2015-08-19. |

## <a name="azure-sdk-for-java"></a>Azure SDK för Java

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient-1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Rikta in the Management REST API-version = 2015-08-19.|

## <a name="azure-sdk-for-python"></a>Azure SDK för Python

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [**Python Azure-MGMT-search 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Rikta in the Management REST API-version = 2015-08-19. |