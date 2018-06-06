---
title: Azure filer skalbarhets- och prestandamål | Microsoft Docs
description: Läs mer om skalbarhets- och prestandamål för Azure-filer, inklusive kapacitet, förfrågningar och gränser för inkommande och utgående bandbredd.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: beb3e5caf8c8dce9b2ea06bbd0a2ea5a4e05a714
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738082"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure filer
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB standardprotokoll. Den här artikeln beskrivs skalbarhets- och prestandamål för Azure-filer och Azure filen Sync (förhandsversion).

Skalbarhets- och prestandamål som listas här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan genomströmning för en fil också begränsas av den tillgängliga nätverksbandbredden inte bara de servrar som är värd för tjänsten för Azure-filer. Vi rekommenderar starkt att testa din användningsmönstret och ta reda på om skalbarhet och prestanda för Azure-filer uppfyller dina krav. Vi arbetar också öka gränserna över tid. Du gärna ge oss feedback, antingen i kommentarerna nedan eller på den [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilket begränsar du skulle vilja se oss öka.

## <a name="azure-storage-account-scale-targets"></a>Azure storage-konto skala mål
Den överordnade resursen för en Azure-filresursen är ett Azure storage-konto. Ett lagringskonto representerar lagringspoolen i Azure som kan användas av flera lagringstjänster, inklusive Azure-filer för att lagra data. Andra tjänster som lagrar data i storage-konton är Azure Blob storage, Azure Queue storage och Azure Table storage. Följande mål gäller alla storage-tjänster som lagrar data i ett lagringskonto:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Lagringsanvändningen konto från andra lagringstjänster påverkar din Azure-filresurser på ditt lagringskonto. Till exempel om du når den maximala lagringskapaciteten konto med Azure Blob storage kan kommer du inte att kunna skapa nya filer på din Azure-filresursen även om Azure-filresursen är lägre än den maximala delade storleken.

## <a name="azure-files-scale-targets"></a>Azure filer skala mål
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure filsynkronisering skala mål
Med Azure filsynkronisering har vi försökt så mycket som möjligt att utforma för obegränsad användning, men detta inte är alltid möjligt. I tabellen nedan visar gränserna i våra tester och vilka mål är faktiskt gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure filsynkronisering prestandamått
Eftersom Azure filsynkronisering agenten körs på en Windows Server-dator som ansluter till Azure-filresurser, effektiv sync prestanda beror på ett antal faktorer i infrastrukturen: Windows Server och den underliggande diskkonfigurationen nätverkets bandbredd mellan servern och Azure storage, storlek, totalt dataset storlek och aktiviteten för datamängden. Eftersom Azure filsynkronisering fungerar på filnivå, mäts bättre prestandaegenskaper för en Azure-filen Sync-baserad lösning av antalet objekt (filer och kataloger) som bearbetas per sekund. 
 
För Azure filsynkronisering är prestanda viktiga i två steg:
1. **Första enstaka etableringen**: för att optimera prestanda på första etableringen avser [Onboarding med Azure filsynkronisering](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) för optimala distributionsinformationen.
2. **En pågående synkronisering**: när data inledningsvis dirigeras i Azure-filresurser, Azure filsynkronisering håller flera slutpunkter synkroniserade.

För att hjälpa dig att planera distributionen för varje steg observeras nedan resultaten under interna tester på en dator med en konfiguration
| Systemkonfiguration |  |
|-|-|
| Processor | 64 virtuella kärnor med 64 MiB L3-cache |
| Minne | 128 GiB |
| Disk | SAS-diskar med RAID 10 med batteri säkerhetskopieras cache |
| Nätverk | 1 Gbit/s nätverk |
| Arbetsbelastning | Allmän filserver|

| Första enstaka etableringen  |  |
|-|-|
| Antal objekt | 10 miljoner objekt | 
| DataSet storlek| ~ 4 TiB |
| Genomsnittlig storlek | ~ 500 KiB (största filen: 100 GiB) |
| Överför genomflöde | 15 objekt per sekund |
| Namespace Download genomströmning * | 350 objekt per sekund |
 
* När en ny serverslutpunkt skapas ned Azure filsynkronisering agenten inte filen innehåll. Fullständig namnområdet synkroniseras först och sedan utlösare bakgrund återkalla för att hämta filer, antingen i sin helhet eller, om molnet skiktning är aktiverat i molnet lagringsnivåer princip på Serverslutpunkten.

| En pågående synkronisering  |   |
|-|--|
| Antal objekt som synkroniseras| 125,000 objekt (omsättningen ~ 1%) | 
| DataSet storlek| 50 giB |
| Genomsnittlig storlek | ~ 500 KiB (största filen: 100 GiB) |
| Överför genomflöde | 20 objekt per sekund |
| Fullständig hämtning genomströmning * | 30 objekt per sekund |
 
Om molnet skiktning är aktiverat, troligen kommer att se bättre prestanda som endast en del av filen data hämtas. Azure filsynkronisering endast hämtar data för cachelagrade filer när de ändras på någon av slutpunkterna. Agenten för nivåindelade eller nyligen skapade filer, hämta inte data i filen och i stället endast synkroniserar namnområdet till server-slutpunkter. Agenten stöder även partiella nedladdningar av nivåindelade filer som de används av användaren. 
 
> [!Note]  
> Talen ovan är inte en indikation på prestanda som du får. Den faktiska prestandan beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän vägledning för distributionen bör du behålla några saker i åtanke:
- Objekt-genomströmning skalas cirka i förhållande till antalet synkroniseringsgrupper på servern. Dela upp data i flera synkroniseringsgrupper på en server som ger bättre genomflöde, vilket också är begränsad av servern och nätverket.
- Objekt-genomströmning är omvänt proportionell till MiB per andra genomflöde. För mindre filer får högre dataflöde räknat antalet objekt som behandlas per sekund med lägre MiB per andra genomflöde. För större filer får du däremot färre objekt som behandlas per sekund med högre MiB per andra genomflöde. MiB per andra genomströmning begränsas av Azure-filer skala mål. 

## <a name="see-also"></a>Se också
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md)
- [Mål för skalbarhet och prestanda för andra storage-tjänster](../common/storage-scalability-targets.md)