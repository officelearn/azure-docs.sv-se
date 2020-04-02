---
title: Servicegränser för nivåer och skus
titleSuffix: Azure Cognitive Search
description: Tjänstgränser som används för kapacitetsplanering och maximala gränser för begäranden och svar för Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549043"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Tjänstbegränsningar i Azure Cognitive Search

Maximala gränser för lagring, arbetsbelastningar och kvantiteter av index och andra objekt beror på om du [etablerar Azure Cognitive Search](search-create-service-portal.md) på **free,** **basic,** **standard**eller **lagringsoptimerade** prisnivåer.

+ **Gratis** är en delad tjänst med flera innehavare som medföljer din Azure-prenumeration. Indexerings- och frågebegäranden körs på repliker och partitioner som används av andra klienter.

+ **Basic** tillhandahåller dedikerade datorresurser för produktionsarbetsbelastningar i mindre skala, men delar vissa nätverksinfrastruktur med andra klienter.

+ **Standard** körs på dedikerade maskiner med mer lagrings- och bearbetningskapacitet på alla nivåer. Standard finns i fyra nivåer: S1, S2, S3 och S3 HD.

+ **Lagringsoptimerade** körs på dedikerade datorer med mer total lagring, lagringsbandbredd och minne än **Standard**. Lagringsoptimerad finns i två nivåer: L1 och L2

