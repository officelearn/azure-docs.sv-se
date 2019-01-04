---
title: Definition av index och begrepp – Azure Search
description: Introduktion till indexet termer och begrepp i Azure Search, inklusive fysiska structre av ett vägar i inverterad index.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 40291b105eb39b44da0b0697f5808d819291e457
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630221"
---
# <a name="indexes-in-azure-search"></a>Index i Azure Search
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-an-index.md)
> * [Portalen](search-create-index-portal.md)
> * [NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

I Azure Search är ett *index* ett beständigt arkiv med *dokument* och andra konstruktioner som används av en Azure Search-tjänst. Ett dokument är en enskild enhet med sökbara data i ditt index. En återförsäljare som sysslar med e-handel kan till exempel ha ett dokument för varje artikel som företaget säljer, och en nyhetsorganisation kan ha ett dokument för varje nyhetsartikel osv. Om vi matchar dessa koncept till vanliga motsvarigheter i databasvärlden så kan ett *index* begreppsmässigt liknas vid en *tabell* och *dokument* kan grovt jämföras med *rader* i en tabell.

När du lägger till/laddar upp dokument och skickar sökfrågor till Azure Search skickar du dina förfrågningar till ett specifikt index i din söktjänst.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Fälttyper och fältattribut i ett Azure Search-index
När du definierar ett schema måste du ange namnet, typen och attributet för varje fält i ditt index. Fälttypen klassificerar de data som lagras i fältet. Attribut anges för enskilda fält och definierar hur fältet används. Följande tabeller innehåller de typer och attribut som du kan ange.

### <a name="field-types"></a>Fälttyper
| Typ | Beskrivning |
| --- | --- |
| *Edm.String* |Text som kan tokeniseras för textsökning (radbrytning, ordstamsigenkänning osv). |
| *Collection(Edm.String)* |En lista med strängar som kan tokeniseras för textsökning. Det finns ingen teoretisk övre gräns för antalet objekt i en samling, men den övre gränsen på 16 MB för nyttolasten gäller för samlingar. |
| *Edm.Boolean* |Innehåller sant/falskt-värden. |
| *Edm.Int32* |32-bitars heltalsvärden. |
| *Edm.Int64* |64-bitars heltalsvärden. |
| *Edm.Double* |Numeriska data med dubbel precision. |
| *Edm.DateTimeOffset* |Datum- och tidsvärden som representeras i OData V4-format (t.ex. `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |En punkt som representerar en geografisk plats i världen. |

Mer detaljerad information om [vilka datatyper som stöds i Azure Search finns här](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="field-attributes"></a>Fältattribut
| Attribut | Beskrivning |
| --- | --- |
| *Nyckel* |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| *Hämtningsbar* |Anger om ett fält kan returneras i sökresultat. |
| *Filtrerbar* |Gör att fältet kan användas i filterfrågor. |
| *Sorterbar* |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| *Fasettbar* |Gör att ett fält kan användas i en struktur för [aspektbaserad navigering](search-faceted-navigation.md) för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| *Sökbar* |Markerar fältet som fulltextsökbart. |

Mer detaljerad information om [indexattributen i Azure Search finns här](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="guidance-for-defining-an-index-schema"></a>Riktlinjer för att definiera ett indexschema
När du skapar ditt index bör du ta dig tid i planeringsfasen och tänka igenom varje beslut. Det är viktigt att du har användarupplevelsen och dina affärsbehov i åtanke när du utformar ditt index eftersom varje fält måste tilldelas [rätt egenskaper](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Om du behöver ändra ett index som har distribuerats måste du återskapa och läsa in dina data igen.

Om datalagringsbehovet ändras med tiden kan du öka eller minska kapaciteten genom att lägga till eller ta bort partitioner. Mer information finns i [Hantera din söktjänst i Azure](search-manage.md) och [Tjänstgränser](search-limits-quotas-capacity.md).

