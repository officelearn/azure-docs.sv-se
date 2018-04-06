---
title: Gränser i Azure Search | Microsoft Docs
description: Tjänstbegränsningarna som används för kapacitetsplanering och övre gräns för begäranden och -svar för Azure Search.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 77ea75bf66f4b6ae6ec7d6ede4364db6b7fa82a0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="service-limits-in-azure-search"></a>Tjänstbegränsningarna i Azure Search
Gränsvärdet på lagring, arbetsbelastningar och mängder index, dokument och andra objekt är beroende av om du [etablera Azure Search](search-create-service-portal.md) på **lediga**, **grundläggande**, eller **Standard** prisnivåer.

+ **Ledigt** är en delad tjänst för flera innehavare som medföljer din Azure-prenumeration.

+ **Grundläggande** ger dedikerade datorresurser för produktionsarbetsbelastningar i mindre skala.

+ **Standard** körs på dedikerade datorer med mer kapacitet för lagring och bearbetning på varje nivå. Standard kommer i fyra nivåer: S1, S2, S3 och S3 HD.

  S3 (S3 HD) med hög densitet är utformad för specifika arbetsbelastningar: [multitenans](search-modeling-multitenant-saas-applications.md) och stora mängder små index (1 miljon dokument per index, tre tusen index per service). Det här skiktet innehåller inte den [indexeraren funktionen](search-indexer-overview.md). På S3 HD utnyttja datapåfyllning push-metoden med hjälp av API-anrop för pusha data från källan till index. 

> [!NOTE]
> En tjänst har etablerats på en specifik nivå. Hoppa över nivåer för att få kapacitet innebär att etablera en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [Välj en SKU- eller nivå](search-sku-tier.md). Mer information om hur du justerar inom en tjänst som du redan har etablerat finns [skala resursen nivåer för fråga och indexering arbetsbelastningar](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Prenumerationsbegränsningar
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Lagringsgränser
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index gränser

| Resurs | Gratis | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Maximalt antal index |3 |5 eller 15 |50 |200 |200 |1 000 per partition eller 3 000 per tjänst |
| Maximal fält per index |1000 |100 |1000 |1000 |1000 |1000 |
| Maximal bedömningsprofil profiler per index |100 |100 |100 |100 |100 |100 |
| Maximal funktioner per profil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> grundläggande tjänster som skapas efter sen 2017 har ökat högst 15 index, datakällor och indexerare. Tjänster som skapats tidigare ha 5. Grundnivån är endast SKU: N med en nedre gräns på 100 fält per index.

## <a name="document-limits"></a>Dokumentet gränser 

I de flesta regioner Azure Search prisnivåer (Basic, S1, S2, S3, S3 HD) har obegränsad dokumentantal för alla tjänster som skapats efter November/December 2017. Det här avsnittet identifierar de regioner där begränsningar tillämpas och hur du bestämmer om din tjänst påverkas. 

För att fastställa om din tjänst dokumentet gränser, kontrollera ikonen för användning på översiktssidan för din tjänst. Dokumentera antal är antingen obegränsade eller omfattas av en begränsning baserat på nivån.

  ![Ikonen användning](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Regioner och tjänster som har dokumentet gränser

Tjänster som har gränser skapats antingen innan du sen 2017 eller körs på datacenter med lägre kapacitet kluster som värd för Azure Search-tjänster. Berörda datacenter finns i följande områden:

+ Australien, östra
+ Asien, östra
+ Indien, centrala
+ Japan, västra
+ Västra centrala USA

För tjänster med dokumentet begränsningar gäller gränserna som följande:

|  Gratis | Grundläggande | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1 miljon |15 miljoner per partition eller 180 miljoner per tjänst |60 miljoner per partition eller 720 miljoner per tjänst |120 miljoner per partition eller 1,4 miljarder per tjänst |1 miljon per index eller 200 miljoner per partition |

> [!Note] 
> 200 miljoner dokument per partition har tagits bort men 1 miljon-dokument per index gränsen förblir för S3 hög densitet-tjänster som har skapats efter sen 2017.


### <a name="document-size-limits-per-api-call"></a>Dokumentet storleksgränser per API-anrop

Maximal dokumentstorleken när du anropar en API för Index är ungefär 16 MB.

Dokumentet är faktiskt en gräns för storleken på begärandetexten Index API. Eftersom du kan överföra en grupp med flera dokument till indexet API på samma gång, beror normalt storleksgränsen på hur många dokument är i batchen. Den maximala storleken är 16 MB JSON för en grupp med ett enskilt dokument.

Kom ihåg att utesluta icke-frågbar data från begäran om du vill behålla storlek. Bilder och andra binära data är inte direkt frågbar och får inte lagras i indexet. Om du vill integrera icke frågbar data i sökresultat, definiera ett icke-sökbart fält som innehåller en URL: en referens till resursen.

## <a name="indexer-limits"></a>Indexerare gränser

Grundläggande tjänster som skapats efter sen 2017 har ökat högst 15 index, datakällor och indexerare.

| Resurs | Ledigt&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Maximalt antal indexerare |3 |5 eller 15|50 |200 |200 |Saknas |
| Maximalt antal datakällor |3 |5 eller 15 |50 |200 |200 |Saknas |
| Maximal indexering belastning per anrop |10 000 dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |Begränsas bara av maximum dokument |Saknas |
| Maximal körtid | 1-3 minuter |24 timmar |24 timmar |24 timmar |24 timmar |Saknas  |
| BLOB-indexeraren: maximala blob, storlek i MB |16 |16 |128 |256 |256 |Saknas  |
| BLOB-indexeraren: maximala antalet tecken innehåll extraheras från ett blob |32,000 |64,000 |4 miljoner |4 miljoner |4 miljoner |Saknas |

<sup>1</sup> gratistjänster ha indexeraren maximala körningstiden för 3 minuter för blobbkällorna och 1 minut för alla andra datakällor.

<sup>2</sup> grundläggande tjänster som skapas efter sen 2017 har ökat högst 15 index, datakällor och indexerare. Tjänster som skapats tidigare ha 5.

<sup>3</sup> S3 HD-tjänster inte har stöd för indexering.

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
