---
title: Hantera registrerade servrar med Azure filsynkronisering (förhandsversion) | Microsoft Docs
description: Lär dig hur du registrerar och Avregistrerar en Windows-Server med en Azure-filen Sync Storage Sync-tjänst.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 7385e8b84668facf8bf44f569a611e7dcdba9a1e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738300"
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Hantera registrerade servrar med Azure filsynkronisering (förhandsgranskning)
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

I följande artikel visar hur du kan registrera och hantera en server med en Storage-synkroniseringstjänsten. Se [hur du distribuerar Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md) information om hur du distribuerar Azure filsynkronisering slutpunkt till slutpunkt.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrera/avregistrera en server med synkroniseringstjänsten för lagring
Registrera en server med Azure filsynkronisering upprättar en förtroenderelation mellan Windows Server och Azure. Den här relationen kan sedan användas för att skapa *server slutpunkter* på servern, som representerar specifika mappar som ska synkroniseras med en Azure-filresurs (även kallat en *molnet endpoint*). 

### <a name="prerequisites"></a>Förutsättningar
Om du vill registrera en server med en tjänst för synkronisering av lagring, måste du förbereda de nödvändiga förutsättningarna för servern:

* Servern måste köra en version av Windows Server stöds. Mer information finns i [versioner av Windows Server stöds](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Se till att en lagring Sync-tjänsten har distribuerats. Mer information om hur du distribuerar en tjänst för synkronisering av lagring finns [hur du distribuerar Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md).
* Kontrollera att servern är ansluten till internet och att Azure är tillgänglig.
* Inaktivera Förbättrad säkerhetskonfiguration för Internet Explorer för administratörer med Serverhanteraren UI.
    
    ![Serverhanteraren UI med den Förbättrad säkerhetskonfiguration markerat](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Kontrollera att AzureRM PowerShell-modulen är installerad på servern. Om servern är medlem i ett redundanskluster, kräver varje nod i klustret modulen AzureRM. Mer information om hur du installerar modulen AzureRM kan hittas på den [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av AzureRM PowerShell-modulen för att registrera/avregistrera en server. Om paketet AzureRM tidigare har installerats på den här servern (och PowerShell-versionen på den här servern är 5.* eller högre), kan du använda den `Update-Module` att uppdatera det här paketet. 
* Om du använder en proxyserver i din miljö kan du konfigurera proxyinställningar på servern för att synkronisera agenten ska använda.
    1. Fastställa din IP-adress och port proxynummer
    2. Redigera dessa två filer:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Lägg till rader i bild 1 (under det här avsnittet) under /System.ServiceModel i ovanstående två filer som ändrar 127.0.0.1:8888 rätt IP-adress (Ersätt 127.0.0.1) och rätt portnummer (Ersätt 8888):
    4. Ange WinHTTP-proxyinställningar via kommandoraden:
        * Visa proxyn: netsh winhttp visa proxy
        * Ange proxy: netsh winhttp ange proxy 127.0.0.1:8888
        * Återställ proxyn: netsh winhttp återställa proxy
        * Om detta är installationen när agenten har installerats startar du om våra sync-agent: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med synkroniseringstjänsten för lagring
Innan en server kan användas som en *serverslutpunkt* i en Azure-filsynkronisering *sync grupp*, måste den vara registrerad med en *lagring synkroniseringstjänsten*. En server kan endast registreras med en synkroniseringstjänsten för lagring i taget.

#### <a name="install-the-azure-file-sync-agent"></a>Installera Azure filsynkronisering agenten
1. [Ladda ned Azure filsynkronisering agent](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installationsprogrammet för agenten Azure filsynkronisering.
    
    ![Den första rutan i installationsprogrammet för agenten Azure filsynkronisering](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Glöm inte att aktivera uppdateringar av agenten för Azure filsynkronisering med Microsoft Update. Det är viktigt eftersom kritiska säkerhetskorrigeringar och funktionsförbättringar på server-paketet levereras via Microsoft Update.

    ![Kontrollera att Microsoft Update är aktiverat i fönstret Microsoft Update i installationsprogrammet för agenten Azure filsynkronisering](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare, visas registrera Användargränssnittet för servern omedelbart när installationen är klar.

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste filsynkronisering för Azure-agenten installeras på varje nod i klustret.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av Användargränssnittet för registreringen av servern
> [!Important]  
> Molnprenumerationer Solution Provider (CSP) kan inte använda Användargränssnittet för registreringen av servern. Använd i stället PowerShell (under det här avsnittet).

1. Om inte registrera Användargränssnittet för servern startade omedelbart när du har slutfört installationen av agenten Azure filsynkronisering den kan startas manuellt genom att köra `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Klicka på *inloggning* åtkomst till din Azure-prenumeration. 

    ![Öppna dialogrutan i Användargränssnittet för registreringen av servern](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj korrekt prenumeration, resursgrupp och lagring-synkroniseringstjänsten från dialogrutan.

    ![Informationen om synkroniseringstjänsten](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I förhandsgranskning för krävs en mer inloggning för att slutföra processen. 

    ![Logga in dialogrutan](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste varje server kör registrera servern. När du visar de registrerade servrarna i Azure-portalen, Azure filsynkronisering automatiskt identifierar varje nod som en medlem i samma kluster för växling vid fel och för att gruppera dem på lämpligt sätt.

#### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra registrera servern via PowerShell. Detta är det enda sättet att registrera servern för Cloud Solution Providers (CSP) prenumerationer:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera servern med synkroniseringstjänsten för lagring
Det finns flera steg som krävs för att avregistrera en server med en Storage-synkroniseringstjänsten. Låt oss ta en titt på hur du avregistrerar en server korrekt.

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, molnbaserad skiktning eller någon aspekt av Azure filsynkronisering av avregistreras och registrera en server, eller ta bort och återskapa server-slutpunkter såvida inte uttryckligen har angett till av en Microsoft-tekniker. Avregistrerar en server och ta bort server slutpunkter är en destruktiva åtgärd och nivåindelade filer i volymer med server-slutpunkter kommer inte ”återanslutas” till deras platser på Azure-filresursen när registrerade servern och server-slutpunkter återskapas, vilket leder synkroniserade fel. Observera också, skiktad filer som finns utanför en slutpunkt namnrymd kan vara förlorade permanent. Nivåindelad filer kan finnas inom server slutpunkter även om molnet skiktning aldrig har aktiverats.

#### <a name="optional-recall-all-tiered-data"></a>(Valfritt) Återställa alla nivåindelade data
Om du vill att filer som för närvarande nivåer för att vara tillgängliga efter att ta bort Azure filsynkronisering (dvs. Detta är en produktion, inte en test, miljö), kan du återställa alla filer på varje volym som innehåller server-slutpunkter. Inaktivera molnet skiktning för alla slutpunkter för server och kör följande PowerShell-cmdlet:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Om den lokala volymen värdslutpunkten server inte har tillräckligt med ledigt utrymme för att återställa alla nivåindelade data i `Invoke-StorageSyncFileRecall` cmdlet misslyckas.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla synkroniseringsgrupper
Server-slutpunkter på servern innan du avregistrera servern på synkroniseringsservern lagring måste tas bort. Detta kan göras via Azure portal:

1. Gå till lagring synkroniseringstjänsten där servern är registrerad.
2. Ta bort alla server-slutpunkter för den här servern i varje synkronisering grupp i synkroniseringstjänsten för lagring. Detta kan åstadkommas genom att högerklicka på relevant Serverslutpunkten i fönstret sync grupp.

    ![Ta bort en serverslutpunkt för från en grupp för synkronisering](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också utföras med ett enkelt PowerShell.skript:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Avregistrera servern
Nu när alla data har den återkallats och servern har tagits bort från alla synkroniseringsgrupper, kan servern avregistreras. 

1. I Azure portal, navigerar du till den *registrerade servrar* delen av synkroniseringstjänsten för lagring.
2. Högerklicka på den server som du vill avregistrera och klicka på ”avregistrering av Server”.

    ![Avregistrera server](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Att se till att Azure-filsynkronisering är en bra granne i ditt datacenter 
Eftersom Azure filsynkronisering sällan är den enda service körs i ditt datacenter, kanske du vill begränsa användningen av Azure filsynkronisering nätverk och lagring.

> [!Important]  
> Ange begränsningar för lågt påverkar prestandan för Azure filsynkronisering synkronisering och återkalla.

### <a name="set-azure-file-sync-network-limits"></a>Ange begränsningar för Azure filsynkronisering nätverk
Du kan begränsa nätverksanvändning för Azure filsynkronisering med hjälp av den `StorageSyncNetworkLimit` cmdlets. 

Du kan till exempel skapa en ny begränsningsvärdet så att Azure filsynkronisering inte använda fler än 10 Mbit/s mellan 9: 00 och 17: 00 (17:00-h) under Arbetsvecka: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Du kan se gränsen genom att använda följande cmdlet:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Ta bort nätverket gränser med `Remove-StorageSyncNetworkLimit`. Till exempel följande kommando tar bort alla gränser i nätverket:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Använda Windows QoS-serverlagring 
När Azure filsynkronisering finns på en virtuell dator som körs på en Windows Server-Virtualiseringsvärd, kan du använda QoS-lagring (lagringens tjänstkvalitet) att reglera lagring i/o-användning. Storage QoS-principen kan anges som maximalt (eller gränsen som hur StorageSyncNetwork gränsen tillämpas ovan) eller som ett minimum (eller reservation). Minst i stället för maximalt kan Azure filsynkronisering till burst använda tillgängligt lagringsutrymme bandbredd om andra arbetsbelastningar som inte använder den. Mer information finns i [Tjänstkvalitet för lagring](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Se också
- [Planera för distribution av en Azure-filsynkronisering (förhandsgranskning)](storage-sync-files-planning.md)
- [Distribuera Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md) 
- [Felsöka Azure filsynkronisering (förhandsgranskning)](storage-sync-files-troubleshoot.md)
