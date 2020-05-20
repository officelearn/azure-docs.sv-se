---
title: Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto
titleSuffix: Azure Storage
description: Lär dig hur du kontrollerar den senaste synkroniseringstid-egenskapen för ett geo-replikerat lagrings konto. Den senaste synkroniseringstid-egenskapen anger efter hur lång tid alla skrivningar från den primära regionen skrevs till den sekundära regionen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 02f7d7e2735717a7a6e7a56273551197c16b77aa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659245"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto

När du konfigurerar ett lagrings konto kan du ange att dina data ska kopieras till en sekundär region som är hundratals mil från den primära regionen. Geo-replikering erbjuder tålighet för dina data i händelse av ett betydande avbrott i den primära regionen, t. ex. en natur katastrof. Om du dessutom aktiverar Läs åtkomst till den sekundära regionen förblir dina data tillgängliga för Läs åtgärder om den primära regionen blir otillgänglig. Du kan utforma ditt program för att växla sömlöst för att läsa från den sekundära regionen om den primära regionen inte svarar.

Geo-redundant lagring (GRS) och geo-Zone-redundant lagring (GZRS) replikerar båda dina data asynkront till en sekundär region. Om du vill ha Läs behörighet till den sekundära regionen ska du aktivera Geo-redundant lagring med Läs behörighet (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS). Mer information om de olika alternativen för redundans som erbjuds av Azure Storage finns [Azure Storage redundans](storage-redundancy.md).

I den här artikeln beskrivs hur du kontrollerar den **senaste synkroniseringstid** -egenskapen för ditt lagrings konto så att du kan utvärdera eventuella skillnader mellan den primära och den sekundära regionen.

## <a name="about-the-last-sync-time-property"></a>Om den senaste synkroniseringstid-egenskapen

Eftersom geo-replikering är asynkront, är det möjligt att data som skrivs till den primära regionen ännu inte har skrivits till den sekundära regionen vid den tidpunkt då ett avbrott inträffar. Den **senaste synkroniseringstid** -egenskapen anger den senaste gången data från den primära regionen skrevs till den sekundära regionen. Alla skrivningar till den primära regionen före den senaste synkroniseringstid-tiden är tillgängliga för läsning från den sekundära platsen. Skrivningar som gjorts till den primära regionen efter den senaste synkroniseringstid-egenskapen kan eller kanske inte är tillgänglig för läsningar ännu.

Den **senaste synkroniseringstid** -egenskapen är ett GMT-datum/tid-värde.

## <a name="get-the-last-sync-time-property"></a>Hämta den senaste synkroniseringstid-egenskapen

Du kan använda PowerShell eller Azure CLI för att hämta värdet för den **senaste synkroniseringstid** -egenskapen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill hämta den senaste synkroniseringen för lagrings kontot med PowerShell installerar du en version av modulen AZ. Storage som har stöd för att hämta geo-replikerings statistik. Exempel:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.14.0 –AllowClobber –Force
```

Kontrol lera sedan lagrings kontots **GeoReplicationStats. LastSyncTime** -egenskap. Kom ihåg att ersätta plats hållarnas värden med dina egna värden:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kontrol lera lagrings kontots **geoReplicationStats. lastSyncTime** -egenskap för att hämta den senaste synkroniseringstid för lagrings kontot med Azure CLI. Använd `--expand` parametern för att returnera värden för egenskaperna som är kapslade under **geoReplicationStats**. Kom ihåg att ersätta plats hållarnas värden med dina egna värden:

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

- [Azure Storage redundans](storage-redundancy.md)
- [Ändra redundans alternativ för ett lagrings konto](redundancy-migration.md)
- [Använd GEO-redundans för att skapa program med hög tillgänglighet](geo-redundant-design.md)