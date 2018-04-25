---
title: Vad är Azure Search | Microsoft Docs
description: Azure Search är en fullständigt hanterad värdbaserad molnsöktjänst. Läs mer i den här funktionsöversikten.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: ad5c60c246c2946e4dd3a5bb6b4d6e8d21d2b03d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-search"></a>Vad är Azure Search?
Azure Search är en sökning-som-tjänst-molnlösning som ger utvecklare API:er och verktyg för att lägga till omfattande sökfunktioner för innehåll i webb-, mobil- och företagsprogram.

Funktionerna exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET-SDK](search-howto-dotnet-sdk.md) som maskerar den inbyggda komplexiteten i informationshämtning. Förutom API:er tillhandahåller Azure Portal stöd för administration och innehållshantering, med verktyg för indexprototyper och -frågor. Eftersom tjänsten körs i molnet hanteras infrastruktur och tillgänglighet av Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Funktionssammanfattning

| Kategori | Funktioner |
|----------|----------|
|Fulltextsökning och textanalys | [Fulltextsökning](search-lucene-query-architecture.md) är ett primärt användningsfall för de flesta sökbaserade appar. Frågor kan formuleras med en syntax som stöds. <br/><br/>[**Enkel frågesyntax**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) innehåller logiska operatorer, frassökoperatorer, suffixoperatorer och prioritetsoperatorer.<br/><br/>[**Lucene-frågesyntax**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) innehåller alla åtgärder i enkel syntax, med tillägg för fuzzy-sökning, närhetssökning, termförstärkning och reguljära uttryck.| 
| Dataintegrering | Azure Search-index accepterar data från alla källor, förutsatt att de skickas som en JSON-datastruktur. <br/><br/> För datakällor som stöds i Azure kan du om du vill använda [**indexerare**](search-indexer-overview.md) för att automatiskt crawla [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) eller [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och synkronisera sökindexets innehåll med det primära datalagret. Azure Blob-indexerare kan utföra *dokumentknäckning* för [indexering av större filformat](search-howto-indexing-azure-blob-storage.md), bland annat Microsoft Office-, PDF- och HTML-dokument. |
| Språklig analys | Analysverktyg är komponenter som används för textbearbetning under indexerings- och sökåtgärder. Det finns två typer. <br/><br/>[**Anpassad lexikalisk analys**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) används för komplexa sökfrågor med fonetisk matchning och reguljära uttryck. <br/><br/>[**Språkanalys**](https://docs.microsoft.com/rest/api/searchservice/language-support) från Lucene eller Microsoft används för intelligent hantering av språkspecifik lingvistik, bland annat verbtempus, genus, substantiv med oregelbunden plural (till exempel 'mus' kontra 'möss'), uppdelning av sammansatta ord, ordseparation (för språk utan blanksteg) och mycket mer. |
| Geo-sökning | Azure Search bearbetar, filtrerar och visar geografiska platser. Det gör det möjligt för användare att utforska data baserat på ett sökresultats närhet till en fysisk plats. [Titta på den här videon](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) eller [gå igenom det här exemplet](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) om du vill veta mer. |
| Funktioner för användarupplevelse | [**Sökförslag**](https://docs.microsoft.com/rest/api/searchservice/suggesters) kan aktiveras för ifyllningsfrågor i ett sökfält. Faktiska dokument i indexet föreslås när användare anger partiell sökinmatning. <br/><br/>[**Aspektbaserad navigering**](https://docs.microsoft.com/azure/search/search-faceted-navigation) aktiveras genom en enda frågeparameter. Azure Search returnerar en aspektbaserad navigeringsstruktur som du kan använda som koden bakom en lista med kategorier, för automatiskt dirigerad filtrering (till exempel för filtrering av katalogobjekt efter prisintervall eller varumärke). <br/><br/> [**Filter**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) kan användas för att införliva aspektbaserad navigering i programmets användargränssnitt, förbättra frågeformulering och filtrera baserat på användar- eller utvecklarangivna villkor. Skapa filter med OData-syntax.<br/><br/> [**Träffmarkering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) tillämpar textformatering på ett matchande nyckelord i sökresultat. Du kan välja vilka fält som ska returnera markerade fragment.<br/><br/>[**Sortering**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) erbjuds för flera fält via indexschemat och växlas sedan vid frågetid med en enda sökparameter.<br/><br/> [**Växling**](search-pagination-page-layout.md) och begränsning av sökresultat är enkelt med den finjusterade kontroll över sökresultaten som Azure Search erbjuder.  
| Relevans | [**Enkel bedömning**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) är en viktig fördel med Azure Search. Bedömningsprofiler används för att modellera relevans som en funktion av värden i själva dokumenten. Du kanske till exempel vill att nyare produkter eller rabatterade produkter ska visas högre upp i sökresultaten. Du kan också skapa bedömningsprofiler med hjälp av taggar för anpassad bedömning baserat på kunders sökinställningar som du har spårat och lagrat separat. |
| Övervakning och rapportering | [**Söktrafikanalys**](search-traffic-analytics.md) samlas in och analyseras för att få nya insikter utifrån vad användare skriver i sökrutan. <br/><br/>Mått för frågor per sekund, svarstid och begränsning fångas in och rapporteras på portalsidor utan att ytterligare konfiguration krävs. Du kan också enkelt övervaka index- och dokumentantal så att du kan justera kapacitet vid behov. Mer information finns i [Tjänstadministration](search-manage.md) |
| Verktyg för prototyper och inspektion | I portalen kan du använda [**guiden Importera data**](search-import-data-portal.md) för att konfigurera indexerare, indexdesignern för att sätta upp ett index och [**Sökutforskaren**](search-explorer.md) för att testa frågor och förfina bedömningsprofiler. Du kan också öppna ett index om du vill visa dess schema. |
| Infrastruktur | **Plattformen med hög tillgänglighet** ger en mycket tillförlitlig söktjänst. När det skalas korrekt [erbjuder Azure Search ett serviceavtal på 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Azure Search är **fullständigt hanterat och skalbart** som en komplett lösning, och kräver ingen som helst infrastrukturhantering. Tjänsten kan skräddarsys efter dina behov genom att skala i två dimensioner för att hantera mer dokumentlagring, högre frågebelastningar eller båda.

## <a name="how-to-use-azure-search"></a>Använda Azure Search
### <a name="step-1-provision-service"></a>Steg 1: Etablera tjänsten
Du kan få igång en Azure Search-tjänst i [Azure Portal](https://portal.azure.com/) eller via [Azure Resource Management-API:t](/rest/api/searchmanagement/). Du kan välja den kostnadsfria tjänsten som delas med andra prenumeranter, eller en [betald nivå](https://azure.microsoft.com/pricing/details/search/) som dedikerar resurser som bara används av din tjänst. För betalda nivåer kan du skala en tjänst i två dimensioner: 

- Lägg till repliker och öka kapaciteten för hantering av tunga frågebelastningar.   
- Lägg till partitioner och utöka lagringen för fler dokument. 

Genom att hantera dokumentlagring och frågeflöde separat kan du kalibrera resurser baserat på produktionskrav.

### <a name="step-2-create-index"></a>Steg 2: Skapa index
Innan du kan ladda upp sökbart innehåll måste du först definiera ett Azure Search-index. Ett index är som en databastabell som innehåller dina data och kan acceptera sökfrågor. Du definierar indexschemat som ska mappas för att återspegla strukturen för de dokument du vill söka i, ungefär som fält i en databas.

Ett schema kan skapas i Azure Portal eller programmässigt med [.NET-SDK](search-howto-dotnet-sdk.md) eller [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Steg 3: Indexera data
När du har definierat ett index kan du ladda upp innehåll. Du kan använda en push- eller pull-modell.

Med pull-modellen hämtas data från externa datakällor. Den stöds med hjälp av *indexerare* som effektiviserar och automatiserar aspekter av datainmatning, till exempel anslutning till, läsning och serialisering av data. [Indexerare](/rest/api/searchservice/Indexer-operations) finns tillgängliga för Azure Cosmos DB, Azure SQL Database, Azure Blob Storage och SQL Server på en virtuell Azure-dator. Du kan konfigurera en indexerare för datauppdatering på begäran eller enligt schemaläggning.

Push-modellen tillhandahålls via SDK eller REST API:er, som används för att skicka uppdaterade dokument till ett index. Du kan skicka data från i princip valfri datamängd med JSON-format. Mer information om hur du läser in data finns i [Lägga till, uppdatera och ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents) och [Använda .NET-SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Steg 4: Söka
När du har fyllt i ett index kan du [utfärda sökfrågor](/rest/api/searchservice/Search-Documents) till tjänstens slutpunkt med hjälp av enkla HTTP-begäranden med REST API eller .NET-SDK.

## <a name="how-azure-search-compares"></a>Azure Search i jämförelse

Kunder frågar ofta om Azure Search i jämförelse med andra sökrelaterade lösningar. I följande tabell sammanfattas viktiga skillnader.

| Jämfört med | Viktiga skillnader |
|--|--|
|Bing | [API för webbsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) söker i indexen på Bing.com efter matchande termer som du skickar. Index skapas utifrån HTML-, XML- och annat webbinnehåll på offentliga webbplatser. [Anpassad sökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) erbjuder samma crawlerteknik för webbinnehållstyper, begränsat till enskilda webbplatser.<br/><br/>Azure Search söker i ett index du definierar, som fyllts med data och dokument du äger, ofta från olika källor. Azure Search har crawlerfunktioner för vissa datakällor med hjälp av [indexerare](search-indexer-overview.md), men du kan skicka valfritt JSON-dokument som överensstämmer med indexschemat till en enda konsoliderad sökbar resurs. |
|Databassökning | [SQL Server-fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) är för innehåll som är internt för DBMS, i SQL-tabeller. <br/><br/>Azure Search lagrar innehåll från heterogena källor och har särskilda funktioner för textbearbetning, till exempel språklig och anpassad analys. [Motorn för fulltextsökning](search-lucene-query-architecture.md) i Azure Search bygger på Apache Lucene, en branschstandard inom informationshämtning. <br/><br/>Resursutnyttjande är en annan brytpunkt. Sökning med naturligt språk är ofta beräkningsintensiv. Om sökningen avlastas till en dedikerad lösning bevaras resurser för transaktionsbearbetning. Genom att göra sökningen extern kan du enkelt justera skalan utifrån frågevolymen.|
|Dedikerad söklösning | Lokala lösningar eller molntjänstlösningar är dedikerade söklösningar med kompletta funktioner. Söktekniker erbjuder vanligtvis kontroll över indexerings- och frågepipelines, tillgång till mer avancerad fråge- och filtreringssyntax, kontroll över rangordning och relevans, samt funktioner för automatiskt dirigerad och intelligent sökning. <br/><br/>Du kan hitta dedikerade söklösningar som erbjuds som en molntjänst eller som en fristående server som finns lokalt eller på en virtuell dator. En molntjänst är det rätta valet om du vill ha en [nyckelfärdig lösning med minimalt merarbete och underhåll, och justerbar skala](#cloud-service-advantage). <br/><br/>Inom molnparadigmet erbjuder flera leverantörer jämförbara baslinjefunktioner, med fulltextsökning, geo-sökning och möjlighet att hantera en viss nivå av tvetydighet i sökinmatningar. Det är vanligtvis en [specialiserad funktion](#feature-drilldown) eller den övergripande enkelheten i API:er, verktyg och hantering som avgör det bästa valet. |

Bland molnproviders är Azure Search starkast för arbetsbelastningar för fulltextsökning i innehållslager och -databaser på Azure, för appar som främst förlitar sig på sökning för både informationshämtning och innehållsnavigering. Viktiga fördelar är:

+ Azure-dataintegrering (crawler) på indexeringslagret
+ Azure Portal för central hantering
+ Azure-skala, tillförlitlighet och tillgänglighet i världsklass
+ Språklig och anpassad analys, med analysverktyg för fulltextsökning på 56 språk
+ [Centrala funktioner som är gemensamma för sökcentriska appar](#feature-drilldown): bedömning, aspektbasering, förslag, synonymer, geo-sökning och mycket mer.

> [!Note]
> Icke-Azure-datakällor stöds fullt ut, men förlitar sig på en mer kodintensiv push-metod i stället för indexerare. Med hjälp av API:er kan du skicka valfri JSON-dokumentsamling till ett Azure Search-index.

Bland våra kunder är de som kan dra nytta av det bredaste utbudet av funktioner i Azure Search bland annat onlinekataloger, affärsprogram och program för dokumentidentifiering.

## <a name="rest-api--net-sdk"></a>REST API | .NET-SDK

Även om många uppgifter kan utföras i portalen är Azure Search avsett för utvecklare som vill integrera sökfunktioner i befintliga program. Följande programmeringsgränssnitt är tillgängliga.

|Plattform |Beskrivning |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-kommandon som stöds av alla programmeringsplattformar och -språk, bland annat Xamarin, Java och JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-omslutning för REST API erbjuder effektiv kodning i C# och andra språk med förvaltad kod med .NET Framework som mål |

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Azure-prenumeranter kan [etablera en tjänst på den kostnadsfria nivån](search-create-service-portal.md).

Om du inte är prenumerant kan du [öppna ett Azure-konto utan kostnad](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Du får krediter för att prova Azure-betaltjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster. 

## <a name="how-to-get-started"></a>Så här kommer du igång

1. Skapa en tjänst på den [kostnadsfria nivån](search-create-service-portal.md).

2. Gå igenom en eller flera av följande självstudier. 

  + I [Använda .NET-SDK](search-howto-dotnet-sdk.md) demonstreras huvudstegen inom förvaltad kod.  
  + I [Komma igång med REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) visas samma steg med REST API.  
  + [Skapa ditt första index i portalen](search-get-started-portal.md) med inbyggda funktioner för indexering och prototyper.   

Sökmotorer är vanliga för informationshämtning i mobilappar, på webben och i företags datalager. Azure Search ger dig verktyg för att skapa en sökfunktion som liknar funktionerna på stora kommersiella webbplatser.

I den här 9-minutersvideon från programansvarige Liam Cavanagh får du reda på hur integrering av en sökmotor kan vara till nytta för din app. Korta demonstrationer tar upp viktiga funktioner i Azure Search och hur ett vanligt arbetsflöde ser ut. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuter omfattar viktiga funktioner och användningsfall.
+ 3-4 minuter omfattar tjänstetablering. 
+ 4-6 minuter omfattar guiden Importera data som används för att skapa ett index med hjälp av den inbyggda datamängden för fastigheter.
+ 6-9 minuter omfattar Sökutforskaren och olika frågor.


