---
title: Hantera registrerade servrar med Azure File Sync | Microsoft Docs
description: Lär dig hur du registrerar och Avregistrerar en Windows-Server med en Azure File Sync Lagringssynkroniseringstjänst.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 493f6f3380dee4ed70bb6e0bc9bba24f93071097
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165339"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Hantera registrerade servrar med Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

I följande artikel visar hur du kan registrera och hantera en server med en Lagringssynkroniseringstjänst. Se [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) information om hur du distribuerar Azure File Sync slutpunkt till slutpunkt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrera/avregistrera en server med Storage Sync-tjänsten
Registrera en server med Azure File Sync upprättar en förtroenderelation mellan Windows Server och Azure. Den här relationen kan sedan användas för att skapa *serverslutpunkter* på servern, som visar specifika mappar som ska synkroniseras med en Azure-filresurs (även kallat en *Molnets slutpunkt*). 

### <a name="prerequisites"></a>Förutsättningar
Om du vill registrera en server med en Lagringssynkroniseringstjänst, måste du förbereda de nödvändiga förutsättningarna för servern:

* Servern måste köra en version av Windows Server stöds. Mer information finns i [Azure File Sync-systemkrav och samverkan](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Se till att en Storage Sync-tjänsten har distribuerats. Läs mer om hur du distribuerar en tjänst för Lagringssynkronisering [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).
* Kontrollera att servern är ansluten till internet och att Azure är tillgänglig.
* Inaktivera Förbättrad säkerhetskonfiguration för administratörer med Serverhanteraren UI.
    
    ![Serverhanteraren UI med den Förbättrad säkerhetskonfiguration markerat](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Kontrollera att Azure PowerShell-modulen är installerad på servern. Om servern är medlem i ett redundanskluster, kräver varje nod i klustret Az-modulen. Mer information om hur du installerar Az-modulen kan hittas på den [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av Az PowerShell-modulen för att registrera/avregistrera en server. Om Az-paketet har installerats tidigare på den här servern (och PowerShell-versionen på den här servern är 5.* eller senare), du kan använda den `Update-Module` cmdlet för att uppdatera det här paketet. 
* Om du använder en proxyserver i din miljö kan du konfigurera proxyinställningar på servern om sync-agenten ska kunna använda.
    1. Fastställa din IP-adress och port proxynummer
    2. Redigera dessa två filer:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Lägg till rader i bild 1 (under det här avsnittet) under /System.ServiceModel i ovanstående två filer som ändrar 127.0.0.1:8888 till rätt IP-adress (Ersätt 127.0.0.1) och rätt portnummer (Ersätt 8888):
    4. Ange WinHTTP-proxyinställningarna via kommandoraden:
        * Visa proxyn: netsh winhttp visa proxy
        * Ange proxy: netsh winhttp ange proxy 127.0.0.1:8888
        * Återställ proxyn: netsh winhttp återställa proxy
        * Om detta har konfigurerats när agenten har installerats, startar du om vårt sync-agenten: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med Storage Sync-tjänsten
Innan en server kan användas som en *serverslutpunkt* i en Azure-filsynkronisering *synkroniseringsgruppen*, måste den vara registrerad med en *Lagringssynkroniseringstjänst*. En server kan endast registreras med en tjänst för Lagringssynkronisering i taget.

#### <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
1. [Ladda ned Azure File Sync-agenten](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installationsprogrammet för Azure File Sync-agenten.
    
    ![Den första rutan i installationsprogrammet för Azure File Sync-agenten](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Glöm inte att aktivera uppdateringar av Azure File Sync-agenten med hjälp av Microsoft Update. Det är viktigt eftersom kritiska säkerhetskorrigeringar och funktionsförbättringar av server-paketet levereras via Microsoft Update.

    ![Se till att Microsoft Update är aktiverat i Microsoft Update-rutan i installationsprogrammet för Azure File Sync-agenten](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare, visas serverregistreringsguiden Användargränssnittet omedelbart när installationen är klar.

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste Azure File Sync-agenten installeras på varje nod i klustret.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av Användargränssnittet för serverregistrering
> [!Important]  
> Cloud Solution Provider (CSP)-prenumerationer kan inte använda Användargränssnittet för serverregistrering. Använd i stället PowerShell (under det här avsnittet).

1. Om serverregistreringsguiden UI inte startade omedelbart när du har slutfört installationen av Azure File Sync-agenten, den kan startas manuellt genom att köra `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Klicka på *inloggning* att få åtkomst till din Azure-prenumeration. 

    ![Öppna dialogrutan för serverregistrering UI](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj rätt prenumeration, resursgrupp osv Storage Sync-tjänsten från dialogrutan.

    ![Information om tjänsten för synkronisering](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I förhandsversion krävs en mer inloggning för att slutföra processen. 

    ![Logga in dialogrutan](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster, måste varje server kör Serverregistreringsguiden. När du visar de registrerade servrarna i Azure Portal, Azure File Sync automatiskt identifierar varje nod som en medlem i samma kluster för växling vid fel och för att gruppera dem på lämpligt sätt.

#### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra serverregistrering via PowerShell. Det här är det enda sättet att registrera servern för Cloud Solution Provider (CSP) prenumerationer:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera den med Storage Sync-tjänsten
Det finns flera steg som krävs för att avregistrera en server med en Lagringssynkroniseringstjänst. Låt oss ta en titt på hur du avregistrera en korrekt.

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, lagringsnivåer för moln eller några andra aspekter av Azure File Sync genom att avregistrera och registrera en server, eller ta bort och återskapa serverslutpunkter om inte explicit ger instruktioner till en Microsoft-tekniker. Avregistrera en server och ta bort serverslutpunkter är en destruktiv åtgärd och nivåindelade filer på volymer med serverslutpunkter kommer inte ”återanslutas” till deras platser på Azure-filresursen när den registrerade servern och serverslutpunkter återskapas, vilket resulterar synkroniserade fel. Observera också, nivåindelade filer som finns utanför en slutpunkt namnrymd kan gå förlorade permanent. Kan finnas nivåindelade filer på servern slutpunkter även om molnnivå aldrig har aktiverats.

#### <a name="optional-recall-all-tiered-data"></a>(Valfritt) Återställa alla nivåindelade data
Om du vill ha filer som för närvarande nivåer för att vara tillgängliga efter borttagning av Azure File Sync (dvs. Detta är en produktion, inte en test, miljö), återkalla alla filer på varje volym som innehåller server-slutpunkter. Inaktivera molnlagringsnivåer för alla serverslutpunkter och kör följande PowerShell-cmdlet:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Om den lokala volymen som är värd för Serverslutpunkten inte har tillräckligt med ledigt utrymme för att återställa alla nivåindelade data i `Invoke-StorageSyncFileRecall` cmdlet kommer att misslyckas.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla synkroniseringsgrupper
Innan du avregistrerar servern på Storage Sync-tjänsten måste tas bort alla serverslutpunkter på den servern. Detta kan göras via Azure portal:

1. Gå till Storage Sync-tjänsten där servern är registrerad.
2. Ta bort alla serverslutpunkter för den här servern i varje synkroniseringsgrupp i Storage Sync-tjänsten. Detta kan åstadkommas genom att högerklicka på relevant Serverslutpunkten i fönstret sync grupp.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också inträffa med ett enkelt PowerShell.skript:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzAccount
Login-AzStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Avregistrera servern
Nu när alla data har återkallats och servern har tagits bort från alla synkroniseringsgrupper, kan servern att avregistrera. 

1. I Azure-portalen går du till den *registrerade servrar* delen av Storage Sync-tjänsten.
2. Högerklicka på den server du vill avregistrera och klicka på ”avregistrera Server”.

    ![Avregistrera server](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Säkerställa Azure File Sync är en god granne i ditt datacenter 
Eftersom Azure File Sync sällan är den enda tjänsten som körs i datacentret, kanske du vill begränsa nätverks- och användningen av Azure File Sync.

> [!Important]  
> Ange begränsningar för lågt påverkar prestandan för Azure File Sync-synkronisering och återkallande.

### <a name="set-azure-file-sync-network-limits"></a>Ange begränsningar för Azure File Sync-nätverk
Du kan begränsa nätverksanvändning av Azure File Sync med hjälp av den `StorageSyncNetworkLimit` cmdletar.

> [!Note]  
> Begränsningar för nätverk visas inte när en nivåindelad fil nås eller cmdleten Invoke-StorageSyncFileRecall används.

Du kan till exempel skapa en ny begränsning för att säkerställa att Azure File Sync inte använder mer än 10 Mbit/s mellan 09: 00 och 17: 00 (17:00 h) under arbetsveckan: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Du kan se din gräns genom att använda följande cmdlet:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Ta bort begränsningar för nätverk med `Remove-StorageSyncNetworkLimit`. Följande kommando tar till exempel bort alla begränsningar för nätverk:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Använda QoS för lagring av Windows Server 
När Azure File Sync finns på en virtuell dator som körs på en Windows Server-Virtualiseringsvärd, kan du använda QoS för lagring (Tjänstkvalitet för) för att reglera lagring i/o-användning. QoS-principen kan anges som en högsta (eller gränsen, t.ex. hur StorageSyncNetwork tvingas ovan) eller som ett minimum (eller reservation). Om du minst i stället för maximalt kan Azure File Sync att utöka till använda bandbredden som tillgängligt lagringsutrymme om andra arbetsbelastningar inte använder den. Mer information finns i [lagring Quality of Service](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Se också
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Felsök Azure File Sync](storage-sync-files-troubleshoot.md)
