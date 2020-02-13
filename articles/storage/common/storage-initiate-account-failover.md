---
title: Initiera en redundans för lagrings konto (för hands version) – Azure Storage
description: Lär dig hur du startar ett konto vid fel i händelse av att den primära slut punkten för ditt lagrings konto blir otillgänglig. Redundansväxlingen uppdaterar den sekundära regionen för att bli den primära regionen för ditt lagrings konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7529cfbd0ab75d0113e5cea666bc04aa1b15d30b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157728"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Initiera en redundans för lagrings konto (för hands version)

Om den primära slut punkten för ditt geo-redundanta lagrings konto inte är tillgänglig av någon anledning kan du initiera en konto redundansväxling (för hands version). Vid en konto redundansväxling uppdateras den sekundära slut punkten till den primära slut punkten för ditt lagrings konto. När redundansväxlingen är klar kan klienter börja skriva till den nya primära regionen. Med Tvingad redundans kan du upprätthålla hög tillgänglighet för dina program.

Den här artikeln visar hur du startar en konto redundansväxling för ditt lagrings konto med hjälp av Azure Portal, PowerShell eller Azure CLI. Om du vill veta mer om redundansväxling av kontot, se [haveri beredskap och konto redundans (för hands version) i Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Ett konto växling vid fel resulterar vanligt vis i viss data förlust. Om du vill förstå konsekvenserna av en redundansväxling av ett konto och förbereda dig för data förlust, kan du läsa [förstå processen för redundans av konto](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du kan utföra en redundansväxling på ditt lagrings konto måste du kontrol lera att du har utfört följande steg:

- Registrera dig för för hands versionen av konto redundansväxling. Information om hur du registrerar finns i [om förhands granskningen](storage-disaster-recovery-guidance.md#about-the-preview).
- Kontrol lera att ditt lagrings konto har kon figurer ATS för att använda antingen Geo-redundant lagring (GRS) eller Geo-redundant lagring med Läs behörighet (RA-GRS). Mer information om Geo-redundant lagring finns [Azure Storage redundans](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Viktiga följder vid redundansväxling av konto

När du initierar ett konto för redundans för ditt lagrings konto uppdateras DNS-posterna för den sekundära slut punkten så att den sekundära slut punkten blir den primära slut punkten. Se till att du förstår de potentiella konsekvenserna av ditt lagrings konto innan du påbörjar en redundansväxling.

Om du vill beräkna omfattningen av sannolik data förlust innan du påbörjar en redundansväxling, kontrollerar du den **senaste synkroniseringstid** -egenskapen med hjälp av `Get-AzStorageAccount` PowerShell-cmdleten och inkluderar parametern `-IncludeGeoReplicationStats`. Kontrol lera sedan `GeoReplicationStats` egenskapen för ditt konto. \

Efter redundansväxlingen konverteras lagrings konto typen automatiskt till lokalt redundant lagring (LRS) i den nya primära regionen. Du kan återaktivera Geo-redundant lagring (GRS) eller Geo-redundant lagring med Läs behörighet (RA-GRS) för kontot. Observera att konvertering från LRS till GRS eller RA-GRS ådrar sig en ytterligare kostnad. Mer information finns i [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

När du har aktiverat GRS för ditt lagrings konto börjar Microsoft replikera data i ditt konto till den nya sekundära regionen. Replikerings tiden beror på mängden data som replikeras.  

## <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

Följ dessa steg om du vill starta en redundansväxling av ett konto från Azure Portal:

1. Navigera till ditt lagringskonto.
2. Under **Inställningar**väljer du **geo-replikering**. Följande bild visar geo-replikering och redundans status för ett lagrings konto.

    ![Skärm bild som visar geo-replikering och redundans status](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Kontrol lera att ditt lagrings konto har kon figurer ATS för Geo-redundant lagring (GRS) eller Geo-redundant lagring med Läs behörighet (RA-GRS). Om den inte är det väljer du **konfiguration** under **Inställningar** för att uppdatera ditt konto till Geo-redundant. 
4. Egenskapen **tid för senaste synkronisering** anger hur långt den sekundära ligger bakom från den primära. **Tid för senaste synkronisering** ger en uppskattning av omfattningen av data förlust som du kommer att stöta på När redundansväxlingen har slutförts.
5. Välj **Förbered för redundans (för hands version)** . 
6. Läs igenom bekräftelse dialog rutan. När du är klar anger du **Ja** för att bekräfta och initiera redundansväxlingen.

    ![Skärm bild som visar bekräftelse dialog ruta för redundans av konto](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill använda PowerShell för att initiera en redundansväxling av ett konto måste du först installera 6.0.1 Preview-modulen. Följ de här stegen för att installera modulen:

1. Avinstallera tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med hjälp av inställningen **appar & funktioner** under **Inställningar**.
    - Ta bort alla **Azure** -moduler från `%Program Files%\WindowsPowerShell\Modules`.

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen när du har installerat PowerShellGet. 

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera en Azure Storage Preview-modul som stöder redundans av konton:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Stäng PowerShell-fönstret och öppna det igen.
 
Kör följande kommando för att starta en redundansväxling av ett konto från PowerShell:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande kommandon om du vill använda Azure CLI för att initiera en konto växling vid fel:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Nästa steg

- [Haveri beredskap och konto redundans (för hands version) i Azure Storage](storage-disaster-recovery-guidance.md)
- [Utforma högtillgängliga program med hjälp av RA GRS](storage-designing-ha-apps-with-ragrs.md)
- [Självstudie: Bygg ett program med hög tillgänglighet med Blob Storage](../blobs/storage-create-geo-redundant-storage.md) 
