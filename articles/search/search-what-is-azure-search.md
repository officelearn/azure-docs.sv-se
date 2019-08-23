---
title: Introduktion till Azure Search-Azure Search
description: Azure Search är en fullständigt hanterad Sök tjänst i molnet som tillhandahålls av Microsoft. Granska funktions beskrivningar, utvecklings arbets flödet, jämförelser med andra Microsoft Search-produkter och hur du kommer igång.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/21/2019
ms.author: heidist
ms.openlocfilehash: 204951f725c2885fe9f8bf33fffe83e55628dd34
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899690"
---
# <a name="what-is-azure-search"></a>Vad är Azure Search?

Azure Search är en moln lösning för sökning som en tjänst som tillhandahåller utvecklares API: er och verktyg för att lägga till en omfattande Sök upplevelse över privat, heterogent innehåll i webb-, mobil-och företags program. Din anpassade kod anropar data inmatning (indexering) för att skapa och läsa in ett index. På den andra sidan utfärdar din program kod frågor om begär Anden och hanterar svar. Sök funktionen definieras i-klienten med hjälp av funktioner från Azure Search, med frågekörningen över ett beständigt index som du skapar, äger och lagrar på Azure Search.

![Azure Search arkitektur](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Search arkitektur")

Funktionerna exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET-SDK](search-howto-dotnet-sdk.md) som maskerar den inbyggda komplexiteten i informationshämtning. Förutom API:er tillhandahåller Azure Portal stöd för administration och innehållshantering, med verktyg för indexprototyper och -frågor. Eftersom tjänsten körs i molnet hanteras infrastruktur och tillgänglighet av Microsoft.

## <a name="when-to-use-azure-search"></a>När du ska använda Azure Search

Azure Search passar bra för följande program scenarier:

+ Konsolidering av heterogena innehålls typer i ett privat, enskilt, sökbart index. Frågor är alltid över ett index som du skapar och läser in med dokument, och indexet finns alltid i molnet på din Azure Search-tjänst. Du kan fylla i ett index med strömmar av JSON-dokument från valfri källa eller plattform. Du kan också använda en indexerare för att hämta data till ett index för innehåll som har ursprung på Azure. Index definition och hantering/ägarskap är en viktig orsak till att använda Azure Search.

+ Enkel implementering av sökrelaterade funktioner. Azure Search-API: er fören klar frågornas konstruktion, fasett navigering, filter (inklusive geo-spatial sökning), synonym mappning, typeahead-frågor och relevans-justering. Med hjälp av inbyggda funktioner kan du tillgodose förväntningar på slutanvändare för en Sök upplevelse som liknar de kommersiella sökmotorer för Webbs ökning.

+ Indexera ostrukturerad text eller extrahera text och information från bildfiler. Funktionen [kognitiv sökning](cognitive-search-concept-intro.md) i Azure Search lägger till AI-bearbetning till en indexerings pipeline. Några vanliga användnings fall är OCR över skannade dokument, enhets igenkänning och nyckel fras extrahering över stora dokument, språk identifiering och text översättning och sentiment analys.

+ Språkliga krav uppfylls med hjälp av anpassade och språk analys verktyg för Azure Search. Om du har ett annat innehåll än engelska, stöder Azure Search både Lucene-analyser och Microsofts naturliga språk processorer. Du kan också konfigurera analys verktyg för att uppnå specialiserad bearbetning av rå data, till exempel att filtrera ut dia kritiska tecken.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Funktions beskrivningar

