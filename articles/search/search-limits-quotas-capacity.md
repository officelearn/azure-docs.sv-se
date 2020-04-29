---
title: 'Tjänst begränsningar för nivåer och SKU: er'
titleSuffix: Azure Cognitive Search
description: Tjänst begränsningar som används för kapacitets planering och högsta gräns för begär Anden och svar för Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549043"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Tjänstbegränsningar i Azure Cognitive Search

De maximala gränserna för lagring, arbets belastningar och kvantiteter av index och andra objekt beror på om du [etablerar Azure kognitiv sökning](search-create-service-portal.md) på kostnads nivåer som är **kostnads fria**, **Basic**, **standard**eller **Storage** .

+ **Kostnads fri** är en delad tjänst för flera innehavare som medföljer din Azure-prenumeration. Indexerings-och fråge förfrågningar körs på repliker och partitioner som används av andra klienter.

+ **Basic** tillhandahåller dedikerade data bearbetnings resurser för produktions arbets belastningar i en mindre skala, men delar en nätverks infrastruktur med andra klienter.

+ **Standard** körs på dedikerade datorer med mer lagrings-och bearbetnings kapacitet på alla nivåer. Standard är fyra nivåer: S1, S2, S3 och S3 HD.

+ **Optimerade lagrings enheter** på dedikerade datorer med mer total lagring, lagrings bandbredd och minne än **standard**. Optimerad lagring levereras på två nivåer: L1 och L2

> [!NOTE]
> Från och med 1 juli är alla nivåer allmänt tillgängliga, inklusive den optimerade lagrings nivån. Du hittar alla priser på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) .

  S3 High densitet (S3 HD) är utformad för specifika arbets belastningar: [flera innehavare](search-modeling-multitenant-saas-applications.md) och stora mängder små index (3000 index per tjänst). Den här nivån tillhandahåller inte [funktionen Indexer](search-indexer-overview.md). På S3 HD måste data inmatningen utnyttja push-metoden, med API-anrop för att skicka data från källan till indexet. 

