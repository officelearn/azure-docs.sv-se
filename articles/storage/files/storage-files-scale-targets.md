---
title: Skalbarhet för Azure Files och prestandamål
description: Lär dig mer om skalbarhets- och prestandamålen för Azure-filer, inklusive kapacitets-, begärandehastighet och inkommande och utgående bandbreddsbegränsningar.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 46c46faf8f7ee52978ae5542ab7ebd72a41b8357
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536460"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhet för Azure Files och prestandamål

[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via branschstandard SMB-protokollet. I den här artikeln beskrivs skalbarhets- och prestandamålen för Azure Files och Azure File Sync.

Skalbarhets- och prestandamålen som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Dataflödet för en fil kan till exempel också begränsas av din tillgängliga nätverksbandbredd, inte bara de servrar som är värdar för Azure Files-tjänsten. Vi rekommenderar starkt att du testar användningsmönstret för att avgöra om skalbarheten och prestandan för Azure-filer uppfyller dina krav. Vi är också fast beslutna att öka dessa gränser över tiden. Tveka inte att ge oss feedback, varken i kommentarerna nedan eller på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilka gränser du vill se oss öka.

## <a name="azure-storage-account-scale-targets"></a>Azure-lagringskontoskalamål

Den överordnade resursen för en Azure-filresurs är ett Azure-lagringskonto. Ett lagringskonto representerar en pool av lagring i Azure som kan användas av flera lagringstjänster, inklusive Azure-filer, för att lagra data. Andra tjänster som lagrar data i lagringskonton är Azure Blob storage, Azure Queue storage och Azure Table storage. Följande mål gäller alla lagringstjänster som lagrar data i ett lagringskonto:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Användning av lagringskonto för allmänt bruk från andra lagringstjänster påverkar dina Azure-filresurser i ditt lagringskonto. Om du till exempel når maximal lagringskontokapacitet med Azure Blob-lagring kan du inte skapa nya filer på din Azure-filresurs, även om din Azure-filresurs ligger under den maximala resursstorleken.

## <a name="azure-files-scale-targets"></a>Azure Files skala mål

Det finns tre kategorier av begränsningar att tänka på för Azure-filer: lagringskonton, resurser och filer.

Till exempel: Med premiumfilaktier kan en enda resurs uppnå 100 000 IOPS och en enda fil kan skala upp till 5 000 IOPS. Så om du har tre filer i en resurs, är den maximala IOPS du kan få från den resursen 15.000.

### <a name="standard-storage-account-limits"></a>Standardbegränsningar för lagringskonto

Se avsnittet [Azure storage account scale targets](#azure-storage-account-scale-targets) för dessa gränser.

### <a name="premium-filestorage-account-limits"></a>Begränsningar för premiumfillagringskonto

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Lagringskontogränser gäller för alla resurser. Det går bara att skala upp till max för FileStorage-konton om det bara finns en resurs per FileStorage-konto.

### <a name="file-share-and-file-scale-targets"></a>Mål för fildelning och filskala

> [!NOTE]
> Standardfilaktier större än 5 TiB har vissa begränsningar. En lista över begränsningar och instruktioner för att aktivera större fildelningsstorlekar finns i avsnittet [Aktivera större filresurser på standardfilresurser](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planeringsguiden.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync – skalningsmål

Azure File Sync har utformats med målet obegränsad användning, men obegränsad användning är inte alltid möjligt. I följande tabell anges gränserna för Microsofts testning och även vilka mål som är hårda gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync – prestandamått

Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurserna beror den effektiva synkroniseringsprestandan på ett antal faktorer i infrastrukturen: Windows Server och den underliggande diskkonfigurationen, nätverksbandbredden mellan servern och Azure-lagringen, filstorleken, den totala datauppsättningsstorleken och aktiviteten på datauppsättningen. Eftersom Azure File Sync fungerar på filnivå mäts prestandaegenskaperna för en Azure File Sync-baserad lösning bättre i antalet objekt (filer och kataloger) som bearbetas per sekund.

För Azure File Sync är prestanda avgörande i två steg:

1. **Inledande engångsetablering:** För att optimera prestanda vid inledande etablering, se [Introduktion med Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) för optimal distributionsinformation.
2. **Pågående synkronisering**: När data först har dirigerats i Azure-filresurserna håller Azure File Sync flera slutpunkter synkroniserade.

För att hjälpa dig att planera distributionen för varje steg, nedan är de resultat som observerats under den interna testningen på ett system med en konfidera

| Systemkonfiguration |  |
|-|-|
| Processor | 64 virtuella kärnor med 64 MiB L3-cache |
| Minne | 128 GiB |
| Disk | SAS-diskar med RAID 10 med batteriuppbackad cache |
| Nätverk | 1 Gbps-nätverk |
| Arbetsbelastning | Filserver för allmänt ändamål|

| Inledande engångsetablering  |  |
|-|-|
| Antal objekt | 25 miljoner objekt |
| Datauppsättningsstorlek| ~4.7 TiB |
| Genomsnittlig filstorlek | ~ 200 KiB (Största fil: 100 GiB) |
| Ladda upp dataflöde | 20 objekt per sekund per synkroniseringsgrupp |
| Dataflöde för namnområde* | 400 objekt per sekund |

*När en ny serverslutpunkt skapas hämtar Azure File Sync-agenten inget av filinnehållet. Den synkroniserar först det fullständiga namnområdet och utlöser sedan bakgrundsinkallelse för att hämta filerna, antingen i sin helhet eller, om molnnivådelning är aktiverad, till molnnivåprincipen som angetts på serverns slutpunkt.

| Pågående synkronisering  |   |
|-|--|
| Antal objekt synkroniserade| 125 000 objekt (~1% churn) |
| Datauppsättningsstorlek| 50 Gib (gib) |
| Genomsnittlig filstorlek | ~500 KiB |
| Ladda upp dataflöde | 20 objekt per sekund per synkroniseringsgrupp |
| Fullständigt dataflöde för nedladdning* | 60 objekt per sekund |

*Om molnnivådelning är aktiverat kommer du sannolikt att observera bättre prestanda eftersom endast en del av fildata hämtas. Azure File Sync hämtar bara data för cachelagrade filer när de ändras på någon av slutpunkterna. För alla nivåindelad eller nyligen skapade filer hämtar agenten inte fildata och synkroniserar i stället bara namnområdet till alla serverslutpunkter. Agenten stöder också partiella hämtningar av nivåindelade filer när de används av användaren. 

> [!Note]  
> Siffrorna ovan är inte en indikation på den prestanda som du kommer att uppleva. Den faktiska prestandan beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän guide för distributionen bör du tänka på några saker:

- Objektets dataflöde skalas ungefär i proportion till antalet synkroniseringsgrupper på servern. Att dela upp data i flera synkroniseringsgrupper på en server ger bättre dataflöde, vilket också begränsas av servern och nätverket.
- Objektets dataflöde är omvänt proportionellt mot MiB per sekund genomströmning. För mindre filer får du högre dataflöde när det gäller antalet objekt som bearbetas per sekund, men lägre MiB per sekund-dataflöde. Omvänt, för större filer, får du färre objekt bearbetas per sekund, men högre MiB per sekund dataflöde. MiB per sekund-dataflödet begränsas av Azure Files-skalningsmålen.

## <a name="see-also"></a>Se även

- [Planera för distribution av Azure Files](storage-files-planning.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
