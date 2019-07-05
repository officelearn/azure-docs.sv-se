---
title: Skalbarhets- och prestandamål i Azure filer | Microsoft Docs
description: Läs mer om mål för skalbarhet och prestanda för Azure Files, inklusive kapaciteten, förfrågningar och gränser för inkommande och utgående bandbredd.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 5/5/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 91ec65e17b77ccb3864fce45e30729ff420a48b6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542653"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure filer

[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Den här artikeln beskriver skalbarhets- och prestandamål för Azure Files och Azure File Sync.

Skalbarhets- och prestandamål som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan dataflödet för en fil också begränsas av den tillgängliga nätverksbandbredden inte bara de servrar som är värd för Azure Files-tjänsten. Vi rekommenderar starkt att testa ditt användningsmönster för att avgöra om skalbarhet och prestanda för Azure Files uppfyller dina krav. Vi arbetar också att öka gränserna över tid. Tveka inte att ge oss feedback, antingen i kommentarerna nedan eller på den [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilka begränsningar du vill se oss öka.

## <a name="azure-storage-account-scale-targets"></a>Skala prestandamål för Azure storage-konto

Den överordnade resursen för en Azure-filresurs är ett Azure storage-konto. Ett lagringskonto representerar en lagringspool i Azure som kan användas av flera lagringstjänster, inklusive Azure Files för att lagra data. Andra tjänster som lagrar data i storage-konton är Azure Blob storage, Azure Queue storage och Azure Table storage. Följande gäller alla storage-tjänster som lagrar data i ett lagringskonto:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Generell användning konto lagringsanvändningen från andra lagringstjänster påverkar Azure-filresurser i lagringskontot. Till exempel om du når den maximala lagringskapaciteten konto med Azure Blob storage kan kommer du inte att skapa nya filer på din Azure-filresurs, även om din Azure-filresurs är lägre än maximala filresursens storlek.

## <a name="azure-files-scale-targets"></a>Azure filer skala mål

Det finns tre typer av begränsningar att överväga för Azure Files: storage-konton, resurser och filer.

Exempel: En enda resurs kan uppnå 100 000 IOPS med premium-filresurser och en enskild fil kan skala upp till 5 000 IOPS. Så om du har tre filer i en resurs är den högsta IOPS som du kan hämta från resursen 15 000.

### <a name="standard-storage-account-limits"></a>Standard lagringskontogränser

Se den [prestandamål för Azure storage-konto skala](#azure-storage-account-scale-targets) för dessa gränser.

### <a name="premium-filestorage-account-limits"></a>Premium FileStorage gränser

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Lagringskontogränser gäller för alla resurser. Skala upp till är max för FileStorage konton endast möjligt om det finns bara en resurs per FileStorage konto.

### <a name="file-share-and-file-scale-targets"></a>Filresursen och filen skala mål

> [!NOTE]
> Standard filresurser som är större än 5 TiB är i förhandsversion och har vissa begränsningar.
> En lista av begränsningar och kom igång med förhandsversionen av dessa större filstorlekar för resursen finns i den [Standard filresurser](storage-files-planning.md#standard-file-shares) avsnitt av Planeringsguiden.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync skala mål

Azure File Sync har utformats med målet att obegränsad användning, men obegränsad användning är inte alltid möjligt. I följande tabell anger gränserna för Microsofts testning och anger även vilken riktar sig gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync-prestandamått

Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurser kan beror den effektiva synkroniseringsprestanda på ett antal faktorer i din infrastruktur: Windows Server och den underliggande diskkonfigurationen nätverksbandbredden mellan servern och Azure storage, file storlek, totalt antal datauppsättningens storlek och aktiviteten på datauppsättningen. Eftersom Azure File Sync fungerar på filnivå, mäts bättre prestandaegenskaperna för en Azure File Sync-baserad lösning av antalet objekt (filer och kataloger) som bearbetas per sekund.

För Azure File Sync är prestanda viktiga i två steg:

1. **Den första etableringen enstaka**: För att optimera prestanda på den första etableringen, referera till [registrering med Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) för optimal distributionsinformationen.
2. **Pågående synkronisering**: När data är inledningsvis dirigeras i Azure-filresurser, behåller Azure File Sync flera slutpunkter synkroniserade.

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
| Antal objekt | 25 miljoner objekt |
| Datauppsättningens storlek| ~4.7 TiB |
| Genomsnittlig filstorlek | ~ 200 KiB (största filen: 100 GiB) |
| Ladda upp dataflöde | 20 objekt per sekund |
| Namespace Download dataflöde * | 400 objekt per sekund |

\* När en ny serverslutpunkt skapas, hämta någon av filinnehållet inte av Azure File Sync-agenten. Programmet först synkroniseras fullständig namnområdet och sedan utlösare bakgrund återkallande för att hämta filer, antingen i sin helhet eller, om molnnivå är aktiverat i molnet lagringsnivåer princip på server-slutpunkt.

| Pågående synkronisering  |   |
|-|--|
| Antal objekt som synkroniseras| 125,000 objekt (~ 1% omsättning) |
| Datauppsättningens storlek| 50 giB |
| Genomsnittlig filstorlek | ~ 500 KiB |
| Ladda upp dataflöde | 30 objekt per sekund |
| Fullständig nedladdning dataflöde * | 60 objekt per sekund |

\* Om molnet lagringsnivåer har aktiverats kan du förmodligen att Observera bättre prestanda som bara en del av filen som data hämtas. Azure File Sync hämtas bara de data för cachelagrade filer när de ändras på någon av slutpunkterna. Agenten för nivåindelade eller nyligen skapade filer, hämta inte fildata och synkroniserar i stället endast namnområdet som ska alla serverslutpunkter. Agenten stöder även partiella nedladdningar av nivåindelade filer som de används av användaren. 

> [!Note]  
> Talen ovan är inte en indikation på prestanda som uppstår. Verkliga prestanda beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän vägledning för din distribution, bör du behålla några saker i åtanke:

- Objekt-dataflöde skalar cirka i proportion till antalet synkroniseringsgrupper på servern. Dela upp data i flera synkroniseringsgrupper på en server ger bättre dataflöde, vilket är också begränsat av servern och nätverket.
- Objektet dataflödet beror omvänt MiB per sekund dataflöde. För mindre filer får du högre dataflöde när det gäller antalet objekt som bearbetas per sekund med lägre MiB per sekund dataflöde. För större filer kan får du däremot färre objekt som bearbetas per sekund, men högre MiB per sekund dataflöde. MiB per sekund dataflödet begränsas av Azure Files skala mål.

## <a name="see-also"></a>Se också

- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Mål för skalbarhet och prestanda för andra storage-tjänster](../common/storage-scalability-targets.md)
