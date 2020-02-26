---
title: Skalbarhets- och prestandamål i Azure filer
description: Läs mer om mål för skalbarhet och prestanda för Azure Files, inklusive kapaciteten, förfrågningar och gränser för inkommande och utgående bandbredd.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a74ec3610367193b5eee53ea0e0818901433e96
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598580"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure filer

[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet enligt bransch standard. Den här artikeln beskriver skalbarhets- och prestandamål för Azure Files och Azure File Sync.

Skalbarhets- och prestandamål som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan dataflödet för en fil också begränsas av den tillgängliga nätverksbandbredden inte bara de servrar som är värd för Azure Files-tjänsten. Vi rekommenderar starkt att testa ditt användningsmönster för att avgöra om skalbarhet och prestanda för Azure Files uppfyller dina krav. Vi arbetar också att öka gränserna över tid. Ovilliga inte att ge oss feedback, antingen i kommentarerna nedan eller på den [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilka gränser du vill se en ökning av USA.

## <a name="azure-storage-account-scale-targets"></a>Skala prestandamål för Azure storage-konto

Den överordnade resursen för en Azure-filresurs är ett Azure storage-konto. Ett lagringskonto representerar en lagringspool i Azure som kan användas av flera lagringstjänster, inklusive Azure Files för att lagra data. Andra tjänster som lagrar data i storage-konton är Azure Blob storage, Azure Queue storage och Azure Table storage. Följande gäller alla storage-tjänster som lagrar data i ett lagringskonto:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Generell användning av lagrings konto användningen från andra lagrings tjänster påverkar dina Azure-filresurser i ditt lagrings konto. Till exempel om du når den maximala lagringskapaciteten konto med Azure Blob storage kan kommer du inte att skapa nya filer på din Azure-filresurs, även om din Azure-filresurs är lägre än maximala filresursens storlek.

## <a name="azure-files-scale-targets"></a>Azure filer skala mål

Det finns tre typer av begränsningar att överväga för Azure Files: lagrings konton, resurser och filer.

Till exempel: med Premium-filresurser kan en enda resurs uppnå 100 000 IOPS och en enda fil kan skala upp till 5 000 IOPS. Så om du har tre filer i en resurs är det högsta antalet IOPS som du kan hämta från resursen 15 000.

### <a name="standard-storage-account-limits"></a>Standard gränser för lagrings konto

I avsnittet om [skalnings mål för Azure Storage-konto](#azure-storage-account-scale-targets) finns dessa gränser.

### <a name="premium-filestorage-account-limits"></a>Konto gränser för Premium-FileStorage

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Lagrings konto gränser gäller för alla resurser. Det går bara att skala upp till max för FileStorage-konton om det bara finns en resurs per FileStorage-konto.

### <a name="file-share-and-file-scale-targets"></a>Fil resurs-och fil skalnings mål

> [!NOTE]
> Standard fil resurser som är större än 5 TiB har vissa begränsningar och regionala begränsningar.
> En lista över begränsningar, regional information och instruktioner för att aktivera dessa större fil resurs storlekar finns i avsnittet om [att skapa större fil resurser](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planerings guiden.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål

Azure File Sync har utformats med målet obegränsad användning, men obegränsad användning är inte alltid möjlig. Följande tabell visar gränserna för Microsofts testning och indikerar även vilka mål som är hårda gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync-prestandamått

Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurser kan den effektiva synkroniseringsprestanda beror på ett antal faktorer i infrastrukturen för: Windows Server och den underliggande diskkonfigurationen nätverkets bandbredd mellan servern och Azure storage, filstorlek, totalt antal datauppsättningens storlek och aktiviteten för datauppsättningen. Eftersom Azure File Sync fungerar på filnivå, mäts bättre prestandaegenskaperna för en Azure File Sync-baserad lösning av antalet objekt (filer och kataloger) som bearbetas per sekund.

För Azure File Sync är prestanda viktiga i två steg:

1. **Första engångs etablering**: för att optimera prestanda vid inledande etablering, se [onboarding med Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) för den optimala distributions informationen.
2. **Pågående synkronisering**: när data ursprungligen har dirigerats i Azure-filresurserna, kan Azure File Sync hålla flera slut punkter synkroniserade.

När du planerar distributionen för vart och ett av stegen, observeras nedan resultaten under intern testning av ett system med en konfiguration

| Systemkonfiguration |  |
|-|-|
| Processor | 64 virtuella kärnor med 64 MiB L3-cache |
| Minne | 128 GiB |
| Disk | SAS-diskar med RAID 10 med batteri backas upp cache |
| Nätverk | 1 Gbit/s-nätverk |
| Arbetsbelastning | Allmän filserver|

| Den första enstaka etableringen  |  |
|-|-|
| Antal objekt | 25 000 000 objekt |
| Datauppsättningens storlek| ~ 4,7 TiB |
| Genomsnittlig filstorlek | ~ 200 KiB (största fil: 100 GiB) |
| Ladda upp dataflöde | 20 objekt per sekund per Sync-grupp |
| Namespace Download dataflöde * | 400 objekt per sekund |

\* När en ny serverslutpunkt skapas, hämta någon av filinnehållet inte av Azure File Sync-agenten. Programmet först synkroniseras fullständig namnområdet och sedan utlösare bakgrund återkallande för att hämta filer, antingen i sin helhet eller, om molnnivå är aktiverat i molnet lagringsnivåer princip på server-slutpunkt.

| Pågående synkronisering  |   |
|-|--|
| Antal objekt som synkroniseras| 125,000 objekt (~ 1% omsättning) |
| Datauppsättningens storlek| 50 giB |
| Genomsnittlig filstorlek | ~ 500 KiB |
| Ladda upp dataflöde | 20 objekt per sekund per Sync-grupp |
| Fullständig nedladdning dataflöde * | 60 objekt per sekund |

\* Om molnet lagringsnivåer har aktiverats kan du förmodligen att Observera bättre prestanda som bara en del av filen som data hämtas. Azure File Sync hämtas bara de data för cachelagrade filer när de ändras på någon av slutpunkterna. Agenten för nivåindelade eller nyligen skapade filer, hämta inte fildata och synkroniserar i stället endast namnområdet som ska alla serverslutpunkter. Agenten stöder även partiella nedladdningar av nivåindelade filer som de används av användaren. 

> [!Note]  
> Talen ovan är inte en indikation på prestanda som uppstår. Verkliga prestanda beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän vägledning för din distribution, bör du behålla några saker i åtanke:

- Objekt-dataflöde skalar cirka i proportion till antalet synkroniseringsgrupper på servern. Dela upp data i flera synkroniseringsgrupper på en server ger bättre dataflöde, vilket är också begränsat av servern och nätverket.
- Objektet dataflödet beror omvänt MiB per sekund dataflöde. För mindre filer får du högre dataflöde när det gäller antalet objekt som bearbetas per sekund med lägre MiB per sekund dataflöde. För större filer kan får du däremot färre objekt som bearbetas per sekund, men högre MiB per sekund dataflöde. MiB per sekund dataflödet begränsas av Azure Files skala mål.

## <a name="see-also"></a>Se även

- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
