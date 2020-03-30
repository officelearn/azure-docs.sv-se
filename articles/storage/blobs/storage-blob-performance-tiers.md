---
title: Prestandanivåer för blockbloblagring – Azure Storage
description: I artikeln beskrivs skillnaden mellan premium- och standardprestandanivåer för Azure-blockbloloblagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270229"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Prestandanivåer för blockbloloblagring

När företag distribuerar prestandakänsliga molnbaserade program är det viktigt att ha alternativ för kostnadseffektiv datalagring på olika prestandanivåer.

Azure block blob storage erbjuder två olika prestandanivåer:

- **Premium**: optimerad för höga transaktionshastigheter och ensiffriga konsekventa lagringsfördröjningar
- **Standard**: optimerad för hög kapacitet och hög genomströmning

Följande överväganden gäller för de olika prestandanivåerna:

| Område |Standardprestanda  |Premium prestanda  |
|---------|---------|---------|
|Regional tillgänglighet     |   Alla regioner      | I [vissa områden](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Typer av [lagringskonto som](../common/storage-account-overview.md#types-of-storage-accounts) stöds     |     Allmänt ändamål v2, BlobStorage, Allmänt ändamål v1    |    BlockBlobStorage     |
|Stöder [blockblobar](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) med högt dataflöde     |    Ja, på större än 4 MiB PutBlock eller PutBlob storlekar     |    Ja, på större än 256 KiB PutBlock eller PutBlob storlekar    |
|Redundans     |     Se [Typer av lagringskonton](../common/storage-account-overview.md#types-of-storage-accounts)   |  Stöder för närvarande endast lokalt redundant lagring (LRS) och zon-redudant lagring (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1.</sup> Zonupptraktad lagring (ZRS) är tillgänglig i vissa regioner för premiumprestandablock blob storage-konton.</div>

När det gäller kostnader ger premiumprestanda optimerad prissättning för program med höga transaktionshastigheter för att [sänka den totala lagringskostnaden](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) för dessa arbetsbelastningar.

## <a name="premium-performance"></a>Premium prestanda

Premium prestanda block blob lagring gör data tillgängliga via högpresterande hårdvara. Data lagras på SSD-enheter (Solid State Drives) som är optimerade för låg latens. SSD ger högre dataflöde jämfört med traditionella hårddiskar.

Premium prestandalagring är idealisk för arbetsbelastningar som kräver snabba och konsekventa svarstider. Det är bäst för arbetsbelastningar som utför många små transaktioner. Exempel på arbetsbelastningar är:

- **Interaktiva arbetsbelastningar**. Dessa arbetsbelastningar kräver omedelbara uppdateringar och användarfeedback, till exempel e-handel och mappningsprogram. I ett e-handelsprogram cachelagras till exempel mindre ofta visade objekt troligen inte. De måste dock visas direkt för kunden på begäran.

- **Analys**. I ett IoT-scenario kan många mindre skrivåtgärder skickas till molnet varje sekund. Stora mängder data kan tas in, aggregeras för analysändamål och sedan tas bort nästan omedelbart. De höga inmatningsfunktionerna för premiumblockbloblagring gör det effektivt för den här typen av arbetsbelastning.

- **Artificiell intelligens/maskininlärning (AI/ML).** AI/ML hanterar förbrukning och bearbetning av olika datatyper som visuella objekt, tal och text. Den här högpresterande datortypen av arbetsbelastning behandlar stora mängder data som kräver snabba svarstider och effektiva inmatningstider för dataanalys.

- **Dataomvandling**. Processer som kräver konstant redigering, ändring och konvertering av data kräver omedelbara uppdateringar. För korrekt datarepresentation måste konsumenterna av dessa uppgifter se dessa ändringar återspeglas omedelbart.

## <a name="standard-performance"></a>Standardprestanda

Standardprestanda stöder olika [åtkomstnivåer](storage-blob-storage-tiers.md) för att lagra data på det mest kostnadseffektiva sättet. Den är optimerad för hög kapacitet och hög genomströmning på stora datamängder.

- **Datauppsättningar för säkerhetskopiering och haveriberedskap**. Standardprestandalagring erbjuder kostnadseffektiva nivåer, vilket gör det till ett perfekt användningsfall för både kortsiktiga och långsiktiga datauppsättningar för haveriberedskap, sekundära säkerhetskopieringar och arkivering av efterlevnadsdata.

- **Medieinnehåll**. Bilder och videor används ofta ofta när de först skapas och lagras, men den här innehållstypen används mindre ofta när den blir äldre. Standardprestandalagring erbjuder lämpliga nivåer för medieinnehållsbehov. 

- **Massdatabehandling**. Dessa typer av arbetsbelastningar är lämpliga för standardlagring eftersom de kräver kostnadseffektiv lagring med högt dataflöde i stället för konsekvent låg latens. Stora, råa datauppsättningar är iscensatta för bearbetning och migrera så småningom till svalare nivåer.

## <a name="migrate-from-standard-to-premium"></a>Migrera från standard till premium

Du kan inte konvertera ett befintligt standardkonto för prestandalagring till ett blockblobblagringskonto med premiumprestanda. Om du vill migrera till ett premiumkonto för prestandalagring måste du skapa ett BlockBlobStorage-konto och migrera data till det nya kontot. Mer information finns i [Skapa ett BlockBlobStorage-konto](storage-blob-create-account-block-blob.md).

Om du vill kopiera blobbar mellan lagringskonton kan du använda den senaste versionen av kommandoradsverktyget [AzCopy.](../common/storage-use-azcopy-blobs.md) Andra verktyg som Azure Data Factory är också tillgängliga för data förflyttning och omvandling.

## <a name="blob-lifecycle-management"></a>Blob livscykelhantering

Livscykelhantering för bloblagring erbjuder en omfattande, regelbaserad princip:

- **Premium**: Förfalla data i slutet av livscykeln.
- **Standard**: Övergångsdata till den bästa åtkomstnivån och förfallodata i slutet av livscykeln.

Mer information finns [i Hantera azure blob-lagringslivscykeln](storage-lifecycle-management-concepts.md).

Du kan inte flytta data som lagras i ett premiumblockbloblagringskonto mellan frekventa, svala och arkivnivåer. Du kan dock kopiera blobbar från ett blockblobblagringskonto till hot access-nivån i ett *annat* konto. Om du vill kopiera data till ett annat konto använder du [API:et För att blockera från URL](/rest/api/storageservices/put-block-from-url) eller [AzCopy v10](../common/storage-use-azcopy-v10.md). **Api:et Placera blockera från** URL kopierar avsiktligt data på servern. Anropet slutförs först när alla data har flyttats från den ursprungliga serverplatsen till målplatsen.

## <a name="next-steps"></a>Nästa steg

Utvärdera frekventa, svala och arkivera i GPv2- och Blob-lagringskonton.

- [Lär dig mer om att rehydrera blob-data från arkivnivån](storage-blob-rehydration.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)
- [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
