---
title: "Gränser i Azure Search | Microsoft Docs"
description: "Tjänstbegränsningarna som används för kapacitetsplanering och övre gräns för begäranden och -svar för Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 3deb0ff81114c840798c5927ad7311d7e603813d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="service-limits-in-azure-search"></a>Tjänstbegränsningarna i Azure Search
Gränsvärdet på lagring, arbetsbelastningar och mängder index, dokument och andra objekt är beroende av om du [etablera Azure Search](search-create-service-portal.md) på en **lediga**, **grundläggande**, eller **Standard** prisnivån.

* **Ledigt** är en delad tjänst för flera innehavare som medföljer din Azure-prenumeration. 
* **Grundläggande** ger dedikerade datorresurser för produktionsarbetsbelastningar i mindre skala.
* **Standard** körs på dedikerade datorer med mer kapacitet för lagring och bearbetning på varje nivå. Standard kommer i fyra nivåer: S1, S2, S3 och S3 hög densitet (S3 HD).

> [!NOTE]
> En tjänst har etablerats på en specifik nivå. Hoppa över nivåer för att få kapacitet innebär att etablera en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [Välj en SKU- eller nivå](search-sku-tier.md). Mer information om hur du justerar inom en tjänst som du redan har etablerat finns [skala resursen nivåer för fråga och indexering arbetsbelastningar](search-capacity-planning.md).
>

## <a name="per-subscription-limits"></a>Per prenumerationsbegränsningar
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="per-service-limits"></a>Per tjänstbegränsningarna
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="per-index-limits"></a>Per index gränser
Det finns en motsvarande mellan index begränsningar och gränser indexerare. Får högst 200 index är maxgränsen för indexerare också 200 för samma tjänst.

| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Index: maximala fält per index |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Index: maximalt bedömningen profiler per index |100 |100 |100 |100 |100 |100 |
| Index: maximala funktioner per profil |8 |8 |8 |8 |8 |8 |
| Indexerare: största indexering belastning per anrop |10 000 dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |EJ TILLÄMPLIGT <sup>2</sup> |
| Indexerare: maximala körtiden | 1-3 minuter <sup>3</sup> |24 timmar |24 timmar |24 timmar |24 timmar |EJ TILLÄMPLIGT <sup>2</sup> |
| BLOB-indexeraren: maximala blob, storlek i MB |16 |16 |128 |256 |256 |EJ TILLÄMPLIGT <sup>2</sup> |
| BLOB-indexeraren: maximala antalet tecken innehåll extraheras från ett blob |32,000 |64,000 |4 miljoner |4 miljoner |4 miljoner |EJ TILLÄMPLIGT <sup>2</sup> |

<sup>1</sup> grundnivån är endast SKU: N med en nedre gräns på 100 fält per index.

<sup>2</sup> S3 HD inte stöder indexerare. Kontakta Azure-supporten om du har angeläget för den här funktionen.

<sup>3</sup> indexeraren maximala körningstiden för den kostnadsfria nivån är 3 minuter för blobbkällorna och 1 minut för alla andra datakällor.

## <a name="document-size-limits"></a>Storleksgränser för dokumentet
| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Dokumentstorlek för enskilda per Index API |< 16 MB |< 16 MB |< 16 MB |< 16 MB |< 16 MB |< 16 MB |

Avser den maximala storleken när du anropar en API-Index. Dokumentet är faktiskt en gräns för storleken på begärandetexten Index API. Eftersom du kan överföra en grupp med flera dokument till indexet API på samma gång, beror faktiskt storleksgränsen på hur många dokument är i batchen. Den maximala storleken är 16 MB JSON för en grupp med ett enskilt dokument.

Kom ihåg att utesluta icke-frågbar data från begäran om du vill behålla storlek. Bilder och andra binära data är inte direkt frågbar och får inte lagras i indexet. Om du vill integrera icke frågbar data i sökresultat, definiera ett icke-sökbart fält som innehåller en URL: en referens till resursen.

## <a name="queries-per-second-qps"></a>Frågor per sekund (QPS)

QPS beräknar måste ha utvecklats oberoende av varje kund. Indexstorlek och komplexitet, fråga storleken och komplexiteten och mängden trafik är primär faktorerna för QPS. Det finns inget sätt att erbjuda meningsfulla beräknar när sådana faktorer är okänd.

Uppskattningar är mer förutsägbar i tjänster som körs på dedicerade resurser (nivåerna Basic och Standard). Du kan beräkna QPS mer noggrant eftersom du har kontroll över flera parametrar. Anvisningar om hur du metoden uppskattning finns [Azure Search-prestanda och optimering](search-performance-optimization.md).

## <a name="api-request-limits"></a>API-begärandebegränsningar
* Högst 16 MB per begäran <sup>1</sup>
* Maximal URL-längd 8 KB
* Maximal 1000 dokument per batch i indexet överför, sammanfogas eller tar bort
* Maximalt 32 fälten i satsen $orderby
* Maximal Sök är termen 32 766 byte (32 KB minus 2 byte) för UTF-8-kodat text

<sup>1</sup> i Azure Search brödtexten i en begäran regleras en övre gräns på 16 MB, införa en praktisk gräns på innehållet i enskilda fält eller samlingar som inte annars är begränsad av teoretisk gränser (se [data som stöds typer](https://msdn.microsoft.com/library/azure/dn798938.aspx) mer information om fältet sammansättning och begränsningar).

## <a name="api-response-limits"></a>API-svar gränser
* Maximal 1000 dokument som returneras per sida i sökresultat
* Maximalt 100 förslag som returneras per föreslår API-begäran

## <a name="api-key-limits"></a>API-nyckel gränser
API-nycklar används för autentiseringen av tjänsten. Det finns två typer. Admin nycklar anges i huvudet i begäran och ge fullständig skrivskyddad åtkomst till tjänsten. Frågan nycklar är skrivskyddad, anges i URL: en och distribuerad vanligtvis för klientprogram.

* Högst 2 admin nycklar per tjänst
* Högst 50 frågan nycklar per tjänst
