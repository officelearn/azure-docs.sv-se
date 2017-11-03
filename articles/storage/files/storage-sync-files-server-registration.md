---
title: "Registrera/avregistrera en server med Azure filsynkronisering (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du registrerar och Avregistrerar en Windows-Server med en Azure-filen Sync Storage Sync-tjänst."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Registrera/avregistrera en server med Azure filsynkronisering (förhandsgranskning)
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

I följande artikel illustrerar hur du registrerar och Avregistrerar en server med en Storage-synkroniseringstjänsten. Detta kan det vara önskvärt om en server tas ur drift eller om en ny Server-slutpunkt i en grupp för synkronisering. Se [hur du distribuerar Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md) information om hur du distribuerar Azure filsynkronisering slutpunkt till slutpunkt.

## <a name="prerequisites"></a>Krav
Om du vill registrera en Windows-Server med en tjänst för synkronisering av lagring, måste du förbereda en Windows-Server med kraven:

* Se till att en lagring Sync-tjänsten har distribuerats. Mer information om hur du distribuerar en tjänst för synkronisering av lagring finns [hur du distribuerar Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md).
* Kontrollera att servern är ansluten till internet och att Azure är tillgänglig.
* Inaktivera Förbättrad säkerhetskonfiguration för Internet Explorer för administratörer med Serverhanteraren UI.
    
    ![Serverhanteraren UI med den Förbättrad säkerhetskonfiguration markerat](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Kontrollera att AzureRM PowerShell-modulen är installerad på servern. Om servern är medlem i ett redundanskluster, kräver varje nod i klustret modulen AzureRM. Mer information om hur du installerar modulen AzureRM kan hittas på den [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av AzureRM PowerShell-modulen för att registrera/avregistrera en server. Om paketet AzureRM tidigare har installerats på den här servern (och PowerShell-versionen på den här servern är 5.* eller högre), kan du använda den `Update-Module` att uppdatera det här paketet. 

## <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med synkroniseringstjänsten för lagring
Innan en Windows-Server kan användas som en *Serverslutpunkt* i en Azure-filsynkronisering *Sync grupp*, måste den vara registrerad med en *lagring synkroniseringstjänsten*. En server kan bara registreras med en *lagring synkroniseringstjänsten* i taget.

### <a name="install-the-azure-file-sync-agent"></a>Installera Azure filsynkronisering agenten
1. [Ladda ned Azure filsynkronisering agent](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installationsprogrammet för agenten Azure filsynkronisering.
    
    ![Den första rutan i installationsprogrammet för agenten Azure filsynkronisering](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Glöm inte att aktivera uppdateringar av agenten för Azure filsynkronisering med Microsoft Update. Det är viktigt eftersom kritiska säkerhetskorrigeringar och funktionsförbättringar på server-paketet levereras via Microsoft Update.

    ![Kontrollera att Microsoft Update är aktiverat i fönstret Microsoft Update i installationsprogrammet för agenten Azure filsynkronisering](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare, visas registrera Användargränssnittet för servern omedelbart när installationen är klar.

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste filsynkronisering för Azure-agenten installeras på varje nod i klustret.

### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av Användargränssnittet för registreringen av servern

> [!Important]  
> Molnprenumerationer leverantör kan inte använda Användargränssnittet för registreringen av servern. Använd i stället PowerShell (under det här avsnittet).

1. Om inte registrera Användargränssnittet för servern startade omedelbart när du har slutfört installationen av agenten Azure filsynkronisering den kan startas manuellt genom att köra `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Klicka på *inloggning* åtkomst till din Azure-prenumeration. 

    ![Öppna dialogrutan i Användargränssnittet för servern registrering](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj korrekt prenumeration, resursgrupp och lagring-synkroniseringstjänsten från dialogrutan.

    ![Informationen om synkroniseringstjänsten](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I förhandsgranskning för krävs en mer inloggning för att slutföra processen. 

    ![Logga in dialogrutan](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste varje server kör registrera servern. När du visar de registrerade servrarna i Azure-portalen, Azure filsynkronisering automatiskt identifierar varje nod som en medlem i samma kluster för växling vid fel och för att gruppera dem på lämpligt sätt.

### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra registrera servern via PowerShell. Detta är det enda sättet att registrera servern för leverantör prenumerationer:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera servern med synkroniseringstjänsten för lagring
Det finns flera steg som krävs för att avregistrera en server med en Storage-synkroniseringstjänsten. Låt oss ta en titt på hur du avregistrerar en server korrekt.

### <a name="optional-recall-all-tiered-data"></a>(Valfritt) Återställa alla nivåindelade data
När aktiverat för en slutpunkt för Server, molnet lagringsnivåer kommer *nivå* filer till Azure-filresurser. Detta gör att lokala filresurser för att fungera som en cache i stället för en fullständig kopia av datauppsättningen, för att effektivt utnyttja utrymme på filservern. Men om en Server-slutpunkt har tagits bort med nivåindelade filer fortfarande lokalt på servern, blir filerna tillgängliga. Därför måste fortsatt åtkomst till filen önskas, du återkalla alla nivåindelade filer från Azure-filer innan du fortsätter med avregistrering. 

Detta kan göras med hjälp av PowerShell-cmdlet enligt nedan:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Om den lokala volymen som värd för servern inte har tillräckligt med ledigt utrymme för att återställa alla nivåindelade data i `Invoke-StorageSyncFileRecall` cmdlet misslyckas.  

### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla synkroniseringsgrupper
Innan du avregistrera servern på synkroniseringsservern lagring, tas Server-slutpunkter för servern bort. Detta kan göras via Portal:

1. Gå till lagring synkroniseringstjänsten där servern är registrerad.
2. Ta bort alla Server-slutpunkter för den här servern i varje synkronisering grupp i synkroniseringstjänsten för lagring. Detta kan åstadkommas genom att högerklicka på relevant Serverslutpunkten i fönstret Sync-grupp.

    ![Ta bort en Serverslutpunkt för från en grupp för synkronisering](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också utföras med ett enkelt PowerShell.skript:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Avregistrera servern
Nu när alla data har den återkallats och servern har tagits bort från alla synkroniseringsgrupper, kan servern avregistreras. 

1. Navigera avsnittet ”registrerade servrar” i lagringstjänsten för synkronisering i Azure-portalen.
2. Högerklicka på den server som du vill avregistrera och klicka på ”avregistrering av Server”.

    ![Avregistrera servern](media/storage-sync-files-server-registration/unregister-server-1.png)