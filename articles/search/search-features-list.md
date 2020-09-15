---
title: Översikt över funktioner
titleSuffix: Azure Cognitive Search
description: Utforska funktions kategorierna i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 540936ac190e981ff786ea90a91516161346d65c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533102"
---
# <a name="features-of-azure-cognitive-search"></a>Funktioner i Azure Kognitiv sökning

Azure Kognitiv sökning tillhandahåller en full texts öknings motor, beständig lagring av Sök index, integrerad AI som används under indexeringen för att extrahera mer text och struktur, samt API: er och verktyg. I följande tabell sammanfattas funktionerna efter kategori. Mer information om hur Kognitiv sökning jämför med andra Sök tekniker finns i [Vad är Azure kognitiv sökning?](search-what-is-azure-search.md).

## <a name="indexing-features"></a>Indexerings funktioner

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
| Datakällor | Azure Kognitiv sökning-index accepterar data från vilken källa som helst, förutsatt att de skickas som en JSON-datastruktur. <br/><br/> [**Indexerare**](search-indexer-overview.md) automatiserar data inmatning för Azure-datakällor som stöds och hanterar JSON-serialisering. Anslut till [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)eller [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) för att extrahera sökbart innehåll i primära data lager. Azure Blob-indexerare kan utföra *dokumentknäckning* för [extrahering av text från större filformat](search-howto-indexing-azure-blob-storage.md), bland annat Microsoft Office-, PDF- och HTML-dokument. |
| Hierarkiska och kapslade data strukturer | Med [**komplexa typer**](search-howto-complex-data-types.md) och samlingar kan du modellera i stort sett alla typer av JSON-struktur som ett Azure kognitiv sökning-index. En-till-många och många-till-många-kardinalitet kan uttryckas internt via samlingar, komplexa typer och samlingar av komplexa typer.|
| Språklig analys | Analysverktyg är komponenter som används för textbearbetning under indexerings- och sökåtgärder. Det finns två typer. <br/><br/>[**Anpassad lexikalisk analys**](index-add-custom-analyzers.md) används för komplexa sökfrågor med fonetisk matchning och reguljära uttryck. <br/><br/>[**Språkanalys**](index-add-language-analyzers.md) från Lucene eller Microsoft används för intelligent hantering av språkspecifik lingvistik, bland annat verbtempus, genus, substantiv med oregelbunden plural (till exempel 'mus' kontra 'möss'), uppdelning av sammansatta ord, ordseparation (för språk utan blanksteg) och mycket mer. <br/><br/>|

## <a name="ai-enrichment-and-knowledge-mining"></a>AI-anrikning och kunskaps utvinning

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
|AI-bearbetning under indexering | [**AI-anrikning**](cognitive-search-concept-intro.md) för bild-och text analyser kan tillämpas på en indexerings pipeline för att extrahera text information från RAW-innehåll. Några exempel på [inbyggda kunskaper](cognitive-search-predefined-skills.md) inkluderar optisk teckenläsning (att göra skannad JPEG sökbar), entitetsigenkänning (identifierar en organisation, namn eller plats) och nyckelfrasigenkänning. Du kan också [koda anpassade kunskaper](cognitive-search-create-custom-skill-example.md) att ansluta till pipelinen. Du kan också [integrera Azure Machine Learning skapade färdigheter](./cognitive-search-tutorial-aml-custom-skill.md). |
| Lagra berikat innehåll för analys och konsumtion i icke-sökscenarier | [**Kunskaps lager**](knowledge-store-concept-intro.md) är ett tillägg till AI-baserad indexering. Med Azure Storage som en server del kan du spara berikade som skapats under indexeringen. Dessa artefakter kan användas för att utforma bättre färdighetsuppsättningar eller skapa form och struktur av amorphous eller tvetydiga data. Du kan skapa projektioner av dessa strukturer som riktar sig mot specifika arbets belastningar eller användare. Du kan också analysera extraherade data direkt eller läsa in dem i andra appar.<br/><br/> |
| Cachelagrat innehåll | Den [**stegvisa anrikningen (för hands version)**](cognitive-search-incremental-indexing-conceptual.md) begränsar bearbetningen till enbart de dokument som ändras av en viss redigering till pipelinen med hjälp av cachelagrat innehåll för de delar av pipelinen som inte ändras. |

