---
title: 'Tjänst begränsningar för nivåer och SKU: er – Azure Search'
description: Tjänst begränsningar som används för kapacitets planering och högsta gräns för begär Anden och svar för Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 35beb55d7326b954a568a377b73696fe598742c5
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348294"
---
# <a name="service-limits-in-azure-search"></a>Tjänst begränsningar i Azure Search
De maximala gränserna för lagring, arbets belastningar och kvantiteter av index, dokument och andra objekt är beroende av om du [etablerar Azure Search](search-create-service-portal.md) på kostnads nivåer som är **kostnads fria**, **Basic**, **standard**eller **Storage** .

+ **Kostnads fri** är en delad tjänst för flera innehavare som medföljer din Azure-prenumeration.

+ **Basic** tillhandahåller dedikerade data bearbetnings resurser för produktions arbets belastningar i en mindre skala.

+ **Standard** körs på dedikerade datorer med mer lagrings-och bearbetnings kapacitet på alla nivåer. Standard finns på fyra nivåer: S1, S2, S3 och S3 HD.

+ **Optimerade lagrings enheter** på dedikerade datorer med mer total lagring, lagrings bandbredd och minne än **standard**. Optimerad lagring levereras på två nivåer: L1 och L2

> [!NOTE]
> Från och med 1 juli är alla nivåer allmänt tillgängliga, inklusive den optimerade lagrings nivån. Du hittar alla priser på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) .

  S3 High densitet (S3 HD) är utformad för specifika arbets belastningar: [flera innehavare](search-modeling-multitenant-saas-applications.md) och stora mängder små index (1 000 000 dokument per index, 3000 index per tjänst). Den här nivån tillhandahåller inte [funktionen Indexer](search-indexer-overview.md). På S3 HD måste data inmatningen utnyttja push-metoden, med API-anrop för att skicka data från källan till indexet. 

