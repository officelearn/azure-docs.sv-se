---
title: Nya funktionsmeddelanden
titleSuffix: Azure Cognitive Search
description: Meddelanden om nya och förbättrade funktioner, inklusive ett tjänstbyte av Azure Search till Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 475f89fc5b33948864fd83c39ee8058ab6908cad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247205"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nyheter i Azure Cognitive Search

Läs om vad som är nytt i tjänsten. Bokmärk den här sidan för att hålla dig uppdaterad med tjänsten.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nytt tjänstnamn

Azure Search har nu bytt namn till **Azure Cognitive Search** för att återspegla den utökade (men valbara) användningen av kognitiva färdigheter och AI-bearbetning i kärnåtgärder. API-versioner, NuGet-paket, namnområden och slutpunkter är oförändrade. Nya och befintliga söklösningar påverkas inte av ändringen av tjänstens namn.

## <a name="feature-announcements"></a>Funktionsmeddelanden

### <a name="march-2020"></a>Mars 2020

+ [Native blob soft delete (preview)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) innebär att Azure Blob Storage-indexeraren i Azure Cognitive Search känner igen blobbar som är i ett mjukt borttaget tillstånd och tar bort motsvarande sökdokument under indexeringen.

+ Nya stabila [REST API för hantering (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) är nu tillgängligt. 

### <a name="february-2020"></a>Februari 2020

+ [PII Detection (preview)](cognitive-search-skill-pii-detection.md) är en kognitiv färdighet som används vid indexering som extraherar personligt identifierbar information från en indatatext och ger dig möjlighet att maskera den från den texten på olika sätt.

+ [Anpassad entitetssökning (förhandsgranskning)](cognitive-search-skill-custom-entity-lookup.md ) söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketterar den alla dokument med alla matchande entiteter. Färdigheten stöder också en grad av luddig matchning som kan tillämpas för att hitta matcher som är liknande men inte riktigt exakt. 

### <a name="january-2020"></a>Januari 2020

+ [Kundhanterade krypteringsnycklar](search-security-manage-encryption-keys.md) är nu allmänt tillgängliga. Om du använder REST kan du `api-version=2019-05-06`komma åt funktionen med . För hanterad kod är rätt paket fortfarande [.NET SDK version 8.0-preview](search-dotnet-sdk-migration-version-9.md) även om funktionen inte är förhandsgranskning. 

+ Privat åtkomst till en söktjänst är tillgänglig via två mekanismer, båda för närvarande i förhandsversion:

  + Du kan begränsa åtkomsten till specifika IP-adresser genom att använda REST-API:et `api-version=2019-10-01-Preview` för hantering för att skapa tjänsten. Förhands-API:et har nya **iprule-** och **networkruleSet-egenskaper** i [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Den här förhandsgranskningsfunktionen är tillgänglig i valda områden. Mer information finns i [Så här använder du REST-API:et för hantering](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + För närvarande tillgänglig via en förhandsversion med begränsad åtkomst kan du etablera en Azure Search-tjänst som stöder Azure Private Endpoint för anslutningar från klienter i samma virtuella nätverk. Mer information finns i [Skapa en privat slutpunkt för en säker anslutning](service-create-private-endpoint.md).

### <a name="december-2019"></a>December 2019

+ [Skapa app (förhandsgranskning)](search-create-app-portal.md) är en ny guide i portalen som genererar en nedladdningsbar HTML-fil. Filen levereras med inbäddade skript som återger en fungerande "localhost"-stil webbapp, bunden till ett index på din söktjänst. Sidor kan konfigureras i guiden och kan innehålla ett sökfält, ett resultatområde, navigering i sidofältet och stöd för typeahead-frågor. Du kan ändra HTML-offline för att utöka eller anpassa arbetsflödet eller utseendet.

+ [Skapa en privat slutpunkt för säkra anslutningar (förhandsversion)](service-create-private-endpoint.md) förklarar hur du konfigurerar en privat länk för säkra anslutningar till söktjänsten. Den här förhandsversionsfunktionen är tillgänglig på begäran och använder [Azure Private Link](../private-link/private-link-overview.md) och Azure Virtual [Network](../virtual-network/virtual-networks-overview.md) som en del av lösningen.

### <a name="november-2019---ignite-conference"></a>November 2019 - Antända konferensen

+ [Inkrementell anrikning (förhandsversion)](cognitive-search-incremental-indexing-conceptual.md) lägger till cachelagring och tillståndsfullhet i en anrikningspipeline så att du kan arbeta med specifika steg eller faser utan att förlora innehåll som redan har bearbetats. Tidigare krävde alla ändringar av en anrikningspipeline en fullständig ombyggnad. Med inkrementell berikning bevaras produktionen av kostsam analys, särskilt bildanalys.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Dokumentextrahering (förhandsgranskning)](cognitive-search-skill-document-extraction.md) är en kognitiv färdighet som används vid indexering och som gör att du kan extrahera innehållet i en fil inifrån en kompetens. Tidigare inträffade dokumentsprickor endast före körning av kompetensuppsättningen. Med tillägget av den här färdigheten kan du också utföra den här åtgärden inom kompetensutförande.

+ [Textöversättning](cognitive-search-skill-text-translation.md) är en kognitiv färdighet som används vid indexering och som utvärderar text och returnerar texten som översatts till det angivna målspråket för varje post.

+ [Power BI-mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kan få fart på dina visualiseringar och analyser av berikat innehåll i ett kunskapslager på Power BI-skrivbordet. Den här mallen är utformad för Azure-tabellprojektioner som skapats via [guiden Importera data](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (förhandsversion)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (förhandsversion)](search-howto-index-cosmosdb.md)och [Cosmos DB Cassandra API (förhandsversion)](search-howto-index-cosmosdb.md) stöds nu i indexerare. Du kan registrera dig med [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du kommer att få en bekräftelse via e-post när du har godkänts i förhandsgranskningsprogrammet.

### <a name="july-2019"></a>Juli 2019

+ Allmänt tillgänglig i [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Tjänstuppdateringar

[Meddelanden om tjänstuppdatering](https://azure.microsoft.com/updates/?product=search&status=all) för Azure Cognitive Search finns på Azure-webbplatsen.