## <a name="query-and-user-experience"></a>Fråge-och användar upplevelse

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
|Formaliafri textsökning | [**Full texts ökning**](search-lucene-query-architecture.md) är ett primärt användnings fall för de flesta Sök-baserade appar. Frågor kan formuleras med en syntax som stöds. <br/><br/>[**Enkel frågesyntax**](query-simple-syntax.md) innehåller logiska operatorer, frassökoperatorer, suffixoperatorer och prioritetsoperatorer.<br/><br/>[**Lucene-frågesyntax**](query-lucene-syntax.md) innehåller alla åtgärder i enkel syntax, med tillägg för fuzzy-sökning, närhetssökning, termförstärkning och reguljära uttryck.|
| Relevans | [**Enkel Poäng**](index-add-scoring-profiles.md) är en viktig fördel med Azure kognitiv sökning. Bedömningsprofiler används för att modellera relevans som en funktion av värden i själva dokumenten. Du kanske till exempel vill att nyare produkter eller rabatterade produkter ska visas högre upp i sökresultaten. Du kan också skapa bedömningsprofiler med hjälp av taggar för anpassad bedömning baserat på kunders sökinställningar som du har spårat och lagrat separat. |
| Geo-sökning | Azure Kognitiv sökning bearbetar, filtrerar och visar geografiska platser. Det gör det möjligt för användare att utforska data baserat på ett sökresultats närhet till en fysisk plats. [Titta på den här videon](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) eller [gå igenom det här exemplet](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) om du vill veta mer. |
| Filter och ansikts | [**Aspektbaserad navigering**](search-faceted-navigation.md) aktiveras genom en enda frågeparameter. Azure Kognitiv sökning returnerar en fasett-navigerings struktur som du kan använda som kod bakom en kategori lista, för självdirigerad filtrering (till exempel för att filtrera katalog objekt efter pris intervall eller varumärke). <br/><br/> [**Filter**](query-odata-filter-orderby-syntax.md) kan användas för att införliva aspektbaserad navigering i programmets användargränssnitt, förbättra frågeformulering och filtrera baserat på användar- eller utvecklarangivna villkor. Skapa filter med OData-syntax. |
| Användarupplevelse | [**Funktionen Komplettera automatiskt**](search-autocomplete-tutorial.md) kan aktive ras för typ frågor i ett Sök fält. <br/><br/>[**Sökförslag**](/rest/api/searchservice/suggesters) fungerar även för delar av inmatad text i sökfält, men resultaten är faktiska dokument i indexet snarare än frågetermer. <br/><br/>[**Synonymer**](search-synonyms.md) associerar ekvivalenta termer som implicit utökar frågans omfattning utan att användaren behöver ange de alternativa termerna. <br/><br/>[**Träffmarkering**](/rest/api/searchservice/Search-Documents) tillämpar textformatering på ett matchande nyckelord i sökresultat. Du kan välja vilka fält som ska returnera markerade fragment.<br/><br/>[**Sortering**](/rest/api/searchservice/Search-Documents) erbjuds för flera fält via indexschemat och växlas sedan vid frågetid med en enda sökparameter.<br/><br/> [**Växling**](search-pagination-page-layout.md) och begränsning av Sök resultatet är enkelt med den finjusterade kontroll som Azure kognitiv sökning erbjuder över dina Sök resultat.  <br/><br/>|

