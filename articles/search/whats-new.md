---
title: Nya funktions meddelanden
titleSuffix: Azure Cognitive Search
description: Meddelanden om nya och förbättrade funktioner, inklusive tjänst byte för Azure Search till Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/10/2020
ms.openlocfilehash: 3150eed063413d0665adfc104a603f9f2b5755c3
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129179"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Vad är nytt i Azure Kognitiv sökning

Läs om vad som är nytt i tjänsten. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nytt tjänst namn

Azure Search har nu bytt namn till **Azure kognitiv sökning** för att återspegla den utökade (ännu valfria) användningen av kognitiva kunskaper och AI-bearbetning i kärn åtgärder. API-versioner, NuGet-paket, namnrymder och slut punkter är oförändrade. Nya och befintliga Sök lösningar påverkas inte av tjänst namns ändringen.

## <a name="feature-announcements"></a>Funktions aviseringar

### <a name="march-2020"></a>Mars 2020

+ Ny stabil [hanterings REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) är nu tillgängligt. 

### <a name="february-2020"></a>Februari 2020

+ [PII-identifiering (för hands version)](cognitive-search-skill-pii-detection.md) är en kognitiv färdighet som används vid indexering som extraherar personligt identifierbar information från en indata-text och ger dig möjlighet att maskera den från den texten på olika sätt.

+ [Anpassad sökning efter entitet (för hands version)](cognitive-search-skill-custom-entity-lookup.md ) söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketteras alla dokument med matchande entiteter. Kompetensen har även stöd för en viss fuzzy-matchning som kan användas för att söka efter matchningar som liknar varandra, men som inte exakt stämmer. 

### <a name="january-2020"></a>Januari 2020

+ [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) är nu allmänt tillgängliga. Om du använder REST kan du komma åt funktionen med hjälp av `api-version=2019-05-06`. För hanterad kod är rätt paket fortfarande [.NET SDK version 8,0 – förhands granskning](search-dotnet-sdk-migration-version-9.md) trots att funktionen inte är i förhands granskning. 

+ Privat åtkomst till en Sök tjänst är tillgänglig via två mekanismer, både för närvarande i för hands version:

  + Du kan begränsa åtkomsten till vissa IP-adresser med hjälp av hanterings REST API `api-version=2019-10-01-Preview` för att skapa tjänsten. För hands versions-API: et har nya egenskaper för **IpRule** och **NETWORKRULESET** i [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Den här förhands gransknings funktionen är tillgänglig i valda regioner. Mer information finns i [så här använder du hanterings REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + För närvarande tillgängligt via en begränsad till gång kan du etablera en Azure Search-tjänst som stöder Azures privata slut punkter för anslutningar från klienter i samma virtuella nätverk. Mer information finns i [skapa en privat slut punkt för en säker anslutning](service-create-private-endpoint.md).

### <a name="december-2019"></a>December 2019

+ [Skapa app (för hands version)](search-create-app-portal.md) är en ny guide i portalen som genererar en nedladdnings bar HTML-fil. Filen levereras med inbäddat skript som återger en fungerande "localhost"-webbapp som är knutet till ett index i din Sök tjänst. Sidor kan konfigureras i guiden och kan innehålla ett sökfält, resultat område, navigerings fält för navigering och stöd för typeahead-frågor. Du kan ändra HTML-koden offline för att utöka eller anpassa arbets flödet eller utseendet.

+ [Skapa en privat slut punkt för säkra anslutningar (förhands granskning)](service-create-private-endpoint.md) förklarar hur du konfigurerar en privat länk för säkra anslutningar till din Sök tjänst. Den här förhands gransknings funktionen är tillgänglig på begäran och använder [Azures privata länk](../private-link/private-link-overview.md) och [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) som en del av lösningen.

### <a name="november-2019---ignite-conference"></a>2019 november-inbrännings konferens

+ [Stegvis anrikning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) lägger till cachelagring och statefullness i en anriknings pipeline så att du kan arbeta med vissa steg eller faser utan att förlora innehåll som redan har bearbetats. Tidigare krävde alla ändringar i en anriknings pipeline en fullständig återuppbyggnad. Med stegvis anrikning bevaras utdata från kostsam analys, särskilt bild analys.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrahering av dokument (för hands version)](cognitive-search-skill-document-extraction.md) är en kognitiv kunskap som används vid indexering och som gör att du kan extrahera innehållet i en fil från en färdigheter. Tidigare inträffade endast dokument sprickor innan färdigheter kördes. Med att lägga till den här kunskapen kan du också utföra den här åtgärden i färdigheter-körning.

+ [Översättning av text](cognitive-search-skill-text-translation.md) är en kognitiv kunskap som används vid indexering som utvärderar text och, för varje post, returnerar texten översatt till det angivna mål språket.

+ [Power BI mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kan rivstart med visualiseringar och analys av berikat innehåll i ett kunskaps lager på Power BI Desktop. Den här mallen är utformad för Azure Table-projektioner som skapats via [guiden Importera data](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (för hands version)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (för hands version)](search-howto-index-cosmosdb.md)och [Cosmos DB API för Cassandra (för hands version)](search-howto-index-cosmosdb.md) stöds nu i indexerare. Du kan registrera dig med [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du får ett bekräftelse meddelande när du har accepterat i för hands versions programmet.

### <a name="july-2019"></a>Juli 2019

+ Allmänt tillgänglig i [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Tjänstuppdateringar

[Tjänst uppdaterings meddelanden](https://azure.microsoft.com/updates/?product=search&status=all) för Azure kognitiv sökning finns på Azure-webbplatsen.