> [!NOTE]
> Från och med den 1 juli är alla nivåer allmänt tillgängliga, inklusive lagringsoptimerade nivån. Alla priser finns på sidan [Prisinformation.](https://azure.microsoft.com/pricing/details/search/)

  S3 High Density (S3 HD) är konstruerad för specifika arbetsbelastningar: [flerargsinnehavare](search-modeling-multitenant-saas-applications.md) och stora mängder små index (tre tusen index per tjänst). Den här nivån tillhandahåller inte [indexeringsfunktionen](search-indexer-overview.md). På S3 HD måste datainmatning utnyttja push-metoden med hjälp av API-anrop för att skicka data från källa till index. 

> [!NOTE]
> En tjänst etableras på en viss nivå. Hoppande nivåer för att få kapacitet innebär att etablera en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [Välj en SKU eller nivå](search-sku-tier.md). Mer information om hur du justerar kapaciteten inom en tjänst som du redan har etablerat finns i [Skala resursnivåer för fråge- och indexeringsarbetsbelastningar](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Prenumerationsgränser
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Begränsningar för lagring
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Indexgränser

| Resurs | Kostnadsfri | Grundläggande&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 (på andra) | L2 (på andra) |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal index |3 |5 eller 15 |50 |200 |200 |1 000 per partition eller 3 000 per tjänst |10 |10 |
| Maximalt antal enkla fält per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximala komplexa samlingsfält per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximala element i alla&nbsp;komplexa samlingar per dokument<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximalt djup för komplexa fält |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximala [förslagshållare](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximala [poängsättningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximala funktioner per profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Grundläggande tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) på index. Grundläggande nivå är den enda SKU med en nedre gräns på 100 fält per index.

<sup>2</sup> Att ha ett mycket stort antal element i komplexa samlingar per dokument orsakar för närvarande hög lagringsanvändning. Detta är ett känt problem. Under tiden är en gräns på 3000 en säker övre gräns för alla tjänstnivåer. Den här gränsen tillämpas endast för indexeringsåtgärder som använder den tidigaste allmänt`2019-05-06`tillgängliga (GA)API-versionen som stöder komplexa typfält ( ) och framåt. Om du inte vill bryta klienter som använder tidigare API-versioner för förhandsversionen (som stöder komplexa typfält) kommer vi inte att tillämpa den här gränsen för indexeringsåtgärder som använder dessa api-versioner för förhandsversionen. Observera att api-versioner för förhandsversioner inte är avsedda att användas för produktionsscenarier och vi rekommenderar starkt att kunderna flyttar till den senaste GA API-versionen.

> [!NOTE]
> Även om den maximala kapaciteten för ett enskilt index vanligtvis begränsas av tillgänglig lagring, finns det maximala övre gränser för det totala antalet dokument som kan lagras i ett enda index. Denna gräns är cirka 24 miljarder dokument per index för söktjänsterna Basic, S1, S2 och S3 och 2 miljarder dokument per index för S3HD-söktjänster. Varje element i en komplex samling räknas som separata dokument för dessa gränser.

<a name="document-limits"></a>

## <a name="document-limits"></a>Begränsningar för dokument 

Från och med oktober 2018 finns det inte längre några gränser för antal dokument för någon ny tjänst som skapats på någon fakturerbar nivå (Basic, S1, S2, S3, S3 HD) i någon region. Äldre tjänster som skapats före oktober 2018 kan fortfarande omfattas av dokumenträkningsgränser.

Om du vill ta reda på om tjänsten har dokumentgränser använder du [REST-API:et FÖR GET Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Dokumentgränser återspeglas i svaret, med `null` inga gränser.

### <a name="document-size-limits-per-api-call"></a>Begränsningar av dokumentstorlek per API-anrop

Den maximala dokumentstorleken när du anropar ett index-API är cirka 16 megabyte.

Dokumentstorleken är faktiskt en gräns för storleken på gruppen index-API-begäranden. Eftersom du kan skicka en bunt med flera dokument till index-API:et samtidigt, beror storleksgränsen realistiskt på hur många dokument som finns i batchen. För en batch med ett enda dokument är den maximala dokumentstorleken 16 MB JSON.

Kom ihåg att utesluta data som inte kan ifrågasättas från begäran om du vill behålla dokumentstorleken. Bilder och andra binära data är inte direkt frågebara och bör inte lagras i indexet. Om du vill integrera data som inte kan ifrågasättas i sökresultaten definierar du ett fält som inte är sökbart och som lagrar en URL-referens till resursen.

## <a name="indexer-limits"></a>Indexeringsgränser

Det finns maximala körtider för att ge balans och stabilitet till tjänsten som helhet, men större datauppsättningar kan behöva mer indexeringstid än vad det högsta tillåtna antalet tillåter. Om ett indexeringsjobb inte kan slutföras inom den maximala tillåtna tiden kan du prova att köra det enligt ett schema. Schemaläggaren håller reda på indexeringsstatus. Om ett schemalagt indexeringsjobb avbryts av någon anledning kan indexeraren fortsätta där det senast slutade vid nästa schemalagda körning.


| Resurs | Gratis&nbsp;<sup>1</sup> | Grundläggande&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;&nbsp;HD<sup>3</sup>|L1 (på andra) |L2 (på andra) |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal indexerare |3 |5 eller 15|50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximalt antal datakällor |3 |5 eller 15 |50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximala skillsets <sup>4</sup> |3 |5 eller 15 |50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximal indexeringsbelastning per anrop |10 000 dokument |Begränsas endast av maximalt antal dokument |Begränsas endast av maximalt antal dokument |Begränsas endast av maximalt antal dokument |Begränsas endast av maximalt antal dokument |Ej tillämpligt |Obegränsad |Obegränsad |
| Minsta schema | 5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter | 5 minuter |
| Maximal körtid <sup>5</sup> | 1-3 minuter |24 timmar |24 timmar |24 timmar |24 timmar |Ej tillämpligt  |24 timmar |24 timmar |
| Maximal körtid för kognitiva sökkunskaper eller blobindexering med bildanalys <sup>5</sup> | 3-10 minuter |2 timmar |2 timmar |2 timmar |2 timmar |Ej tillämpligt  |2 timmar |2 timmar |
| Blob indexerare: maximal blob storlek, MB |16 |16 |128 |256 |256 |Ej tillämpligt  |256 |256 |
| Blob indexerare: maximalt tecken av innehåll som extraherats från en blob |32,000 |64,000 |4&nbsp;miljoner |8&nbsp;miljoner |16&nbsp;miljoner |Ej tillämpligt |4&nbsp;miljoner |4&nbsp;miljoner |

<sup>1</sup> Kostnadsfria tjänster har indexeraren maximal körningstid på 3 minuter för blob-källor och 1 minut för alla andra datakällor. För AI-indexering som anropar Cognitive Services är kostnadsfria tjänster begränsade till 20 kostnadsfria transaktioner per dag, där en transaktion definieras som ett dokument som framgångsrikt passerar genom anrikningspipelinen.

<sup>2</sup> Grundläggande tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) för indexerare, datakällor och kompetenser.

<sup>3</sup> S3 HD-tjänster inkluderar inte indexerarstöd.

<sup>4</sup> Högst 30 färdigheter per kompetens.

<sup>5</sup> Kognitiva sökarbetsbelastningar och bildanalys i Azure blob indexering har kortare körtider än vanlig textindexering. Bildanalys och bearbetning av naturligt språk är beräkningsmässigt intensiva och förbrukar oproportionerliga mängder tillgänglig processorkraft. Speltiden reducerades för att ge andra jobb i kön en möjlighet att köra.  

> [!NOTE]
> Som anges i [indexgränserna](#index-limits)kommer indexerare också att framtvinga den övre gränsen på 3000 element`2019-05-06`i alla komplexa samlingar per dokument som börjar med den senaste GA API-versionen som stöder komplexa typer ( ) och framåt. Det innebär att om du har skapat indexeraren med en tidigare API-version kommer du inte att omfattas av den här gränsen. För att bevara maximal kompatibilitet **utesluts** en indexerare som har `2019-05-06` skapats med en tidigare API-version och sedan uppdaterats med en API-version eller senare fortfarande från gränserna. Kunderna bör vara medvetna om de negativa effekterna av att ha mycket stora komplexa samlingar (som tidigare nämnts) och vi rekommenderar starkt att du skapar nya indexerare med den senaste GA API-versionen.

## <a name="synonym-limits"></a>Synonymgränser

Det maximala antalet synonymkartor som tillåts varierar beroende på prisnivå. Varje regel kan ha upp till 20 expansioner, där en expansion är en motsvarande term. Till exempel, med tanke på "katt", förening med "kitty", "katt", och "felis" (släktet för katter) skulle räknas som 3 expansioner.

| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3-HD |L1 (på andra) | L2 (på andra) |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximala synonymkartor |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximalt antal regler per karta |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Frågor per sekund (QPS)

QPS uppskattningar måste utvecklas självständigt av varje kund. Indexstorlek och komplexitet, frågestorlek och komplexitet samt mängden trafik är primära bestämningsfaktorer för QPS. Det finns inget sätt att erbjuda meningsfulla uppskattningar när sådana faktorer är okända.

Uppskattningar är mer förutsägbara när de beräknas på tjänster som körs på dedikerade resurser (grundläggande nivåer och standardnivåer). Du kan uppskatta QPS närmare eftersom du har kontroll över fler av parametrarna. Mer information om hur du närmar dig uppskattning finns i [Azure Cognitive Search performance and optimization](search-performance-optimization.md).

För lagringsoptimerade nivåer bör du förvänta dig ett lägre frågedataflöde och högre svarstid än standardnivåerna.  Metoden för att uppskatta frågeprestanda som du får uppleva är samma som standardnivåerna.

## <a name="data-limits-ai-enrichment"></a>Datagränser (AI-anrikning)

En [AI-anrikningspipeline](cognitive-search-concept-intro.md) som anropar en Text Analytics-resurs för [entitetsigenkänning,](cognitive-search-skill-entity-recognition.md) [extrahering av nyckelfraser,](cognitive-search-skill-keyphrases.md) [sentimentanalys,](cognitive-search-skill-sentiment.md) [språkidentifiering](cognitive-search-skill-language-detection.md)och [PII-identifiering](cognitive-search-skill-pii-detection.md) är föremål för datagränser. Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till sentimentanalysatorn använder du [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Begränsningsgränser

Sökfråga och indexeringsbegäranden begränsas när systemet närmar sig maximal kapacitet. Begränsning fungerar annorlunda för olika API:er. Fråge-API:er (Sök/föreslå/komplettera automatiskt) och indexering av API:er begränsar dynamiskt baserat på belastningen på tjänsten. Index-API:er har statiska begränsningar för begäranden. 

Statiska hastighetsbegärangränser för åtgärder som är relaterade till ett index:

+ Lista index (GET /index): 5 per sekund per sökenhet
+ Hämta index (GET /index/myindex): 10 per sekund per sökenhet
+ Skapa index (POST /index): 12 per minut per sökenhet
+ Skapa eller uppdatera index (PUT /index/myindex): 6 per sekund per sökenhet
+ Ta bort index (DELETE /index/myindex): 12 per minut per sökenhet 

## <a name="api-request-limits"></a>Begränsningar för API-begäran
* Maximalt 16 MB per begäran <sup>1</sup>
* Maximal url-längd på 8 kB
* Maximalt 1 000 dokument per batch med indexuppladdningar, sammanslagningar eller borttagningar
* Maximalt 32 fält i $orderby-satsen
* Maximal söktermstorlek är 32 766 byte (32 KB minus 2 byte) AV UTF-8-kodad text

<sup>1</sup> I Azure Cognitive Search omfattas brödtexten för en begäran av en övre gräns på 16 MB, vilket innebär en praktisk gräns för innehållet i enskilda fält eller samlingar som annars inte begränsas av teoretiska gränser (se [Datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för mer information om fältsammansättning och begränsningar).

## <a name="api-response-limits"></a>API-svarsgränser
* Maximalt 1 000 dokument som returneras per sida med sökresultat
* Maximalt 100 förslag som returneras per föreslå API-begäran

## <a name="api-key-limits"></a>Api-nyckelgränser
API-nycklar används för tjänstautentisering. Det finns två typer. Administratörsnycklar anges i begäranden och ger tjänsten fullständig läs-skriv-åtkomst. Frågenycklar är skrivskyddade, anges på URL:en och distribueras vanligtvis till klientprogram.

* Högst 2 administratörsnycklar per tjänst
* Maximalt 50 frågenycklar per tjänst