## <a name="security-features"></a>Säkerhetsfunktioner

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
| Datakryptering | [**Microsoft-Managed Encrypted Encryption-at-rest**](search-security-overview.md#encrypted-transmissions-and-storage) är inbyggt i det interna lagrings lagret och är oåterkalleligt. <br/><br/>[**Kundhanterade krypterings nycklar**](search-security-manage-encryption-keys.md) som du skapar och hanterar i Azure Key Vault kan användas för kompletterande kryptering av index och synonym mappningar. För tjänster som skapats efter 1 2020 augusti utökas CMK-kryptering till data på temporära diskar för fullständig dubbel kryptering av indexerat innehåll.|
| Endpoint Protection | Med [**IP-regler för inkommande brand Väggs stöd**](service-configure-firewall.md) kan du konfigurera IP-intervall som Sök tjänsten ska ta emot begär Anden för.<br/><br/>[**Skapa en privat slut punkt**](service-create-private-endpoint.md) med hjälp av en privat Azure-länk för att tvinga alla begär Anden via ett virtuellt nätverk. |
| Utgående säkerhet (indexerare) | [**Med data åtkomst via privata slut punkter**](search-indexer-howto-access-private.md) kan en indexerare ansluta till Azure-resurser som skyddas via en privat Azure-länk.<br/><br/>[**Data åtkomst med en betrodd identitet**](search-howto-managed-identities-data-sources.md) innebär att anslutnings strängar till externa data källor kan utesluta användar namn och lösen ord. När en indexerare ansluter till data källan tillåter resursen anslutningen om Sök tjänsten tidigare har registrerats som en betrodd tjänst. |

## <a name="portal-features"></a>Portal funktioner

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
| Verktyg för prototyper och inspektion | [**Guiden Importera data**](search-import-data-portal.md) skapar index, indexerare, färdighetsuppsättningar och definitioner av data källor. <br/><br/>[**Sök Utforskaren**](search-explorer.md) används för att testa frågor och förfina bedömnings profiler.<br/><br/>[**Skapa demo-app**](search-create-app-portal.md) används för att generera en HTML-sida som kan användas för att testa Sök funktionen.  |
| Övervakning och diagnostik | [**Aktivera övervakningsfunktionerna**](search-monitor-usage.md) för att gå bortom måtten – vid en snabb skärm som alltid är synliga i portalen. Mått för frågor per sekund, svarstid och begränsning fångas in och rapporteras på portalsidor utan att ytterligare konfiguration krävs.|

## <a name="programmability"></a>Programmerbarhet

| Kategori&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
| REST | [**Tjänst REST API**](https://docs.microsoft.com/rest/api/searchservice/) är för data Plans åtgärder, inklusive alla åtgärder relaterade till indexering, frågor och AI-anrikning. Du kan också använda det här klient biblioteket för att hämta system information och statistik. <br/><br/>[**Hanterings REST API**](https://docs.microsoft.com/rest/api/searchmanagement/) för att skapa och rensa tjänster genom Azure Resource Manager. Du kan också använda det här API: et för att hantera nycklar och tillhandahålla en tjänst.|
| Azure SDK för .NET | [**Azure.Search.Documents**](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme) är för data Plans åtgärder, inklusive alla åtgärder relaterade till indexering, frågor och AI-anrikning. Du kan också använda det här klient biblioteket för att hämta system information och statistik. <br/><br/>[**Microsoft. Azure. Management. search**](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search) för att skapa och rensa tjänster genom att Azure Resource Manager. Du kan också använda det här API: et för att hantera nycklar och tillhandahålla en tjänst.|
| Azure SDK för Java | [**com.azure.search.documents**](https://docs.microsoft.com/java/api/com.azure.search.documents) är för data Plans åtgärder, inklusive alla åtgärder relaterade till indexering, frågor och AI-anrikning. Du kan också använda det här klient biblioteket för att hämta system information och statistik. <br/><br/>[**com. Microsoft. Azure. Management. search**](https://docs.microsoft.com/java/api/overview/azure/search/management) används för att skapa och rensa tjänster genom att Azure Resource Manager. Du kan också använda det här API: et för att hantera nycklar och tillhandahålla en tjänst.|
| Azure SDK för Python | [**Azure-Search-dokument**](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme) är för data Plans åtgärder, inklusive alla åtgärder relaterade till indexering, frågor och AI-anrikning. Du kan också använda det här klient biblioteket för att hämta system information och statistik. <br/><br/>[**Azure-MGMT – Sök**](https://docs.microsoft.com/python/api/overview/azure/search/management) är till för att skapa och rensa tjänster genom att Azure Resource Manager. Du kan också använda det här API: et för att hantera nycklar och tillhandahålla en tjänst. |
| Azure SDK för Java Script/TypeScript | [**Azure/Search-dokument**](https://docs.microsoft.com/javascript/api/@azure/search-documents/) är för data Plans åtgärder, inklusive alla åtgärder relaterade till indexering, frågor och AI-anrikning. Du kan också använda det här klient biblioteket för att hämta system information och statistik. <br/><br/>[**Azure/arm – Sök**](https://docs.microsoft.com/javascript/api/@azure/arm-search/) tjänsten är till för att skapa och rensa genom att Azure Resource Manager. Du kan också använda det här API: et för att hantera nycklar och tillhandahålla en tjänst. |

## <a name="see-also"></a>Se även

+ [Vad är nytt i Kognitiv sökning](whats-new.md)

+ [Förhands gransknings funktioner i Kognitiv sökning](search-api-preview.md)