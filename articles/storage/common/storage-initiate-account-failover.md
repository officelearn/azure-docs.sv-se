---
title: Skapa ett storage-konto failover (förhandsversion), Azure Storage
description: Lär dig hur du startar en konto-redundans i händelse av att den primära slutpunkten för ditt lagringskonto blir otillgänglig. Redundansen uppdaterar den sekundära regionen för att bli den primära regionen för ditt lagringskonto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: 1290a990515cd68f46d137c276e8bb36834ff482
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513493"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Påbörja en växling för storage-konto (förhandsversion)

Om den primära slutpunkten för ditt ra-GRS-konto blir otillgänglig av någon anledning, kan du initiera en konto-redundans (förhandsversion). En konto-redundans uppdaterar den sekundära slutpunkten om du vill bli den primära slutpunkten för ditt lagringskonto. När redundansväxlingen är klar, kan klienter börja skriva till den nya primära regionen. Forcerad redundans hjälper dig att upprätthålla hög tillgänglighet för dina program.

Den här artikeln visar hur du startar en konto-redundans för ditt lagringskonto med Azure-portalen, PowerShell eller Azure CLI. Mer information om kontot redundans finns [Disaster recovery och konto redundans (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> En konto-redundans uppstår vanligen data gå förlorade. Förstå konsekvenserna av en konto-redundans och förbereda för förlust av data, granska [konto redundans konfigureringen](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

## <a name="prerequisites"></a>Förutsättningar

Innan du kan utföra en konto-redundans på ditt lagringskonto måste du kontrollera att du har utfört följande steg:

- Registrera dig för förhandsversionen av konto redundans. Information om hur du registrerar finns i [om förhandsversionen av](storage-disaster-recovery-guidance.md#about-the-preview).
- Kontrollera att lagringskontot är konfigurerad för att använda geo-redundant lagring (GRS) eller read-access geo-redundant lagring (RA-GRS). Läs mer om geo-redundant lagring, [Geo-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Viktiga effekterna av konto redundans

När du har initierat en konto-redundans för ditt lagringskonto kan uppdateras DNS-posterna för den sekundära slutpunkten så att den sekundära slutpunkten blir den primära slutpunkten. Se till att du förstår den potentiella effekten till ditt lagringskonto innan du startar en redundansväxling.

Om du vill beräkna omfattningen av sannolikt dataförlust innan du startar en redundansväxling måste du kontrollera den **senaste synkroniseringstid** egenskapen med hjälp av den `Get-AzureRmStorageAccount` PowerShell-cmdleten och inkludera den `-IncludeGeoReplicationStats` parametern. Kontrollera sedan den `GeoReplicationStats` egenskapen för ditt konto. 

Efter redundansen kan konverteras din typ av lagringskonto automatiskt till lokalt redundant lagring (LRS) i den nya primära regionen. Du kan återaktivera geo-redundant lagring (GRS) eller read-access geo-redundant lagring (RA-GRS) för kontot. Observera att konvertering från LRS till GRS eller RA-GRS tillkommer en extra kostnad. Mer information finns i [prisinformation för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). 

När du återaktivera GRS för ditt lagringskonto, börjar Microsoft replikera data i ditt konto till den nya sekundära regionen. Replikeringstid beror på mängden data som replikeras.  

## <a name="azure-portal"></a>Azure Portal

Följ dessa steg för att initiera en konto-redundans från Azure portal:

1. Navigera till ditt lagringskonto.
2. Under **inställningar**väljer **Geo-replikering**. Följande bild visar status för geo-replikering och redundans för ett lagringskonto.

    ![Skärmbild som visar status för geo-replikering och redundans](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Kontrollera att ditt lagringskonto har konfigurerats för geo-redundant lagring (GRS) eller read-access geo-redundant lagring (RA-GRS). Om du inte väljer **Configuration** under **inställningar** att uppdatera ditt konto för att vara geo-redundant. 
4. Den **senaste synkroniseringstid** egenskapen anger hur långt sekundärt ligger bakom från primärt. **Senaste synkroniseringstid** ger en uppskattning av omfattningen av förlust av data som uppstår när redundansen är klar.
5. Välj **förbereda för redundans (förhandsversion)**. 
6. Granska i bekräftelsedialogrutan. När du är klar anger **Ja** att bekräfta och påbörja redundans.

    ![Skärmbild som visar bekräftelsedialogruta för kontot växling](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Om du vill använda PowerShell för att initiera en konto-redundans, måste du först installera 6.0.1 förhandsversionsmodulen. Följ stegen nedan för att installera modulen:

1. Avinstallera alla tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med hjälp av den **appar och funktioner** inställningen **inställningar**.
    - Ta bort alla **Azure*** moduler från `%Program Files%\WindowsPowerShell\Modules`.
    
1. Se till att du har den senaste versionen av installerat PowerShellGet. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Stäng och öppna PowerShell-fönstret när du har installerat PowerShellGet. 

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera ett Azure Storage-förhandsversionsmodulen som har stöd för Azure AD:
   
    ```powershell
    Install-Module -Name AzureRM.Storage -AllowPrerelease
    ```
1. Stäng och öppna PowerShell-fönstret.
 

Om du vill initiera en konto-redundans från PowerShell kör du följande kommando:

```powershell
Invoke-AzureRmStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Azure CLI

Om du vill använda Azure CLI för att initiera en konto-redundans, kör du följande kommandon:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Nästa steg

- [Disaster recovery och konto redundans (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md)
- [Utforma högtillgängliga program med hjälp av RA GRS](storage-designing-ha-apps-with-ragrs.md)
- [Självstudier: Skapa ett program med hög tillgänglighet med Blob storage](../blobs/storage-create-geo-redundant-storage.md) 