> [!NOTE]
> En tjänst tillhandahålls på en angiven nivå. Att flytta nivåer för att få kapacitet innebär att tillhandahålla en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [Välj en SKU eller nivå](search-sku-tier.md). Mer information om hur du justerar kapacitet i en tjänst som du redan har etablerad finns i [skala resurs nivåer för frågor och indexering av arbets belastningar](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Prenumerationsgränser
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Lagrings gränser
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index gränser

| Resource | Lediga | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal index |3 |5 eller 15 |50 |200 |200 |1 000 per partition eller 3 000 per tjänst |10 |10 |
| Maximalt antal enkla fält per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximalt antal komplexa samlings fält per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximalt antal element i alla komplexa samlingar per dokument |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximalt djup för komplexa fält |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximalt antal [förslag](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximala [bedömnings profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximalt antal funktioner per profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) för index. Basic-nivån är den enda SKU: n med en lägre gräns på 100 fält per index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokument gränser 

Från och med oktober 2018 finns det inte längre några dokument gränser för nya tjänster som skapats på någon fakturerbar nivå (Basic, S1, S2, S3, S3 HD) i vilken region som helst. Även om de flesta regioner har haft obegränsade dokument antal sedan november/december 2017 var det fem regioner som fortsatte att införa dokument gränser. Beroende på när och var du skapade en Sök tjänst kanske du kör en tjänst som fortfarande omfattas av dokument gränser.

För att avgöra om din tjänst har dokument gränser, se användnings panelen på sidan Översikt för din tjänst. Antalet dokument är antingen obegränsat eller kan omfattas av en gräns som baseras på nivån.

  ![Användningsikonen](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regioner som tidigare hade dokument gränser

Om portalen anger en dokument gräns, skapades tjänsten antingen före den senaste 2017, eller så skapades den i ett Data Center med hjälp av kluster med lägre kapacitet för värd tjänster Azure Search Services:

+ Östra Australien
+ Östasien
+ Indien, centrala
+ Västra Japan
+ Västra centrala USA

För tjänster som omfattas av dokument gränser gäller följande gräns värden:

|  Lediga | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1&nbsp;miljon |15 miljoner per partition eller 180 miljoner per tjänst |60 miljoner per partition eller 720 miljoner per tjänst |120 miljoner per partition eller 1,4 miljarder per tjänst |1 miljon per index eller 200 miljoner per partition |

Om tjänsten har gränser som blockerar dig skapar du en ny tjänst och publicerar sedan om allt innehåll till den tjänsten. Det finns ingen mekanism för att sömlöst etablera tjänsten på ny maskin vara i bakgrunden.

> [!Note] 
> För S3-tjänster med hög densitet som skapats efter sent 2017 har 200 000 000-dokumentet per partition tagits bort, men gränsen för 1 000 000-dokument per index kvarstår.


### <a name="document-size-limits-per-api-call"></a>Dokument storleks gränser per API-anrop

Maximal dokument storlek vid anrop till ett index-API är cirka 16 megabyte.

Dokument storleken är faktiskt en gräns för storleken på innehållet i index-API-begäran. Eftersom du kan skicka en batch med flera dokument till index-API: et samtidigt beror storleks gränsen på det realistiska sättet på hur många dokument som finns i gruppen. För en batch med ett enda dokument är maximal dokument storlek 16 MB JSON.

Kom ihåg att undanta data som inte går att köra från begäran för att bevara storleken på dokumentet. Bilder och andra binära data är inte direkt frågor och bör inte lagras i indexet. Om du vill integrera data som inte går att köra i Sök resultaten definierar du ett fält som inte går att söka i och lagrar en URL-referens till resursen.

## <a name="indexer-limits"></a>Indexerings gränser

Maximal körnings tid finns för att ge balans och stabilitet till tjänsten som helhet, men större data uppsättningar kan kräva mer indexerings tid än vad som tillåts. Om ett indexerings jobb inte kan slutföras inom den högsta tillåtna tiden kan du prova att köra det enligt ett schema. Scheduler håller reda på indexerings status. Om ett schemalagt indexerings jobb avbryts av någon anledning kan indexeraren Hämta var den senast slutade vid nästa schemalagda körning.


| Resource | Kostnads&nbsp;fri<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal indexerare |3 |5 eller 15|50 |200 |200 |Gäller inte |10 |10 |
| Maximalt antal datakällor |3 |5 eller 15 |50 |200 |200 |Gäller inte |10 |10 |
| Maximalt färdighetsuppsättningar <sup>4</sup> |3 |5 eller 15 |50 |200 |200 |Gäller inte |10 |10 |
| Maximal indexerings belastning per anrop |10 000 dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Endast begränsat av maximalt antal dokument |Gäller inte |Ingen gräns |Ingen gräns |
| Lägsta schema | 5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter | 5 minuter |
| Maximal kör tid <sup>5</sup> | 1-3 minuter |24 timmar |24 timmar |24 timmar |24 timmar |Gäller inte  |24 timmar |24 timmar |
| Maximal kör tid för kognitiv sökning färdighetsuppsättningar eller BLOB-indexering med bild analys <sup>5</sup> | 3-10 minuter |2 timmar |2 timmar |2 timmar |2 timmar |Gäller inte  |2 timmar |2 timmar |
| BLOB-indexerare: maximal BLOB-storlek, MB |16 |16 |128 |256 |256 |Gäller inte  |256 |256 |
| BLOB-indexerare: maximalt antal tecken för innehåll som extraherats från en BLOB |32,000 |64,000 |4&nbsp;miljoner |4&nbsp;miljoner |4&nbsp;miljoner |Gäller inte |4&nbsp;miljoner |4&nbsp;miljoner |

<sup>1</sup> kostnads fria tjänster har indexeraren maximal körnings tid på 3 minuter för BLOB-källor och 1 minut för alla andra data källor. För AI-indexering som anropar Cognitive Services är kostnads fria tjänster begränsade till 20 kostnads fria transaktioner per dag, där en transaktion definieras som ett dokument som passerar genom anriknings pipelinen.

<sup>2</sup> grundläggande tjänster som skapats före december 2017 har lägre gränser (5 i stället för 15) på indexerare, data källor och färdighetsuppsättningar.

<sup>3</sup> S3 HD-tjänster inkluderar inte stöd för indexerare.

<sup>4</sup> maximalt 30 kunskaper per färdigheter.

<sup>5</sup> kognitiv sökning arbets belastningar och bild analys i Azure Blob-indexering har kortare körnings tider än vanlig text indexering. Bild analys och naturlig språk bearbetning är i beräknings intensiva och använder oproportionerliga mängder tillgängliga processor kraft. Körnings tiden sänktes för att ge andra jobb i kön en möjlighet att köra.  

## <a name="queries-per-second-qps"></a>Frågor per sekund (frågor per sekund)

FRÅGOR per sekund-uppskattningar måste utvecklas oberoende av varje kund. Index storlek och komplexitet, fråga om storlek och komplexitet och mängden trafik är primär Determinant av frågor per sekund. Det finns inget sätt att erbjuda meningsfulla uppskattningar när sådana faktorer är okända.

Uppskattningar är mer förutsägbara när de beräknas på tjänster som körs på dedikerade resurser (Basic-och standard-nivåer). Du kan beräkna frågor per sekund mer noggrant eftersom du har kontroll över fler parametrar. Information om hur du använder uppskattning finns i [Azure Search prestanda och optimering](search-performance-optimization.md).

För lagrings optimerade nivåer bör du förvänta dig ett lägre flöde för frågor och högre latens än standard-nivåerna.  Metoden för att uppskatta de frågeresultat som du kommer att uppleva är densamma som för standard-nivåerna.

## <a name="data-limits-cognitive-search"></a>Data begränsningar (kognitiv sökning)

En [kognitiv Sök-pipeline](cognitive-search-concept-intro.md) som gör anrop till en textanalys resurs för [enhets igenkänning](cognitive-search-skill-entity-recognition.md), [extrahering av nyckel fraser](cognitive-search-skill-keyphrases.md), [sentiment analys](cognitive-search-skill-sentiment.md)och [språk identifiering](cognitive-search-skill-language-detection.md) är beroende av data begränsningar. Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till sentiment analys, använder du [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Begränsningar för API-begäran
* Högst 16 MB per begäran <sup>1</sup>
* Maximal längd på 8 KB-URL
* Maximalt 1000 dokument per batch med index överföringar, sammanslagningar eller borttagningar
* Maximalt 32 fält i $orderby-satsen
* Maximal Sök villkors storlek är 32 766 byte (32 KB minus 2 byte) av UTF-8-kodad text

<sup>1</sup> i Azure Search är bröd texten i en begäran underkastad en övre gräns på 16 MB, vilket föreskriver en praktisk gräns för innehållet i enskilda fält eller samlingar som inte på annat sätt begränsas av teoretiska gränser (se [data typer som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för mer information om fält sammansättning och begränsningar).

## <a name="api-response-limits"></a>Svars gränser för API
* Högst 1000 dokument som returneras per sida med Sök Resultat
* Högsta antal 100 förslag som returneras per föreslå API-begäran

## <a name="api-key-limits"></a>API-nyckel gränser
API-nycklar används för tjänsteautentisering. Det finns två typer. Administratörs nycklar anges i begär ande huvudet och beviljar fullständig Läs-och Skriv behörighet till tjänsten. Frågeinställningar är skrivskyddade, angivna i URL: en och distribueras vanligt vis till klient program.

* Högst 2 administrations nycklar per tjänst
* Högst 50 frågeinställningar per tjänst
