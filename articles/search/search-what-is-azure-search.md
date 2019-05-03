---
title: Vad är Azure Search-tjänsten – Azure Search
description: Azure Search är en fullständigt hanterad värdbaserad molnsöktjänst från Microsoft. Läs funktionsbeskrivningar, gå igenom ett utvecklingsarbetsflöde, jämför Azure Search med andra sökprodukter från Microsoft och få hjälp med att komma igång.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 797d0cdb8d164955991de321d8df22e43f5c6a40
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024328"
---
# <a name="what-is-azure-search"></a>Vad är Azure Search?
Azure Search är en molnlösning för sökning som en tjänst 0som ger utvecklare API:er och verktyg för att lägga till omfattande sökfunktioner för privat heterogent innehåll i webb-, mobil- och företagsprogram. Frågor körs över ett användardefinierat index.

+ Bygga ett sökindex som innehåller bara dina data, kommer från flera typer av innehåll och plattformar. 

+ Utnyttja AI enrichments att extrahera text och funktioner från bildfiler, eller entiteter och nyckelfraser från rå text.

+ Skapa intuitiva sökupplevelser med aspekten navigering och filter, synonymer, automatisk komplettering och text analys för ”Menade du” autocorrected söktermer. Hämta relevans justering via funktioner och öka logik.

+ Skapa sökappar för specifika användningsfall. GEO-sökning har stöd för en ”hitta en nära mig”-upplevelse. Flerspråkig sökning stöds språkanalysverktyg för icke-engelska fulltextsökning.

Funktionerna exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET-SDK](search-howto-dotnet-sdk.md) som maskerar den inbyggda komplexiteten i informationshämtning. Förutom API:er tillhandahåller Azure Portal stöd för administration och innehållshantering, med verktyg för indexprototyper och -frågor. Eftersom tjänsten körs i molnet hanteras infrastruktur och tillgänglighet av Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Funktionsbeskrivning

