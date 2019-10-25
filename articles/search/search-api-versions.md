---
title: 'API-versions hantering för .NET SDK och REST API: er'
titleSuffix: Azure Cognitive Search
description: 'Versions princip för Azure Kognitiv sökning REST API: er och klient biblioteket i .NET SDK.'
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a976655d0f634c7026f008d64516a629947e90c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793064"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versioner i Azure Kognitiv sökning

Azure Kognitiv sökning samlar ut funktions uppdateringar regelbundet. Ibland, men inte alltid, kräver de här uppdateringarna en ny version av API: n för att bevara bakåtkompatibilitet. Genom att publicera en ny version kan du styra när och hur du integrerar Sök tjänst uppdateringar i din kod.

Som regel publicerar Azure Kognitiv sökning-teamet nya versioner endast när det behövs, eftersom det kan omfatta viss ansträngning för att uppgradera din kod för att använda en ny API-version. En ny version krävs bara om någon aspekt av API: et har ändrats på ett sätt som bryter mot bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-Surface-områden.

Samma regel gäller för SDK-uppdateringar. Azure Kognitiv sökning SDK följer reglerna för [semantisk version](https://semver.org/) , vilket innebär att dess version har tre delar: Major, mindre och build-nummer (till exempel 1.1.0). En ny huvud version av SDK släpps bara för ändringar som bryter mot bakåtkompatibilitet. Icke-brytande funktions uppdateringar kommer att öka den lägre versionen, och fel korrigeringarna ökar bara versions versionen.

> [!NOTE]
> Din Azure Kognitiv sökning-tjänstinstans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

## <a name="snapshot-of-current-versions"></a>Ögonblicks bild av aktuella versioner
Nedan visas en ögonblicks bild av de aktuella versionerna av alla programmerings gränssnitt till Azure Kognitiv sökning.


| Intervall | Senaste huvud version | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Allmänt tillgänglig, lanserad maj 2019 |
| [För hands version av .NET SDK](https://aka.ms/search-sdk-preview) |8,0 – för hands version |För hands version, lanserad april 2019 |
| [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Allmänt tillgänglig |
| [Service REST API 2019-05-06 – för hands version](search-api-preview.md) |2019-05-06 – för hands version |Förhandsversion |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Allmänt tillgänglig |
| [Hanterings REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allmänt tillgänglig |

För REST-API: er, inklusive `api-version` för varje anrop måste anges. Med hjälp av `api-version` är det enkelt att rikta en speciell version till, till exempel ett för hands versions-API. I följande exempel visas hur `api-version`-parametern anges:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Även om varje begäran har ett `api-version`rekommenderar vi att du använder samma version för alla API-begäranden. Detta gäller särskilt när nya API-versioner introducerar attribut eller åtgärder som inte känns igen av tidigare versioner. Att blanda API-versioner kan ha oönskade konsekvenser och bör undvikas.
>
> Tjänsten REST API och hantering REST API versioner oberoende av varandra. Alla likheter i versions nummer är samincidenter.

Allmänt tillgängliga API: er (eller GA) kan användas i produktion och omfattas av Azure Service nivå avtal. För hands versioner har experimentella funktioner som inte alltid migreras till en GA-version. **Du rekommenderas starkt att undvika att använda för hands versioner av API: er i produktions program.**

## <a name="about-preview-and-generally-available-versions"></a>Om förhands granskning och allmänt tillgängliga versioner
Azure Kognitiv sökning alltid för hands versioner av experimentella funktioner via REST API först, sedan till hands versioner av .NET SDK.

För hands versions funktionerna är tillgängliga för testning och experimentering, med målet att samla in feedback om funktions design och implementering. Av den anledningen kan förhands gransknings funktioner ändras med tiden, eventuellt på sätt som bryter mot bakåtkompatibilitet. Detta är i motsats till funktioner i en GA-version, som är stabila och inte kan ändras med undantag för små bakåtkompatibla korrigeringar och förbättringar. För hands versions funktioner görs dessutom inte alltid till en GA-version.

Av dessa skäl rekommenderar vi att du skriver produktions kod som tar ett beroende på för hands versioner. Om du använder en äldre version av för hands versionen rekommenderar vi att du migrerar till den allmänt tillgängliga versionen (GA).

För .NET SDK: anvisningar för kod migrering finns i [uppgradera .NET SDK](search-dotnet-sdk-migration-version-9.md).

Allmän tillgänglighet innebär att Azure Kognitiv sökning nu omfattas av service nivå avtalet (SLA). Service avtalet finns på [Azure kognitiv sökning service avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