> [!NOTE]
> En tjänst tillhandahålls på en angiven nivå. Att flytta nivåer för att få kapacitet innebär att tillhandahålla en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [Välj en SKU eller nivå](search-sku-tier.md). Mer information om hur du justerar kapacitet i en tjänst som du redan har etablerad finns i [skala resurs nivåer för frågor och indexering av arbets belastningar](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Prenumerationsgränser
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Lagrings gränser
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index gränser

| Resurs | Kostnadsfri | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal index |3 |5 eller 15 |50 |200 |200 |1 000 per partition eller 3 000 per tjänst |10 |10 |
| Maximalt antal enkla fält per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximalt antal komplexa samlings fält per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximalt antal element i alla komplexa samlingar per&nbsp;dokument<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximalt djup för komplexa fält |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximalt antal [förslag](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximala [bedömnings profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximalt antal funktioner per profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) för index. Basic-nivån är den enda SKU: n med en lägre gräns på 100 fält per index.

<sup>2</sup> det finns ett mycket stort antal element i komplexa samlingar per dokument som för närvarande orsakar hög lagrings användning. Detta är ett känt problem. Under tiden är en gräns på 3000 en säker övre gräns för alla tjänst nivåer. Den här gränsen tillämpas endast för indexerings åtgärder som använder den tidigaste allmänt tillgängliga API-versionen (GA) som stöder komplexa typ fält (`2019-05-06`) och senare. Om du inte vill avbryta klienter som använder tidigare för hands versions-API-versioner (som stöder komplexa typ fält) kommer vi inte att genomdriva den här gränsen för indexerings åtgärder som använder dessa för hands versions API-versioner. Observera att för hands versions-API-versioner inte är avsedda att användas för produktions scenarier och vi rekommenderar att kunderna flyttar till den senaste GA API-versionen.

> [!NOTE]
> Även om den maximala kapaciteten för ett enskilt index vanligt vis begränsas av tillgängligt lagrings utrymme, finns det högst övre gränser för det totala antalet dokument som kan lagras i ett enda index. Den här gränsen är cirka 24 000 000 000 dokument per index för Basic-, S1-, S2-och S3 search-tjänster och 2 000 000 000-dokument per index för S3HD search Services. Varje element i en komplex samling räknas som separata dokument för dessa gränser.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokument gränser 

Från och med oktober 2018 finns det inte längre några begränsningar för antalet dokument för nya tjänster som skapats på någon fakturerbar nivå (Basic, S1, S2, S3, S3 HD) i vilken region som helst. Äldre tjänster som skapats före oktober 2018 kan fortfarande omfattas av begränsningar för antalet dokument.

För att avgöra om din tjänst har dokument gränser använder du [REST API Hämta tjänst statistik](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Dokument gränser avspeglas i svaret, med `null` indikeringar utan gränser.

### <a name="document-size-limits-per-api-call"></a>Dokument storleks gränser per API-anrop

Maximal dokument storlek vid anrop till ett index-API är cirka 16 megabyte.

Dokument storleken är faktiskt en gräns för storleken på innehållet i index-API-begäran. Eftersom du kan skicka en batch med flera dokument till index-API: et samtidigt beror storleks gränsen på det realistiska sättet på hur många dokument som finns i gruppen. För en batch med ett enda dokument är maximal dokument storlek 16 MB JSON.

Kom ihåg att undanta data som inte går att köra från begäran för att bevara storleken på dokumentet. Bilder och andra binära data är inte direkt frågor och bör inte lagras i indexet. Om du vill integrera data som inte går att köra i Sök resultaten definierar du ett fält som inte går att söka i och lagrar en URL-referens till resursen.

## <a name="indexer-limits"></a>Indexerings gränser

Maximal körnings tid finns för att ge balans och stabilitet till tjänsten som helhet, men större data uppsättningar kan kräva mer indexerings tid än vad som tillåts. Om ett indexerings jobb inte kan slutföras inom den högsta tillåtna tiden kan du prova att köra det enligt ett schema. Scheduler håller reda på indexerings status. Om ett schemalagt indexerings jobb avbryts av någon anledning kan indexeraren Hämta var den senast slutade vid nästa schemalagda körning.


| Resurs | Kostnads&nbsp;fri<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal indexerare |3 |5 eller 15|50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximalt antal datakällor |3 |5 eller 15 |50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximalt färdighetsuppsättningar <sup>4</sup> |3 |5 eller 15 |50 |200 |200 |Ej tillämpligt |10 |10 |
| Maximal indexerings belastning per anrop |10 000 dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Ej tillämpligt |Obegränsad |Obegränsad |
| Lägsta schema | 5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter | 5 minuter |
| Maximal kör tid <sup>5</sup> | 1-3 minuter |24 timmar |24 timmar |24 timmar |24 timmar |Ej tillämpligt  |24 timmar |24 timmar |
| Maximal kör tid för kognitiv sökning färdighetsuppsättningar eller BLOB-indexering med bild analys <sup>5</sup> | 3-10 minuter |2 timmar |2 timmar |2 timmar |2 timmar |Ej tillämpligt  |2 timmar |2 timmar |
| BLOB-indexerare: maximal BLOB-storlek, MB |16 |16 |128 |256 |256 |Ej tillämpligt  |256 |256 |
| BLOB-indexerare: maximalt antal tecken för innehåll som extraherats från en BLOB |32 000 |64 000 |4&nbsp;miljoner |8&nbsp;miljoner |16&nbsp;miljoner |Ej tillämpligt |4&nbsp;miljoner |4&nbsp;miljoner |

<sup>1</sup> kostnads fria tjänster har indexeraren maximal körnings tid på 3 minuter för BLOB-källor och 1 minut för alla andra data källor. För AI-indexering som anropar Cognitive Services är kostnads fria tjänster begränsade till 20 kostnads fria transaktioner per dag, där en transaktion definieras som ett dokument som passerar genom anriknings pipelinen.

<sup>2</sup> grundläggande tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) på indexerare, data källor och färdighetsuppsättningar.

<sup>3</sup> S3 HD-tjänster inkluderar inte stöd för indexerare.

<sup>4</sup> maximalt 30 kunskaper per färdigheter.

<sup>5</sup> kognitiv sökning arbets belastningar och bild analys i Azure Blob-indexering har kortare körnings tider än vanlig text indexering. Bild analys och naturlig språk bearbetning är i beräknings intensiva och använder oproportionerliga mängder tillgängliga processor kraft. Körnings tiden sänktes för att ge andra jobb i kön en möjlighet att köra.  

> [!NOTE]
> Som det anges i [index gränserna](#index-limits)kommer indexerarna också att genomdriva den övre gränsen på 3000 element i alla komplexa samlingar per dokument som börjar med den senaste ga API-versionen som stöder`2019-05-06`komplexa typer () och senare. Det innebär att om du har skapat din indexerare med en tidigare API-version kommer du inte att omfattas av den här gränsen. För att bevara maximal kompatibilitet kommer en indexerare som har skapats med en tidigare API-version och sedan uppdaterats med `2019-05-06` en API-version eller senare fortfarande att **undantas** från gränserna. Kunderna bör vara medvetna om den negativa effekten av att ha mycket stora komplexa samlingar (som tidigare nämnts) och vi rekommenderar starkt att du skapar alla nya indexerare med den senaste GA API-versionen.

## <a name="synonym-limits"></a>Synonym gränser

Det högsta tillåtna antalet synonym Maps varierar beroende på pris nivå. Varje regel kan ha upp till 20 expansionar, där en expansion är en motsvarande period. Till exempel skulle "katt", Association med "Kitty", "Feline" och "Felis" (släktet för katter) räknas som 3 expansionar.

| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3 – HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximalt synonyma kartor |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximalt antal regler per karta |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Frågor per sekund (frågor per sekund)

FRÅGOR per sekund-uppskattningar måste utvecklas oberoende av varje kund. Index storlek och komplexitet, fråga om storlek och komplexitet och mängden trafik är primär Determinant av frågor per sekund. Det finns inget sätt att erbjuda meningsfulla uppskattningar när sådana faktorer är okända.

Uppskattningar är mer förutsägbara när de beräknas på tjänster som körs på dedikerade resurser (Basic-och standard-nivåer). Du kan beräkna frågor per sekund mer noggrant eftersom du har kontroll över fler parametrar. Information om hur du använder uppskattning finns i [Azure kognitiv sökning prestanda och optimering](search-performance-optimization.md).

För lagrings optimerade nivåer bör du förvänta dig ett lägre flöde för frågor och högre latens än standard-nivåerna.  Metoden för att uppskatta de frågeresultat som du kommer att uppleva är densamma som för standard-nivåerna.

## <a name="data-limits-ai-enrichment"></a>Data begränsningar (AI-anrikning)

En [pipeline för AI-anrikning](cognitive-search-concept-intro.md) som gör anrop till en textanalys resurs för [enhets igenkänning](cognitive-search-skill-entity-recognition.md), [extrahering av nyckel fraser](cognitive-search-skill-keyphrases.md), [sentiment-analys](cognitive-search-skill-sentiment.md), [språk identifiering](cognitive-search-skill-language-detection.md)och [identifiering av PII](cognitive-search-skill-pii-detection.md) är beroende av data begränsningar. Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till sentiment analys, använder du [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Begränsnings gränser

Sök frågor och indexerings begär Anden är begränsade som systemet närmar sig högsta kapacitet. Begränsningen fungerar på olika sätt för olika API: er. API: er för frågor (Sök/föreslå/komplettera om) och indexerings-API: er begränsar dynamiskt baserat på belastningen på tjänsten. Index-API: er har begränsningar för statisk begär ande hastighet. 

Begränsningar för statisk taxa för begäran för åtgärder relaterade till ett index:

+ List index (GET/Indexes): 5 per sekund per Sök enhet
+ Hämta index (GET/Indexes/myindex): 10 per sekund per Sök enhet
+ Skapa index (POST/Indexes): 12 per minut per Sök enhet
+ Skapa eller uppdatera index (Lägg/Indexes/myindex): 6 per sekund per Sök enhet
+ Ta bort index (ta bort/Indexes/myindex): 12 per minut per Sök enhet 

## <a name="api-request-limits"></a>Begränsningar för API-begäran
* Högst 16 MB per begäran <sup>1</sup>
* Maximal längd på 8 KB-URL
* Maximalt 1000 dokument per batch med index överföringar, sammanslagningar eller borttagningar
* Maximalt 32 fält i $orderby-satsen
* Maximal Sök villkors storlek är 32 766 byte (32 KB minus 2 byte) av UTF-8-kodad text

<sup>1</sup> i Azure kognitiv sökning är bröd texten i en begäran underkastad en övre gräns på 16 MB, vilket medför en praktisk gräns för innehållet i enskilda fält eller samlingar som inte på annat sätt begränsas av teoretiska gränser (se [data typer som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för mer information om fält sammansättning och begränsningar).

## <a name="api-response-limits"></a>Svars gränser för API
* Högst 1000 dokument som returneras per sida med Sök Resultat
* Högsta antal 100 förslag som returneras per föreslå API-begäran

## <a name="api-key-limits"></a>API-nyckel gränser
API-nycklar används för tjänsteautentisering. Det finns två typer. Administratörs nycklar anges i begär ande huvudet och beviljar fullständig Läs-och Skriv behörighet till tjänsten. Frågeinställningar är skrivskyddade, angivna i URL: en och distribueras vanligt vis till klient program.

* Högst 2 administrations nycklar per tjänst
* Högst 50 frågeinställningar per tjänst