| Core&nbsp;search&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
|Formaliafri textsökning | [**Fulltextsökning** ](search-lucene-query-architecture.md) är ett primärt användningsfall för de flesta Sökbaserade appar. Frågor kan formuleras med en syntax som stöds. <br/><br/>[**Enkel frågesyntax**](query-simple-syntax.md) innehåller logiska operatorer, frassökoperatorer, suffixoperatorer och prioritetsoperatorer.<br/><br/>[**Lucene-frågesyntax**](query-lucene-syntax.md) innehåller alla åtgärder i enkel syntax, med tillägg för fuzzy-sökning, närhetssökning, termförstärkning och reguljära uttryck.|
| Relevans | [**Enkel bedömning**](index-add-scoring-profiles.md) är en viktig fördel med Azure Search. Bedömningsprofiler används för att modellera relevans som en funktion av värden i själva dokumenten. Du kanske till exempel vill att nyare produkter eller rabatterade produkter ska visas högre upp i sökresultaten. Du kan också skapa bedömningsprofiler med hjälp av taggar för anpassad bedömning baserat på kunders sökinställningar som du har spårat och lagrat separat. |
| Geo-sökning | Azure Search bearbetar, filtrerar och visar geografiska platser. Det gör det möjligt för användare att utforska data baserat på ett sökresultats närhet till en fysisk plats. [Titta på den här videon](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) eller [gå igenom det här exemplet](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) om du vill veta mer. |
| Filter och fasetter | [**Aspektbaserad navigering**](search-faceted-navigation.md) aktiveras genom en enda frågeparameter. Azure Search returnerar en aspektbaserad navigeringsstruktur som du kan använda som koden bakom en lista med kategorier, för automatiskt dirigerad filtrering (till exempel för filtrering av katalogobjekt efter prisintervall eller varumärke). <br/><br/> [**Filter**](query-odata-filter-orderby-syntax.md) kan användas för att införliva aspektbaserad navigering i programmets användargränssnitt, förbättra frågeformulering och filtrera baserat på användar- eller utvecklarangivna villkor. Skapa filter med OData-syntax. |
| Funktioner för användarupplevelse | [**Automatisk komplettering** ](search-autocomplete-tutorial.md) kan aktiveras för frågeifyllningar i ett sökfält. <br/><br/>[**Sökförslag**](https://docs.microsoft.com/rest/api/searchservice/suggesters) fungerar även för delar av inmatad text i sökfält, men resultaten är faktiska dokument i indexet snarare än frågetermer. <br/><br/>[**Synonymer**](search-synonyms.md) associerar ekvivalenta termer som implicit utökar frågans omfattning utan att användaren behöver ange de alternativa termerna. <br/><br/>[**Träffmarkering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) tillämpar textformatering på ett matchande nyckelord i sökresultat. Du kan välja vilka fält som ska returnera markerade fragment.<br/><br/>[**Sortering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) erbjuds för flera fält via indexschemat och växlas sedan vid frågetid med en enda sökparameter.<br/><br/> [**Växling**](search-pagination-page-layout.md) och begränsning av sökresultat är enkelt med den finjusterade kontroll över sökresultaten som Azure Search erbjuder.  <br/><br/>|

| AI&nbsp;berikande&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funktioner |
|-------------------|----------|
|AI som berikats dokument | [**Cognitive Search** ](cognitive-search-concept-intro.md) för bild- och analys kan tillämpas på en pipeline för fulltextindexering att extrahera textinformation från rådata innehåll. Några exempel på [inbyggda kunskaper](cognitive-search-predefined-skills.md) inkluderar optisk teckenläsning (att göra skannad JPEG sökbar), entitetsigenkänning (identifierar en organisation, namn eller plats) och nyckelfrasigenkänning. Du kan också [koda anpassade kunskaper](cognitive-search-create-custom-skill-example.md) att ansluta till pipelinen. |
| Lagrade enrichments för analys och förbrukning| [**Knowledge Store** ](knowledge-store-concept-intro.md) är en utökning av AI-baserade indexering. Med Azure storage som serverdel kan spara du enrichments som skapades under indexering. Dessa artefakter kan användas för att hjälpa dig att utforma bättre kunskaper eller skapa form och struktur ut från amorft eller är tvetydig. Du kan skapa projektioner av dessa strukturer som mål specifika arbetsbelastningar eller användare. Du kan också direkt analysera extraherade data eller läsa in dem i andra appar.<br/><br/> |

| Data&nbsp;import/indexering | Funktioner |
|----------------------------------|----------|
| Datakällor | Azure Search-index accepterar data från alla källor, förutsatt att de skickas som en JSON-datastruktur. <br/><br/> [**Indexerare** ](search-indexer-overview.md) automatisera datainmatning för Azure-datakällor och referensen JSON-serialisering stöds. Ansluta till [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), eller [Azure Blob storage](search-howto-indexing-azure-blob-storage.md) att extrahera sökbart innehåll i primära datalager. Azure Blob-indexerare kan utföra *dokumentknäckning* för [extrahering av text från större filformat](search-howto-indexing-azure-blob-storage.md), bland annat Microsoft Office-, PDF- och HTML-dokument. |
| Hierarkisk och kapslade datastrukturer | [**Komplexa typer** ](search-howto-complex-data-types.md) och samlingar kan du modellera i stort sett alla typer av JSON-strukturen som ett Azure Search-index. En-till-många- och många-till-många kardinalitet kan uttryckas internt via samlingar, komplexa typer och samlingar med komplexa typer.|
| Språklig analys | Analysverktyg är komponenter som används för textbearbetning under indexerings- och sökåtgärder. Det finns två typer. <br/><br/>[**Anpassad lexikalisk analys**](index-add-custom-analyzers.md) används för komplexa sökfrågor med fonetisk matchning och reguljära uttryck. <br/><br/>[**Språkanalys**](index-add-language-analyzers.md) från Lucene eller Microsoft används för intelligent hantering av språkspecifik lingvistik, bland annat verbtempus, genus, substantiv med oregelbunden plural (till exempel 'mus' kontra 'möss'), uppdelning av sammansatta ord, ordseparation (för språk utan blanksteg) och mycket mer. <br/><br/>|


| Plattformen&nbsp;nivå&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funktioner |
|-------------------|----------|
| Verktyg för prototyper och inspektion | I portalen kan du använda [**guiden Importera data**](search-import-data-portal.md) för att konfigurera indexerare, indexdesignern för att sätta upp ett index och [**Sökutforskaren**](search-explorer.md) för att testa frågor och förfina bedömningsprofiler. Du kan också öppna ett index om du vill visa dess schema. |
| Övervakning och diagnostik | [**Aktivera övervakningsfunktioner** ](search-monitor-usage.md) du gå bortom de mått-med ett ögonkast som alltid är synliga i portalen. Mått för frågor per sekund, svarstid och begränsning fångas in och rapporteras på portalsidor utan att ytterligare konfiguration krävs. <br/><br/>[**Söktrafikanalys** ](search-traffic-analytics.md) är ett annat alternativ för övervakning, där serversidan och klientsidan data samlas in och analyseras för att låsa upp insikter om vad användare skriver i sökrutan. |
| Kryptering på serversidan | [**Microsoft-hanterade kryptering vid vila** ](search-security-overview.md#encrypted-transmission-and-storage) har skapats i det interna lagringsskiktet och är oåterkalleligt. Du kan också komplettera standard-kryptering med [ **kundhanterad krypteringsnycklar**](search-security-manage-encryption-keys.md). Nycklar som du skapar och hanterar i Azure Key Vault används för att kryptera index och synonymen mappar i Azure Search. |
| Infrastruktur | **Plattformen med hög tillgänglighet** ger en mycket tillförlitlig söktjänst. När det skalas korrekt [erbjuder Azure Search ett serviceavtal på 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Azure Search är **fullständigt hanterat och skalbart** som en komplett lösning, och kräver ingen som helst infrastrukturhantering. Tjänsten kan skräddarsys efter dina behov genom att skala i två dimensioner för att hantera mer dokumentlagring, högre frågebelastningar eller båda.<br/><br/>|

## <a name="how-to-use-azure-search"></a>Använda Azure Search
### <a name="step-1-provision-service"></a>Steg 1: Etablera tjänsten
Du kan etablera en Azure Search-tjänst i [Azure-portalen](https://portal.azure.com/) eller via [Azure Resource Management-API:t](/rest/api/searchmanagement/). Du kan välja den kostnadsfria tjänsten som delas med andra prenumeranter, eller en [betald nivå](https://azure.microsoft.com/pricing/details/search/) som dedikerar resurser som bara används av din tjänst. För betalda nivåer kan du skala en tjänst i två dimensioner: 

- Lägg till repliker och öka kapaciteten för hantering av tunga frågebelastningar.   
- Lägg till partitioner och utöka lagringen för fler dokument. 

Genom att hantera dokumentlagring och frågeflöde separat kan du kalibrera resurser baserat på produktionskrav.

### <a name="step-2-create-index"></a>Steg 2: Skapa index
Innan du kan ladda upp sökbart innehåll måste du först definiera ett Azure Search-index. Ett index är som en databastabell som innehåller dina data och kan acceptera sökfrågor. Du definierar indexschemat som ska mappas för att återspegla strukturen för de dokument du vill söka i, ungefär som fält i en databas.

Ett schema kan skapas i Azure Portal eller programmässigt med [.NET-SDK](search-howto-dotnet-sdk.md) eller [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Steg 3: Läsa in data
När du har definierat ett index kan du ladda upp innehåll. Du kan använda en push- eller pull-modell.

Med pull-modellen hämtas data från externa datakällor. Den stöds med hjälp av *indexerare* som effektiviserar och automatiserar aspekter av datainmatning, till exempel anslutning till, läsning och serialisering av data. [Indexerare](/rest/api/searchservice/Indexer-operations) finns tillgängliga för Azure Cosmos DB, Azure SQL Database, Azure Blob Storage och SQL Server på en virtuell Azure-dator. Du kan konfigurera en indexerare för datauppdatering på begäran eller enligt schemaläggning.

Push-modellen tillhandahålls via SDK eller REST API:er, som används för att skicka uppdaterade dokument till ett index. Du kan skicka data från i princip valfri datamängd med JSON-format. Mer information om hur du läser in data finns i [Lägga till, uppdatera och ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents) och [Använda .NET-SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Steg 4: Search
När du har fyllt i ett index kan du [utfärda sökfrågor](/rest/api/searchservice/Search-Documents) till tjänstens slutpunkt med hjälp av enkla HTTP-begäranden med REST API eller .NET-SDK.

## <a name="how-it-compares"></a>Jämförelse

Kunder frågar ofta om Azure Search i jämförelse med andra sökrelaterade lösningar. I följande tabell sammanfattas viktiga skillnader.

| Jämfört med | Viktiga skillnader |
|-------------|-----------------|
|Bing | [API för webbsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) söker i indexen på Bing.com efter matchande termer som du skickar. Index skapas utifrån HTML-, XML- och annat webbinnehåll på offentliga webbplatser. [Anpassad sökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) som bygger på samma grund, erbjuder samma crawlerteknik för webbinnehållstyper, begränsat till enskilda webbplatser.<br/><br/>Azure Search söker i ett index du definierar, som fyllts med data och dokument du äger, ofta från olika källor. Azure Search har crawlerfunktioner för vissa datakällor med hjälp av [indexerare](search-indexer-overview.md), men du kan skicka valfritt JSON-dokument som överensstämmer med indexschemat till en enda konsoliderad sökbar resurs. |
|Databassökning | Flera olika databasplattformar innehåller en inbyggd sökupplevelse. SQL Server har [fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB och liknande tekniker har frågbara index. Vid utvärdering av produkter som kombinerar sökning och lagring, kan det vara svårt att avgöra vad som är bäst. Många lösningar använder båda: DBMS för lagring och Azure Search för specialiserade sökfunktioner.<br/><br/>Jämfört med sökfunktionen i DBMS så lagrar Azure Search innehåll från heterogena källor och erbjuder särskilda funktioner för textbearbetning, till exempel språkligt medveten textbearbetning (stemming, lemmatisering, ordformulär) på [56 språk](https://docs.microsoft.com/rest/api/searchservice/language-support). Det stöder också autokorrigering av felstavade ord, [synonymer](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions), [bedömningskontroller](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetter](https://docs.microsoft.com/azure/search/search-filters-facets) och [anpassad tokenisering](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Motorn för fulltextsökning](search-lucene-query-architecture.md) i Azure Search bygger på Apache Lucene, en branschstandard inom informationshämtning. Även om Azure Search kvarhåller data i form av ett inverterat index är det sällan en ersättning för verklig datalagring. För ytterligare information, se det här [foruminlägget](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Resursutnyttjande är en annan brytpunkt i den här kategorin. Indexering och vissa frågeåtgärder är ofta beräkningsmässigt intensiva. Om sökningen avlastas från DBMS till en dedikerad lösning i molnet, bevaras systemets resurser för transaktionsbearbetning. Genom att dessutom exterrnalisera sökningen kan du enkelt justera skalan utifrån frågevolymen.|
|Dedikerad söklösning | Under förutsättning att du har valt dedikerad sökning med fullspektrumfunktioner, är en slutlig kategorisk jämförelse mellan lokala lösningar eller en molntjänst. Många söktekniker erbjuder kontroll över indexerings- och frågepipelines, tillgång till mer avancerad fråge- och filtreringssyntax, kontroll över rangordning och relevans, samt funktioner för självdirigerad och intelligent sökning. <br/><br/>En molntjänst är det rätta valet om du vill ha en nyckelfärdig lösning med minimalt merarbete och underhåll, och justerbar skala. <br/><br/>Inom molnparadigmet erbjuder flera leverantörer jämförbara baslinjefunktioner, med fulltextsökning, geo-sökning och möjlighet att hantera en viss nivå av tvetydighet i sökinmatningar. Det är vanligtvis en [specialiserad funktion](#feature-drilldown) eller den övergripande enkelheten i API:er, verktyg och hantering som avgör det bästa valet. |

Bland molnproviders är Azure Search starkast för arbetsbelastningar för fulltextsökning i innehållslager och -databaser på Azure, för appar som främst förlitar sig på sökning för både informationshämtning och innehållsnavigering. 

Viktiga fördelar är:

+ Azure-dataintegrering (crawler) på indexeringslagret
+ Azure Portal för central hantering
+ Azure-skala, tillförlitlighet och tillgänglighet i världsklass
+ Språklig och anpassad analys, med analysverktyg för fulltextsökning på 56 språk
+ [Centrala funktioner som är gemensamma för sökcentriska appar](#feature-drilldown): bedömning, aspektbasering, förslag, synonymer, geo-sökning och mycket mer.

> [!Note]
> Icke-Azure-datakällor stöds fullt ut, men förlitar sig på en mer kodintensiv push-metod i stället för indexerare. Med hjälp av API:er kan du skicka valfri JSON-dokumentsamling till ett Azure Search-index.

Bland våra kunder är de som kan dra nytta av det bredaste utbudet av funktioner i Azure Search bland annat onlinekataloger, affärsprogram och program för dokumentidentifiering.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Även om många uppgifter kan utföras i portalen är Azure Search avsett för utvecklare som vill integrera sökfunktioner i befintliga program. Följande programmeringsgränssnitt är tillgängliga.

|Plattform |Beskrivning |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-kommandon som stöds av alla programmeringsplattformar och -språk, bland annat Xamarin, Java och JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-omslutning för REST API erbjuder effektiv kodning i C# och andra språk med förvaltad kod med .NET Framework som mål |

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Azure-prenumeranter kan [etablera en tjänst på den kostnadsfria nivån](search-create-service-portal.md).

Om du inte är prenumerant kan du [öppna ett Azure-konto utan kostnad](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Du får krediter för att prova Azure-betaltjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster. 

## <a name="how-to-get-started"></a>Så här kommer du igång

1. Skapa en [kostnadsfri tjänst](search-create-service-portal.md). Alla snabbstarter och självstudier kan utföras med den kostnadsfria tjänsten.

2. Gå igenom [självstudien om hur du använder inbyggda verktyg för indexering och frågar](search-get-started-portal.md). Lär dig viktiga begrepp och bekanta dig med informationen som är tillgänglig på portalen.

3. Gå vidare med kod med .NET eller REST-API:et:

   + [Använda .NET SDK](search-howto-dotnet-sdk.md) beskriver huvudarbetsflödet i förvaltad kod.  
   + I [Komma igång med REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) visas samma steg med REST API. Du kan också använda den här snabbstarten för att anropa REST-API:er från Postman eller Fiddler: [Utforska REST-API:er för Azure Search](search-fiddler.md).

## <a name="watch-this-video"></a>Titta på den här videon

Sökmotorer är vanliga för informationshämtning i mobilappar, på webben och i företags datalager. Azure Search ger dig verktyg för att skapa en sökfunktion som liknar funktionerna på stora kommersiella webbplatser.

I den här 9-minutersvideon från programansvarige Liam Cavanagh får du reda på hur integrering av en sökmotor kan vara till nytta för din app. Korta demonstrationer tar upp viktiga funktioner i Azure Search och hur ett vanligt arbetsflöde ser ut. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuter omfattar viktiga funktioner och användningsfall.
+ 3-4 minuter omfattar tjänstetablering. 
+ 4-6 minuter omfattar guiden Importera data som används för att skapa ett index med hjälp av den inbyggda datamängden för fastigheter.
+ 6-9 minuter omfattar Sökutforskaren och olika frågor.
