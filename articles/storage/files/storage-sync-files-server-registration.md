---
title: Hantera registrerade servrar med Azure File Sync | Microsoft-dokument
description: Lär dig hur du registrerar och avregistrerar en Windows-server med en Azure File Sync Storage Sync Service.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255098"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Hantera registrerade servrar med Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta gör detta genom att omvandla dina Windows-servrar till en snabb cache av din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Följande artikel visar hur du registrerar och hanterar en server med en storage sync-tjänst. Se [Så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) för information om hur du distribuerar Azure File Sync från på nytt.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrera/avregistrera en server med Storage Sync Service
Om du registrerar en server med Azure File Sync upprättas en förtroenderelation mellan Windows Server och Azure. Den här relationen kan sedan användas för att skapa *serverslutpunkter* på servern, som representerar specifika mappar som ska synkroniseras med en Azure-filresurs (kallas även *för en molnslutpunkt*). 

### <a name="prerequisites"></a>Krav
Om du vill registrera en server med en storage sync-tjänst måste du först förbereda servern med de nödvändiga förutsättningarna:

* Servern måste köra en version av Windows Server som stöds. Mer information finns i [Systemkrav för Azure File Sync och interoperabilitet](storage-sync-files-planning.md#windows-file-server-considerations).
* Kontrollera att en lagringssynkroniseringstjänst har distribuerats. Mer information om hur du distribuerar en lagringssynkroniseringstjänst finns i [Så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).
* Kontrollera att servern är ansluten till internet och att Azure är tillgängligt.
* Inaktivera IE Enhanced Security Configuration för administratörer med serverhanterarens användargränssnitt.
    
    ![Serverhanterarens användargränssnitt med IE-förbättrad säkerhetskonfiguration markerad](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Kontrollera att Azure PowerShell-modulen är installerad på servern. Om servern är medlem i ett redundanskluster kräver varje nod i klustret Az-modulen. Mer information om hur du installerar Az-modulen finns på [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av Az PowerShell-modulen för att registrera/avregistrera en server. Om Az-paketet tidigare har installerats på den här servern (och PowerShell-versionen på den `Update-Module` här servern är 5.* eller mer) kan du använda cmdlet för att uppdatera paketet. 
* Om du använder en nätverksproxyserver i din miljö konfigurerar du proxyinställningar på servern som synkroniseringsagenten kan använda.
    1. Bestäm din proxy-IP-adress och portnummer
    2. Redigera dessa två filer:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Lägg till raderna i figur 1 (under det här avsnittet) under /System.ServiceModel i ovanstående två filer som ändrar 127.0.0.1:8888 till rätt IP-adress (ersätt 127.0.0.1) och korrekt portnummer (ersätt 8888):
    4. Ange proxyinställningarna för WinHTTP via kommandoraden:
        * Visa proxy: netsh winhttp visa proxy
        * Ange proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Återställ proxyn: netsh winhttp reset proxy
        * Om detta är setup efter agenten har installerats, sedan starta om vår synkronisering agent: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med tjänsten Storage Sync
Innan en server kan användas som *en serverslutpunkt* i en Azure File *Sync-synkroniseringsgrupp*måste den registreras med en *Storage Sync Service*. En server kan bara registreras med en Storage Sync Service åt gången.

#### <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
1. [Ladda ned Azure File Sync-agenten](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installationsprogrammet för Azure File Sync-agenten.
    
    ![Det första fönstret i installationsprogrammet för Azure File Sync-agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Var noga med att aktivera uppdateringar till Azure File Sync agent med Microsoft Update. Det är viktigt eftersom viktiga säkerhetskorrigeringar och funktionsförbättringar i serverpaketet levereras via Microsoft Update.

    ![Kontrollera att Microsoft Update är aktiverat i fönstret Microsoft Update i installationsprogrammet för Azure File Sync-agent](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare visas användargränssnittet för serverregistrering omedelbart efter att installationen har slutförts.

> [!Important]  
> Om servern är medlem i ett redundanskluster måste Azure File Sync-agenten installeras på varje nod i klustret.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av användargränssnittet för serverregistrering
> [!Important]  
> CSP-prenumerationer (Cloud Solution Provider) kan inte använda användargränssnittet för serverregistrering. Använd i stället PowerShell (under det här avsnittet).

1. Om användargränssnittet för serverregistrering inte startade direkt efter att installationen av Azure File Sync-agenten har slutförts kan det startas manuellt genom att `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`köra .
2. Klicka på Logga in för att komma åt din *Azure-prenumeration.* 

    ![Öppna dialogrutan för användargränssnittet för serverregistrering](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj rätt prenumerations-, resursgrupps- och lagringssynkroniseringstjänst i dialogrutan.

    ![Information om tjänsten Lagringssynkronisering](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I förhandsgranskningen krävs ytterligare en inloggning för att slutföra processen. 

    ![Dialogrutan Logga in](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster måste varje server köra serverregistreringen. När du visar de registrerade servrarna i Azure Portal identifierar Azure File Sync automatiskt varje nod som medlem i samma Redundanskluster och grupperar dem på lämpligt sätt.

#### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra serverregistrering via PowerShell. Detta är det enda sättet att registrera servern för CSP-prenumerationer (Cloud Solution Provider):

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera servern med Storage Sync Service
Det finns flera steg som krävs för att avregistrera en server med en storage sync-tjänst. Låt oss ta en titt på hur du korrekt avregistrera en server.

> [!Warning]  
> Försök inte felsöka problem med synkronisering, molnnivådelning eller någon annan aspekt av Azure File Sync genom att avregistrera och registrera en server, eller ta bort och återskapa serverslutpunkterna om inte uttryckligen instrueras av en Microsoft-tekniker. Att avregistrera en server och ta bort serverslutpunkter är en destruktiv åtgärd, och nivåindelada filer på volymerna med serverslutpunkter kommer inte att "återanslutas" till sina platser på Azure-filresursen efter att de registrerade serverslutpunkterna är återskapas, vilket resulterar i synkroniseringsfel. Observera också att nivåindelada filer som finns utanför en serverslutpunktsnamnområde kan gå förlorade permanent. Nivåindelad filer kan finnas i serverslutpunkter även om molnnivådelning aldrig har aktiverats.

#### <a name="optional-recall-all-tiered-data"></a>(Valfritt) Återkalla alla nivåindelad data
Om du vill att filer som för närvarande är nivåindelade ska vara tillgängliga när du har tagit bort Azure File Sync (dvs. detta är en produktion, inte ett test, miljö), återkalla alla filer på varje volym som innehåller serverslutpunkter. Inaktivera molnnivåindelning för alla serverslutpunkter och kör sedan följande PowerShell-cmdlet:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Om den lokala volymen som är värd för serverslutpunkten inte har `Invoke-StorageSyncFileRecall` tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data, misslyckas cmdleten.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla synkroniseringsgrupper
Innan servern avregistreras på storage sync-tjänsten måste alla serverslutpunkter på den servern tas bort. Detta kan göras via Azure-portalen:

1. Navigera till tjänsten Lagringssynkronisering där servern är registrerad.
2. Ta bort alla serverslutpunkter för den här servern i varje synkroniseringsgrupp i tjänsten Lagringssynkronisering. Detta kan åstadkommas genom att högerklicka på relevant serverslutpunkt i synkroniseringsgruppfönstret.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också åstadkommas med ett enkelt PowerShell-skript:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Avregistrera servern
Nu när alla data har återkallats och servern har tagits bort från alla synkroniseringsgrupper kan servern avregistreras. 

1. I Azure-portalen navigerar du till avsnittet *Registrerade servrar* i tjänsten Lagringssynkronisering.
2. Högerklicka på den server du vill avregistrera och klicka på "Avregistrera servern".

    ![Avregistrera servern](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Se till att Azure File Sync är en bra granne i ditt datacenter 
Eftersom Azure File Sync sällan är den enda tjänsten som körs i ditt datacenter, kanske du vill begränsa nätverks- och lagringsanvändningen av Azure File Sync.

> [!Important]  
> Om du anger gränser för lågt påverkar prestanda för synkronisering och återkallande av Azure File Sync.

### <a name="set-azure-file-sync-network-limits"></a>Ange nätverksgränser för Azure File Sync
Du kan begränsa nätverksanvändningen av Azure `StorageSyncNetworkLimit` File Sync med hjälp av cmdlets.

> [!Note]  
> Nätverksgränser gäller inte när en nivåindelad fil används eller så används cmdletEn Invoke-StorageSyncFileRecall.

Du kan till exempel skapa en ny begränsningsgräns för att säkerställa att Azure File Sync inte använder mer än 10 Mbit/s mellan 09.00 och 17.000 under arbetsveckan: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Du kan se din gräns med hjälp av följande cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Om du vill `Remove-StorageSyncNetworkLimit`ta bort nätverksgränser använder du . Följande kommando tar till exempel bort alla nätverksgränser:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Använda QoS för Windows Server-lagring 
När Azure File Sync finns på en virtuell dator som körs på en Windows Server-virtualiseringsvärd kan du använda Storage QoS (lagringskvalitet för tjänsten) för att reglera lagrings-IO-förbrukning. Storage QoS-principen kan ställas in antingen som ett maximum (eller gräns, till exempel hur StorageSyncNetwork-gränsen tillämpas ovan) eller som ett minimum (eller reservation). Om du anger ett minimum i stället för ett maximum kan Azure File Sync burst för att använda tillgänglig lagringsbandbredd om andra arbetsbelastningar inte använder den. Mer information finns i [Lagringskvalitet på tjänsten](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
