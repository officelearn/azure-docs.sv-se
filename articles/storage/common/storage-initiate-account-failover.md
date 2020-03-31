---
title: Initiera en redundans för lagringskonto (förhandsversion) – Azure Storage
description: Lär dig hur du initierar en växlingsöverenskole i händelse av att den primära slutpunkten för ditt lagringskonto blir otillgänglig. Redundansen uppdaterar den sekundära regionen för att bli den primära regionen för ditt lagringskonto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535360"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Initiera en redundans för lagringskonto (förhandsversion)

Om den primära slutpunkten för ditt geoseranta lagringskonto blir otillgänglig av någon anledning kan du initiera en kontoundanslag (förhandsversion). En växlingshandvik för kontot uppdaterar den sekundära slutpunkten så att den blir den primära slutpunkten för ditt lagringskonto. När redundansen är klar kan klienter börja skriva till den nya primära regionen. Med en påtvingad redundans kan du behålla hög tillgänglighet för dina program.

Den här artikeln visar hur du initierar en kontoundanslag för ditt lagringskonto med Azure-portalen, PowerShell eller Azure CLI. Mer information om felinformation om kontot finns i [Katastrofåterställning och återställning av konton (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> En växlingsförlust för kontot resulterar vanligtvis i viss dataförlust. Om du vill förstå konsekvenserna av en kontoväxling och förbereda för dataförlust läser du Förstå processen för [att återställa kontot](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du kan utföra en felinformation för ditt lagringskonto måste du se till att du har utfört följande steg:

- Kontrollera att ditt lagringskonto är konfigurerat för att använda antingen geo-redundant lagring (GRS) eller läsåtkomst geo-redundant lagring (RA-GRS). Mer information om geo redundant lagring finns i [Azure Storage redundans](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Viktiga konsekvenser av failover för kontot

När du initierar en kontoväxling för ditt lagringskonto uppdateras DNS-posterna för den sekundära slutpunkten så att den sekundära slutpunkten blir den primära slutpunkten. Se till att du förstår den potentiella effekten på ditt lagringskonto innan du påbörjar en redundans.

Om du vill uppskatta omfattningen av den sannolika dataförlusten innan du `Get-AzStorageAccount` initierar en redundans `-IncludeGeoReplicationStats` kontrollerar du egenskapen **Senaste synkroniseringstid** med hjälp av PowerShell-cmdlet och inkluderar parametern. Kontrollera sedan `GeoReplicationStats` fastigheten för ditt konto. \

Efter redundansen konverteras lagringskontotypen automatiskt till lokalt redundant lagring (LRS) i den nya primära regionen. Du kan återaktivera geo redundant lagring (GRS) eller läsåtkomst geo-redundant lagring (RA-GRS) för kontot. Observera att konvertering från LRS till GRS eller RA-GRS medför en extra kostnad. Mer information finns i [Information om bandbreddsprissättning](https://azure.microsoft.com/pricing/details/bandwidth/).

När du har återaktivera GRS för ditt lagringskonto börjar Microsoft replikera data i ditt konto till den nya sekundära regionen. Replikeringstiden är beroende av mängden data som replikeras.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

Så här initierar du en kontoväxling från Azure-portalen:

1. Navigera till ditt lagringskonto.
2. Under **Inställningar**väljer du **Geo-replikering**. Följande bild visar geo-replikerings- och redundansstatus för ett lagringskonto.

    ![Skärmbild som visar georeplikering och redundansstatus](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Kontrollera att ditt lagringskonto är konfigurerat för geoupptundant lagring (GRS) eller geoundundant lagring (läsåtkomst geo redundant lagring ). Om det inte är det väljer du **Konfiguration** under **Inställningar** för att uppdatera ditt konto så att det är geouppsagt. 
4. Egenskapen **Senaste synkroniseringstid** anger hur långt sekundärt är efter från den primära. **Senaste synkroniseringstid** ger en uppskattning av omfattningen av dataförlust som du kommer att uppleva när redundansen har slutförts.
5. Välj **Förbered för redundans (förhandsgranskning)**. 
6. Granska bekräftelsedialogrutan. När du är klar anger du **Ja** för att bekräfta och initiera redundansen.

    ![Skärmbild som visar bekräftelsedialogruta för en växlingsbild för kontot](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill använda PowerShell för att starta en kontoundangång måste du först installera förhandsgranskningsmodulen 6.0.1. Så här installerar du modulen:

1. Avinstallera alla tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med inställningen **För appar & funktioner** under **Inställningar**.
    - Ta bort alla `%Program Files%\WindowsPowerShell\Modules` **Azure-moduler** från .

1. Kontrollera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng och öppna PowerShell-fönstret igen när du har installerat PowerShellGet. 

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera en förhandsversionsmodul för Azure Storage som stöder redundans för kontot:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Stäng och öppna PowerShell-fönstret igen.
 
Om du vill initiera en felinformation för kontot från PowerShell kör du följande kommando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att initiera en återställning av kontot kör du följande kommandon:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Nästa steg

- [Katastrofåterställning och felinformation om kontot (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md)
- [Utforma högtillgängliga program med hjälp av RA GRS](storage-designing-ha-apps-with-ragrs.md)
- [Självstudiekurs: Skapa ett program med högtillgänge med Blob-lagring](../blobs/storage-create-geo-redundant-storage.md) 