| Kärn&nbsp;sökning&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funktioner |
|-------------------|----------|
|Formaliafri textsökning | [**Full texts ökning**](search-lucene-query-architecture.md) är ett primärt användnings fall för de flesta Sök-baserade appar. Frågor kan formuleras med en syntax som stöds. <br/><br/>[**Enkel frågesyntax**](query-simple-syntax.md) innehåller logiska operatorer, frassökoperatorer, suffixoperatorer och prioritetsoperatorer.<br/><br/>[**Lucene-frågesyntax**](query-lucene-syntax.md) innehåller alla åtgärder i enkel syntax, med tillägg för fuzzy-sökning, närhetssökning, termförstärkning och reguljära uttryck.|
| Relevans | [**Enkel bedömning**](index-add-scoring-profiles.md) är en viktig fördel med Azure Search. Bedömningsprofiler används för att modellera relevans som en funktion av värden i själva dokumenten. Du kanske till exempel vill att nyare produkter eller rabatterade produkter ska visas högre upp i sökresultaten. Du kan också skapa bedömningsprofiler med hjälp av taggar för anpassad bedömning baserat på kunders sökinställningar som du har spårat och lagrat separat. |
| Geo-sökning | Azure Search bearbetar, filtrerar och visar geografiska platser. Det gör det möjligt för användare att utforska data baserat på ett sökresultats närhet till en fysisk plats. [Titta på den här videon](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) eller [gå igenom det här exemplet](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) om du vill veta mer. |
| Filter och ansikts | [**Aspektbaserad navigering**](search-faceted-navigation.md) aktiveras genom en enda frågeparameter. Azure Search returnerar en aspektbaserad navigeringsstruktur som du kan använda som koden bakom en lista med kategorier, för automatiskt dirigerad filtrering (till exempel för filtrering av katalogobjekt efter prisintervall eller varumärke). <br/><br/> [**Filter**](query-odata-filter-orderby-syntax.md) kan användas för att införliva aspektbaserad navigering i programmets användargränssnitt, förbättra frågeformulering och filtrera baserat på användar- eller utvecklarangivna villkor. Skapa filter med OData-syntax. |
| Funktioner för användarupplevelse | [**Funktionen Komplettera automatiskt**](search-autocomplete-tutorial.md) kan aktive ras för typ frågor i ett Sök fält. <br/><br/>[**Sökförslag**](https://docs.microsoft.com/rest/api/searchservice/suggesters) fungerar även för delar av inmatad text i sökfält, men resultaten är faktiska dokument i indexet snarare än frågetermer. <br/><br/>[**Synonymer**](search-synonyms.md) associerar ekvivalenta termer som implicit utökar frågans omfattning utan att användaren behöver ange de alternativa termerna. <br/><br/>[**Träffmarkering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) tillämpar textformatering på ett matchande nyckelord i sökresultat. Du kan välja vilka fält som ska returnera markerade fragment.<br/><br/>[**Sortering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) erbjuds för flera fält via indexschemat och växlas sedan vid frågetid med en enda sökparameter.<br/><br/> [**Växling**](search-pagination-page-layout.md) och begränsning av sökresultat är enkelt med den finjusterade kontroll över sökresultaten som Azure Search erbjuder.  <br/><br/>|

| AI&nbsp;-anrikning&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funktioner |
|-------------------|----------|
|AI-berikade dokument | [**Kognitiv sökning**](cognitive-search-concept-intro.md) efter bild-och text analyser kan tillämpas på en indexerings-pipeline för att extrahera text information från RAW-innehåll. Några exempel på [inbyggda kunskaper](cognitive-search-predefined-skills.md) inkluderar optisk teckenläsning (att göra skannad JPEG sökbar), entitetsigenkänning (identifierar en organisation, namn eller plats) och nyckelfrasigenkänning. Du kan också [koda anpassade kunskaper](cognitive-search-create-custom-skill-example.md) att ansluta till pipelinen. |
| Lagrade anrikninger för analys och konsumtion| [**Knowledge Store (för hands version)** ](knowledge-store-concept-intro.md) är ett tillägg till AI-baserad indexering. Med Azure Storage som en server del kan du spara berikare som skapats under indexering. Dessa artefakter kan användas för att utforma bättre färdighetsuppsättningar eller skapa form och struktur av amorphous eller tvetydiga data. Du kan skapa projektioner av dessa strukturer som riktar sig mot specifika arbets belastningar eller användare. Du kan också analysera extraherade data direkt eller läsa in dem i andra appar.<br/><br/> |

| Data&nbsp;import/indexering | Funktioner |
|----------------------------------|----------|
| Datakällor | Azure Search-index accepterar data från alla källor, förutsatt att de skickas som en JSON-datastruktur. <br/><br/> [**Indexerare**](search-indexer-overview.md) automatiserar data inmatning för Azure-datakällor som stöds och hanterar JSON-serialisering. Anslut till [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)eller [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) för att extrahera sökbart innehåll i primära data lager. Azure Blob-indexerare kan utföra *dokumentknäckning* för [extrahering av text från större filformat](search-howto-indexing-azure-blob-storage.md), bland annat Microsoft Office-, PDF- och HTML-dokument. |
| Hierarkiska och kapslade data strukturer | Med [**komplexa typer**](search-howto-complex-data-types.md) och samlingar kan du modellera praktiskt taget vilken typ av JSON-struktur som helst som Azure Search index. En-till-många och många-till-många-kardinalitet kan uttryckas internt via samlingar, komplexa typer och samlingar av komplexa typer.|
| Språklig analys | Analysverktyg är komponenter som används för textbearbetning under indexerings- och sökåtgärder. Det finns två typer. <br/><br/>[**Anpassad lexikalisk analys**](index-add-custom-analyzers.md) används för komplexa sökfrågor med fonetisk matchning och reguljära uttryck. <br/><br/>[**Språkanalys**](index-add-language-analyzers.md) från Lucene eller Microsoft används för intelligent hantering av språkspecifik lingvistik, bland annat verbtempus, genus, substantiv med oregelbunden plural (till exempel 'mus' kontra 'möss'), uppdelning av sammansatta ord, ordseparation (för språk utan blanksteg) och mycket mer. <br/><br/>|


| Plattforms&nbsp;nivå&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funktioner |
|-------------------|----------|
| Verktyg för prototyper och inspektion | I portalen kan du använda [**guiden Importera data**](search-import-data-portal.md) för att konfigurera indexerare, indexdesignern för att sätta upp ett index och [**Sökutforskaren**](search-explorer.md) för att testa frågor och förfina bedömningsprofiler. Du kan också öppna ett index om du vill visa dess schema. |
| Övervakning och diagnostik | [**Aktivera övervakningsfunktionerna**](search-monitor-usage.md) för att gå bortom måtten – vid en snabb skärm som alltid är synliga i portalen. Mått för frågor per sekund, svarstid och begränsning fångas in och rapporteras på portalsidor utan att ytterligare konfiguration krävs. <br/><br/>[**Sök trafik analys**](search-traffic-analytics.md) är ett annat övervaknings alternativ, där data från Server sidan och klient sidan samlas in och analyseras för att låsa upp insikter om vad användarna skriver i sökrutan. |
| Kryptering på serversidan | [**Microsoft-Managed Encrypted Encryption-at-rest**](search-security-overview.md#encrypted-transmission-and-storage) är inbyggt i det interna lagrings lagret och är oåterkalleligt. Alternativt kan du komplettera standard kryptering med Kundhanterade [**krypterings nycklar (för hands version)** ](search-security-manage-encryption-keys.md). Nycklar som du skapar och hanterar i Azure Key Vault används för att kryptera index och synonymer Maps i Azure Search. |
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
När du har fyllt i ett index kan du [utfärda Sök frågor](search-query-overview.md) till tjänstens slut punkt med hjälp av enkla HTTP-begäranden med [REST API](/rest/api/searchservice/Search-Documents) eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Stega genom att [skapa din första Sökapp](tutorial-csharp-create-first-app.md) för att skapa och utöka en webb sida som samlar in användarindata och hanterar resultat. Du kan också använda [Postman för interaktiva rest](search-get-started-postman.md) -anrop eller den inbyggda [sök Utforskaren](search-explorer.md) i Azure Portal om du vill fråga ett befintligt index.

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
+ AI-bearbetning av rå data för att göra den mer sökbar, inklusive text från bilder eller hitta mönster i ostrukturerat innehåll.
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
   + I [Komma igång med REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) visas samma steg med REST API. Du kan också använda den här snabbstarten för att anropa REST-API:er från Postman eller Fiddler: [Utforska REST-API:er för Azure Search](search-get-started-postman.md).

## <a name="watch-this-video"></a>Titta på den här videon

Sökmotorer är vanliga för informationshämtning i mobilappar, på webben och i företags datalager. Azure Search ger dig verktyg för att skapa en sökfunktion som liknar funktionerna på stora kommersiella webbplatser.

I den här 9-minutersvideon från programansvarige Liam Cavanagh får du reda på hur integrering av en sökmotor kan vara till nytta för din app. Korta demonstrationer tar upp viktiga funktioner i Azure Search och hur ett vanligt arbetsflöde ser ut. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuter omfattar viktiga funktioner och användningsfall.
+ 3-4 minuter omfattar tjänstetablering. 
+ 4-6 minuter omfattar guiden Importera data som används för att skapa ett index med hjälp av den inbyggda datamängden för fastigheter.
+ 6-9 minuter omfattar Sökutforskaren och olika frågor.
