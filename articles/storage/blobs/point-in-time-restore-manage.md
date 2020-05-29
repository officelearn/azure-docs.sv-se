---
title: Aktivera och hantera tidpunkts återställning för block-blobar (för hands version)
titleSuffix: Azure Storage
description: Lär dig hur du använder återställning av punkt-i-tid (för hands version) för att återställa block-blobar till ett tillstånd vid en tidigare tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fe98e04c37172dc6b91c86fab8200022ed860d4f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170111"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Aktivera och hantera tidpunkts återställning för block-blobar (för hands version)

Du kan använda tidpunkts återställning (för hands version) för att återställa block blobbar till sitt tillstånd vid en tidigare tidpunkt. Den här artikeln beskriver hur du aktiverar återställning av tidpunkter för ett lagrings konto med PowerShell. Det visar också hur du utför en återställnings åtgärd med PowerShell.

Mer information och information om hur du registrerar dig för för hands versionen finns i [punkt-i-tids återställning för block-blobs (för hands version)](point-in-time-restore-overview.md).

> [!CAUTION]
> Återställning vid tidpunkt stöder bara återställnings åtgärder på block-blobbar. Det går inte att återställa åtgärder på behållare. Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden [ta bort behållare](/rest/api/storageservices/delete-container) under för hands versionen av tidpunkten för återställning av tidpunkt, kan den behållaren inte återställas med en återställnings åtgärd. Ta bort enskilda blobbar under för hands versionen, i stället för att ta bort en behållare, om du kanske vill återställa dem.

> [!IMPORTANT]
> För hands versionen av tidpunkt för återställning är endast avsedd för användning utan produktion. Service nivå avtal (service avtal) för produktions tjänster är inte tillgängliga för närvarande.

## <a name="install-the-preview-module"></a>Installera Preview-modulen

Om du vill konfigurera Azure-återställning av Azure-tid med PowerShell installerar du först version [1.14.1 – Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) av AZ. Storage PowerShell-modulen. Följ de här stegen för att installera Preview-modulen:

1. Avinstallera tidigare installationer av Azure PowerShell från Windows med hjälp av inställningen **appar & funktioner** under **Inställningar**.

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen när du har installerat PowerShellGet.

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera AZ. Storage Preview-modulen:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Aktivera och konfigurera återställning av tidpunkter

Innan du aktiverar och konfigurerar återställning vid tidpunkter aktiverar du dess krav: mjuk borttagning, ändrings flöde och blob-versioner. Mer information om hur du aktiverar var och en av dessa funktioner finns i följande artiklar:

- [Aktivera mjuk borttagning för blobbar](soft-delete-enable.md)
- [Aktivera och inaktivera ändrings flödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)

Anropa kommandot enable-AzStorageBlobRestorePolicy för att konfigurera Azures tidpunkts återställning med PowerShell. I följande exempel aktive ras mjuk borttagning och ställer in lagrings perioden för mjuk borttagning, aktiverar ändrings flöde och aktiverar sedan återställning vid tidpunkter. Använd Azure Portal eller en Azure Resource Manager mall för att aktivera BLOB-versioner innan du kör exemplet.

Kom ihåg att ersätta värdena i vinkelparenteser med dina egna värden när du kör exemplet:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Utföra en återställnings åtgärd

Starta en återställnings åtgärd genom att anropa kommandot Restore-AzStorageBlobRange och ange återställnings punkten som ett UTC- **slutdatum** värde. Du kan ange lexicographical-intervall för blobbar som ska återställas eller utelämna ett intervall för att återställa alla blobbar i alla behållare i lagrings kontot. Upp till 10 lexicographical-intervall stöds per återställnings åtgärd. Återställnings åtgärden kan ta flera minuter att slutföra.

Tänk på följande regler när du anger ett intervall med blobbar som ska återställas:

- Behållar mönstret som har angetts för start intervallet och slut intervallet måste innehålla minst tre tecken. Det snedstreck (/) som används för att avgränsa ett behållar namn från ett BLOB-namn räknas inte mot det här minimivärdet.
- Upp till 10 intervall kan anges per återställnings åtgärd.
- Jokertecken stöds inte. De behandlas som standard tecken.
- Du kan återställa blobbar i `$root` `$web` behållaren och genom att uttryckligen ange dem i ett intervall som skickas till en återställnings åtgärd. - `$root` Och- `$web` behållare återställs endast om de uttryckligen anges. Andra system behållare kan inte återställas.

> [!IMPORTANT]
> När du utför en återställnings åtgärd, Azure Storage blockera data åtgärder på Blobbarna i de intervall som återställs under drift tiden. Läs-, skriv-och borttagnings åtgärder blockeras på den primära platsen. Därför kan åtgärder som att Visa behållare i Azure Portal inte utföras som förväntat medan återställnings åtgärden pågår.
>
> Läs åtgärder från den sekundära platsen kan fortsätta under återställnings åtgärden om lagrings kontot är geo-replikerat.

### <a name="restore-all-containers-in-the-account"></a>Återställ alla behållare i kontot

Om du vill återställa alla behållare och blobbar i lagrings kontot anropar du kommandot Restore-AzStorageBlobRange och utelämnar `-BlobRestoreRange` parametern. I följande exempel återställs behållare i lagrings kontot till deras tillstånd 12 timmar före det aktuella tillfället:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Återställa en enda uppsättning block blobbar

Om du vill återställa ett intervall med blobbar anropar du kommandot Restore-AzStorageBlobRange och anger ett lexicographical-intervall med behållare och blob-namn för `-BlobRestoreRange` parametern. Början av intervallet är i mängd och slutet av intervallet är exklusivt.

Om du till exempel vill återställa Blobbarna i en enda behållare med namnet *Sample-container*, kan du ange ett intervall som börjar med *exempel-container* och slutar med *Sample-container1*. Det finns inga krav på att behållarna med namnet i Start-och slut intervall ska finnas. Eftersom slutet på intervallet är exklusivt, även om lagrings kontot innehåller en behållare med namnet *Sample-container1*, kommer endast behållaren som heter *Sample-container* att återställas:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Om du vill ange en delmängd av blobbar i en behållare som ska återställas använder du ett snedstreck (/) för att avgränsa behållar namnet från BLOB-mönstret. Följande intervall väljer till exempel blobbar i en enda behållare vars namn börjar med bokstäverna *d* till *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Ange sedan intervallet till kommandot Restore-AzStorageBlobRange. Ange återställnings punkten genom att ange ett UTC- **datetime** -värde för `-TimeToRestore` parametern. I följande exempel återställs blobbar i det angivna intervallet till status 3 dagar före den aktuella tidpunkten:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Återställa flera intervall av block blobbar

Om du vill återställa flera intervall block blobbar anger du en matris med intervall för `-BlobRestoreRange` parametern. Upp till 10 intervall stöds per återställnings åtgärd. I följande exempel anges två intervall för att återställa det fullständiga innehållet i *container1* och *container4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Nästa steg

- [Återställning av tidpunkt för block-blobar (för hands version)](point-in-time-restore-overview.md)
- [Mjuk borttagning](soft-delete-overview.md)
- [Ändra feed (förhands granskning)](storage-blob-change-feed.md)
- [Blob-version (för hands version)](versioning-overview.md)
