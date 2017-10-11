---
title: "Vad är Azure Search | Microsoft Docs"
description: "Azure Search är en fullständigt hanterad värdbaserade moln söktjänst. Läs mer i översikt över den här funktionen."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: ashmaka
ms.openlocfilehash: baf73639eb6506b14d0d3a4de1bf55b66e973b95
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="what-is-azure-search"></a>Vad är Azure Search?
Azure Search är en molnlösning för sökning som en tjänst som ger utvecklare API: er och verktyg för att lägga till en omfattande sökinställningar över dina data i webb-, mobil- och enterprise-program.

Funktioner som exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET SDK](search-howto-dotnet-sdk.md) som döljer inbyggd komplexiteten i sökteknik. Förutom API: er tillhandahåller Azure-portalen administrations- och prototyper. Tillgänglighet för infrastruktur och hanteras av Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Funktioner – sammanfattning

| Kategori | Funktioner |
|----------|----------|
|Fulltextsökning och analys av text | [**Fulltextsökning** ](search-lucene-query-architecture.md) är en primär användningsfall för de flesta Sökbaserade appar. Frågor kan utformas med en stöds syntax: <br/><br/>[**Enkel frågesyntaxen**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), som innehåller logiska operatorer, frasen sökoperatorer, suffix operatorer, prioritet operatörer.<br/><br/>[**Lucene frågesyntaxen** ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) erbjuder alla enkla frågeterm för support plus fuzzy sökning, närhet sökning, den och reguljära uttryck.| 
| Dataintegrering | Azure Search index ta emot data från alla datakällor under förutsättning att den skickas som en JSON-datastruktur. <br/><br/> För datakällor som stöds i Azure, du kan använda [ **indexerare** ](search-indexer-overview.md) att samla [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-documentdb.md), eller [Azure Blob storage](search-howto-indexing-azure-blob-storage.md) att synkronisera dina sökindex vars innehåll med din primära dataarkivet. Azure Blob-indexerare kan utföra *dokumentet knäcka* för [indexering större filformat](search-howto-indexing-azure-blob-storage.md), inklusive Microsoft Office, PDF- och HTML-dokument. |
| Sök analytics | [**Anpassade lexikala analyzers** ](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) är tillgängliga för avancerade sökningar med fonetisk matchande och vanliga uttryck. |
| Språkstöd | [**Språkanalys** ](https://docs.microsoft.com/rest/api/searchservice/language-support) från Lucene, liksom Microsofts naturligt språk processorer som är tillgängliga på 56 olika språk till ett intelligent sätt hantera språkspecifika linguistics inklusive verbtempus, kön, oregelbundna pluralis substantiv (till exempel ' mus' eller 'möss'), word Frigör sammanslagning, radbrytning (för språk utan blanksteg) och mycket mer. |
| GEO-sökning | Azure Search Intelligent bearbetar, filter, och visar geografiska platser. Det gör det möjligt för användare att utforska data baserat på närhet i ett sökresultatet till en fysisk plats. [Det här videoklippet](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) eller [granska det här exemplet](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) vill veta mer. |
| Användarens upplevelse funktioner | [**Sökförslag** ](https://docs.microsoft.com/rest/api/searchservice/suggesters) kan aktiveras för typ-ahead frågor i ett sökfält. Faktiska dokument i ditt index föreslås som användare ange partiell sökning indata. <br/><br/>[**Fasetterad navigering** ](https://docs.microsoft.com/azure/search/search-faceted-navigation) aktiveras genom en enda frågeparameter. Azure Search returnerar en fasetterad navigeringsstruktur som du kan använda som koden bakom en lista med kategorier, för automatisk dirigerad filtrering (till exempel filter katalogobjekt av pris-intervallet eller varumärken). <br/><br/> [**Filter** ](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) kan användas för att införliva fasetterad navigering i programmets användargränssnitt, förbättra frågan formulering och filtrera baserat på användaren eller utvecklare angivna villkor. Skapa filter med OData-syntax.<br/><br/> [**Träffar syntaxmarkering** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) gäller visual formuläret atting ett matchande nyckelord i sökresultaten. Du kan välja vilka fält returnera markerade kodavsnitt.<br/><br/>[**Sortering** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) är erbjuds för flera fält via indexeringsschema och sedan växlas när databasfrågan med en enda sökparameter.<br/><br/> [**Växling** ](search-pagination-page-layout.md) och begränsa sökresultaten är enkelt med buffertstorleken ögonen öppna kontrollen som Azure Search erbjuder över sökresultat.  
| Relevans | [**Enkel resultatfunktioner** ](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) är en fördel med Azure Search. Bedömningsprofil profiler används för att modellera relevans som en funktion av värdena i själva dokumenten. Till exempel du kanske vill nyare produkter eller rabatterad produkter visas högre upp i sökresultatet. Du kan också skapa bedömningsprofil profiler med hjälp av taggar för anpassade poäng utifrån kundens sökinställningar du spåras och lagrats separat. |
| Övervakning och rapportering | [**Sök trafik analytics** ](search-traffic-analytics.md) samlas in och analyseras för att låsa upp insikter från vad användare skriver i sökrutan. <br/><br/>Mått på frågor per sekund, svarstid och begränsning fångas in och rapporteras i portalens sidor utan ytterligare konfiguration krävs. Du kan också enkelt övervakaren index och dokumentet räknar så att du kan justera kapacitet vid behov. Mer information finns i [tjänsten administration](search-manage.md) |
| Verktyg för prototyper och kontroll | I portalen kan du använda den [ **guiden Importera data** ](search-import-data-portal.md) konfigurera indexerare index designer för att klara av ett index och [ **Sök explorer** ](search-explorer.md) att testa frågor och förfina bedömningsprofil profiler. Du kan också öppna ett index om du vill visa dess schema. |
| Infrastruktur | Den **högtillgänglig plattform** garanterar en mycket tillförlitlig sökinställningar för tjänsten. När skalas korrekt [Azure Search erbjuder ett SLA för 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Fullständigt hanterad och skalbar** som en lösning för slutpunkt till slutpunkt, kräver Azure Search absolut inga infrastrukturhantering. Tjänsten kan skräddarsys efter dina behov genom att skala i två dimensioner kan hantera mer dokumentlagring, högre belastning för frågan eller båda.

## <a name="how-it-works"></a>Hur det fungerar
### <a name="step-1-provision-service"></a>Steg 1: Etablera tjänsten
Du kan få igång en Azure Search-tjänst i den [Azure-portalen](https://portal.azure.com/) eller via den [Azure Resource Management API](/rest/api/searchmanagement/). Du kan välja antingen ledigt service delas med andra prenumeranter, eller en [betald nivå](https://azure.microsoft.com/pricing/details/search/) som dedikerar resurser som bara används av tjänsten. Du kan skala en tjänst i två dimensioner för betald nivåer: 

- Lägg till repliker för att öka kapaciteten att hantera tunga frågan belastning.   
- Lägg till partitioner om du vill utöka lagring för flera dokument. 

Genom att hantera dokument lagrings- och genomströmning separat kalibrera du resourcing baserat på kraven för produktion.

### <a name="step-2-create-index"></a>Steg 2: Skapa index
Innan du kan överföra sökbara innehåll, måste du först definiera ett Azure Search-index. Ett index är som en databastabell som innehåller dina data och kan acceptera sökfrågor. Du kan definiera indexeringsschema att mappa för att återspegla strukturen för de dokument som du vill söka, liknande till fält i en databas.

Ett schema kan vara skapad i Azure-portalen eller programmässigt med hjälp av den [.NET SDK](search-howto-dotnet-sdk.md) eller [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Steg 3: Indexinformationen
När du har definierat ett index är du redo att överföra innehåll. Du kan använda en sändning eller hämtning modell.

Pull-modellen hämtar data från externa datakällor. Den stöds via *indexerare* att förenkla och automatisera aspekter av datapåfyllning som ansluter till, läser och serialisering. [Indexerare](/rest/api/searchservice/Indexer-operations) är tillgängliga för Azure Cosmos DB, Azure SQL Database, Azure Blob Storage och SQL Server finns i en Azure VM. Du kan konfigurera en indexerare för på begäran eller enligt schemalagda datauppdatering.

Push-modell tillhandahålls via SDK eller REST API: er, används för att skicka uppdaterade dokument till ett index. Du kan skicka data från valfri dataset i JSON-format. Se [Lägg till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents) eller [hur du använder .NET SDK)](search-howto-dotnet-sdk.md) anvisningar om att läsa in data.

### <a name="step-4-search"></a>Steg 4: Sök
Efter att fylla i ett index, kan du [utfärda sökfrågor](/rest/api/searchservice/Search-Documents) till din använder enkel HTTP-begäranden med REST API eller .NET SDK-tjänstens slutpunkt.

## <a name="how-it-compares"></a>Jämförelse

Kunder ofta fråga hur [fulltextsökning i Azure Search](search-lucene-query-architecture.md) Jämför med [fulltextsökning](https://en.wikipedia.org/wiki/Full_text_search) i sin Databasprodukt. Vår svaret är att Azure Search språk funktioner är bättre och mer flexibelt med stöd för Lucene frågor, språkanalys från Lucene och Microsoft, anpassade analyzers för fonetisk eller andra särskilda indata och möjligheten att slå samman data från flera källor i sökindexet. 

En annan är angripits att hela sökinställningar adresser för en lösning för sökning. Du kan exempelvis anpassade poängsättning av resultat, fasetterad navigering för automatisk dirigerad filtrering, träffar syntaxmarkering och typeahead frågeförslag. 

Verktyg för övervakning och förståelse av frågeaktivitet kan också räkna i Sök lösningen beslut. Azure Search stöder till exempel [söka trafik analytics](search-traffic-analytics.md) mått på klickningar upp söker, söker utan klickningar och så vidare. Du är produkter där sökningen är ett tillägg, troligen inte att hitta dessa funktioner.

Resursanvändningen är ett annat övervägande. Sökning med naturligt språk är ofta beräkningsmässigt intensiv. Några av våra kunder avläst sökningen till Azure Search som ett sätt att bevara datorresurser för transaktionsbearbetning. Du kan enkelt ändra skala för att matcha frågan volym filernas sökningen.

När du har bestämt att gå med dedikerade search är ditt nästa val mellan en molnbaserad tjänst eller en lokal server. En molnbaserad tjänst är det bästa valet om du vill att en [nyckelfärdig lösning med minimal belastning och underhåll och justerbara skala](#cloud-service-advantage).

Inom paradigmet molnet erbjuder flera providers jämförbara grundläggande funktioner, med fulltextsökning, geo-sökning och förmåga att hantera en viss nivå av tvetydighet i Sök-indata. Normalt den har en [specialiserade funktionen](#feature-drilldown), eller enkel och övergripande enkelhet av API: er, verktyg och hantering som avgör den bästa passning.

Molntjänstleverantörer är Azure Search starkaste för fulltext-sökning arbetsbelastningar över innehållslager och databaser på Azure, för appar som förlitar sig främst på söka efter både hämtning av information och innehåll navigering. Viktiga fördelar är:

+ Azure dataintegrering (robotar) på indexerings-nivå
+ Azure portal för central hantering
+ Azure skalbarhet, tillförlitlighet och världsklass tillgänglighet
+ Språkliga och anpassade analys med analyzers för fasta fulltextsökning i 56 språk
+ [Grundläggande funktioner gemensamma för sökningen till Central appar](#feature-drilldown): resultatfunktioner, faceting, förslag, synonymer, geo-sökning och mycket mer.

> [!Note]
> För att ta bort Azure-datakällor stöds fullt ut, men förlita sig på en mer kod-intensiva push-metod i stället för indexerare. Använda våra API: er kan skicka du en JSON-dokumentsamling till ett Azure Search-index.

Bland våra kunder är de utnyttja bredast utbud av funktioner i Azure Search onlinekataloger, line-of-business-program och program för identifiering av dokumentet.

## <a name="rest-api--net-sdk"></a>REST API | .net SDK

När många aktiviteter kan utföras i portalen, är Azure Search avsedd för utvecklare som vill integrera sökfunktionen i befintliga program. Följande programmeringsgränssnitt är tillgängliga.

|Plattform |Beskrivning |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-kommandon som stöds av alla programming plattformar och språk, inklusive Xamarin, Java och JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-Omslutning för REST-API ger effektiv kodning i C# och andra språk för förvaltad kod som målobjekt för .NET Framework |

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Azure-prenumeranter kan [etablera en tjänst i den kostnadsfria nivån](search-create-service-portal.md).

Om du inte är en prenumerant kan du [öppna ett Azure-konto gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Du får kredit för att pröva Azure-betaltjänster. När du använt, kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig såvida du inte uttryckligen ändrar dina inställningar och ber.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster. 

## <a name="how-to-get-started"></a>Så här kommer du igång

1. Skapa en tjänst i den [kostnadsfria nivån](search-create-service-portal.md).

2. Gå igenom en eller flera av följande kurser. 

  + [Hur du använder .NET SDK](search-howto-dotnet-sdk.md) visar Huvudsteg i förvaltad kod.  
  + [Kom igång med REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) visar samma steg med hjälp av REST-API.  
  + [Skapa ditt första index i portalen](search-get-started-portal.md) visar portalens inbyggda indexering och prototyp funktioner.   

Sökmotorer är vanliga drivrutiner för hämtning av information i mobila appar på Internet och i företagets datalager. Azure Search ger dig verktyg för att skapa en sökinställningar liknar dem på stora kommersiella webbplatser.

Lär dig hur integrera en sökmotor kan göra din app i den här 9-minuters videon från Programhanteraren Liam Cavanagh. Kort demonstrationer beskriver viktiga funktioner i Azure Search och hur ett typiskt arbetsflöde ser ut. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuter omfattar viktiga funktioner och användningsfall.
+ 3-4 minuter täcker service etablering. 
+ 4-6 minuter omfattar används för att skapa ett index med hjälp av inbyggda fastigheter datauppsättningen i guiden Importera Data.
+ 6-9 minuter omfattar Sök Explorer och olika frågor.


