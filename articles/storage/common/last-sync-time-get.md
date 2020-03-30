---
title: Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto
titleSuffix: Azure Storage
description: Lär dig hur du kontrollerar egenskapen **Senaste synkroniseringstid** för ett georedigerat lagringskonto. Egenskapen **Senaste synkroniseringstid** anger den senaste gången alla skrivningar från den primära regionen har skrivits till den sekundära regionen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165493"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto

När du konfigurerar ett lagringskonto kan du ange att dina data ska kopieras till en sekundär region som ligger hundratals miles från den primära regionen. Geo-replikering erbjuder hållbarhet för dina data i händelse av ett betydande avbrott i den primära regionen, till exempel en naturkatastrof. Om du dessutom aktiverar läsåtkomst till den sekundära regionen förblir dina data tillgängliga för läsåtgärder om den primära regionen blir otillgänglig. Du kan utforma ditt program så att det smidigt kan växlas till läsning från den sekundära regionen om den primära regionen inte svarar.

Geo-redundant lagring (GRS) och geo-zon-redundant lagring (GZRS) (förhandsvisning) båda replikera dina data asynkront till en sekundär region. För läsåtkomst till den sekundära regionen aktiverar du geo redundant lagring med läsåtkomst (RA-GRS) eller geo-zon redundant lagring (LÄSÅTKOMST). Mer information om de olika alternativen för redundans som erbjuds av Azure Storage finns i [Azure Storage redundans](storage-redundancy.md).

I den här artikeln beskrivs hur du kontrollerar egenskapen **Senaste synkroniseringstid** för ditt lagringskonto så att du kan utvärdera eventuella avvikelser mellan de primära och sekundära regionerna.

## <a name="about-the-last-sync-time-property"></a>Om egenskapen Senaste synkroniseringstid

Eftersom geo-replikering är asynkron, är det möjligt att data som skrivits till den primära regionen ännu inte har skrivits till den sekundära regionen vid den tidpunkt då ett avbrott inträffar. Egenskapen **Senaste synkroniseringstid** anger den senaste gången data från den primära regionen skrevs till den sekundära regionen. Alla skrivningar som görs till den primära regionen före den senaste synkroniseringstiden kan läsas från den sekundära platsen. Skrivningar som görs till den primära regionen efter den senaste synkroniseringstiden egenskapen kan eller inte kan vara tillgängliga för läsning ännu.

Egenskapen **Last Sync Time** är ett GMT-datum/tidsvärde.

## <a name="get-the-last-sync-time-property"></a>Hämta egenskapen Senaste synkroniseringstid

Du kan använda PowerShell eller Azure CLI för att hämta värdet för egenskapen **Senaste synkroniseringstid.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill få den senaste synkroniseringstiden för lagringskontot med PowerShell installerar du en azure storage-förhandsgranskningsmodul som stöder att få georeplikeringsstatistik. Till exempel:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Kontrollera sedan lagringskontots **GeoReplicationStats.LastSyncTime-egenskap.** Kom ihåg att ersätta platshållarvärdena med dina egna värden:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill hämta den senaste synkroniseringstiden för lagringskontot med Azure CLI kontrollerar du lagringskontots **geoReplicationStats.lastSyncTime-egenskap.** Använd `--expand` parametern för att returnera värden för de egenskaper som är kapslade under **geoReplicationStats**. Kom ihåg att ersätta platshållarvärdena med dina egna värden:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Se även

- [Redundans för Azure Storage](storage-redundancy.md)
- [Ändra redundansalternativet för ett lagringskonto](redundancy-migration.md)
- [Designa program med hög tillgänglighet med hjälp av geo redundant lagring med läsåtkomst](storage-designing-ha-apps-with-ragrs.md)