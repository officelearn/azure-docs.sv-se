---
title: Distribuera Synkronisering av Azure-filer | Microsoft-dokument
description: Lär dig hur du distribuerar Azure File Sync, från början till.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268072"
---
# <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync
Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver bevaras. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cachar som du behöver över hela världen.

Vi rekommenderar starkt att du läser [Planering för en Azure Files-distribution](storage-files-planning.md) och planering för en Azure File [Sync-distribution](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Krav
* En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regiontillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en filresurs](storage-how-to-create-file-share.md) för en steg-för-steg-beskrivning av hur du skapar en filresurs.
* Minst en instans av Windows Server- eller Windows Server-kluster som stöds för synkronisering med Azure File Sync. Mer information om versioner av Windows Server som stöds finns [i Interoperabilitet med Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* Az PowerShell-modulen kan användas med antingen PowerShell 5.1 eller PowerShell 6+. Du kan använda Az PowerShell-modulen för Azure File Sync på alla system som stöds, inklusive icke-Windows-system, men serverregistrerings-cmdleten måste alltid köras på den Windows Server-instans som du registrerar (detta kan göras direkt eller via PowerShell remoting). På Windows Server 2012 R2 kan du kontrollera att du kör minst PowerShell 5.1. \* genom att titta på värdet av **PSVersion-egenskapen** **för $PSVersionTable** objektet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Om psversion-värdet är mindre än 5,1. \*, som kommer att vara fallet med de flesta nya installationer av Windows Server 2012 R2, kan du enkelt uppgradera genom att ladda ner och installera [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Lämpligt paket att ladda ner och installera för Windows Server 2012 R2 är **\*\*\*\*\*\*\*Win8.1AndW2K12R2-KB -x64.msu**. 

    PowerShell 6+ kan användas med alla system som stöds och kan laddas ner via sin [GitHub-sida](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Om du planerar att använda användargränssnittet för serverregistrering i stället för att registrera dig direkt från PowerShell måste du använda PowerShell 5.1.

* Om du har valt att använda PowerShell 5.1 kontrollerar du att minst .NET 4.7.2 är installerat. Läs mer om [.NET Framework-versioner och beroenden på](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) ditt system.

    > [!Important]  
    > Om du installerar .NET 4.7.2+ på Windows Server `quiet` Core `norestart` måste du installera med flaggorna och så misslyckas installationen. Om du till exempel installerar .NET 4.8 skulle kommandot se ut så här:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Az PowerShell-modulen, som kan installeras genom att följa instruktionerna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az.StorageSync-modulen installeras nu automatiskt när du installerar Az PowerShell-modulen.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server för användning med Azure File Sync
För varje server som du tänker använda med Azure File Sync, inklusive varje servernod i ett redundanskluster, inaktiverar **du förbättrad säkerhetskonfiguration**för Internet Explorer . Detta krävs endast för första serverregistrering. Du kan aktivera det igen när servern har registrerats.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Du kan hoppa över det här steget om du distribuerar Azure File Sync på Windows Server Core.

1. Öppna Serverhanteraren.
2. Klicka på **Lokal server:**  
    ![”Lokal server” till vänster i användargränssnittet för Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  
    ![Fönstret ”Förbättrad säkerhetskonfiguration i Internet Explorer” i användargränssnittet i Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. I dialogrutan **Förbättrad säkerhetskonfiguration** i Internet Explorer väljer du **Av** för **administratörer** och **användare:**  
    ![Popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer med ”Av” valt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om du vill inaktivera förbättrad säkerhetskonfiguration i Internet Explorer kör du följande från en upphöjd PowerShell-session:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Distribuera tjänsten för synkronisering av lagring 
Distributionen av Azure File Sync börjar med att placera en **Storage Sync Service-resurs** i en resursgrupp för din valda prenumeration. Vi rekommenderar att du etablerar så få av dessa som behövs. Du skapar en förtroenderelation mellan servrarna och den här resursen och en server kan bara registreras på en Storage Sync Service. Därför rekommenderas att distribuera så många lagringssynkroniseringstjänster som du behöver för att separera grupper av servrar. Tänk på att servrar från olika lagringssynkroniseringstjänster inte kan synkroniseras med varandra.

> [!Note]
> Storage Sync-tjänsten ärver åtkomstbehörigheter från den prenumeration och resursgrupp som den har distribuerats till. Vi rekommenderar att du noggrant kontrollerar vem som har tillgång till den. Entiteter med skrivåtkomst kan börja synkronisera nya uppsättningar filer från servrar som är registrerade till den här lagringssynkroniseringstjänsten och orsaka att data flödar till Azure-lagring som är tillgänglig för dem.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill distribuera en storage sync-tjänst går du till [Azure-portalen,](https://portal.azure.com/)klickar på *Skapa en resurs* och söker sedan efter Azure File Sync. I sökresultaten väljer du **Azure File Sync**och väljer sedan **Skapa** för att öppna fliken **Distribuera lagringssynkronisering.**

I fönstret som öppnas anger du följande information:

- **Namn**: Ett unikt namn (per prenumeration) för Storage Sync Service.
- **Prenumeration**: Prenumerationen där du vill skapa tjänsten Lagringssynkronisering. Beroende på organisationens konfigurationsstrategi kan du ha åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje molntjänst (till exempel Azure Files).
- **Resursgrupp**: En resursgrupp är en logisk grupp med Azure-resurser, till exempel ett lagringskonto eller en storage sync-tjänst. Du kan skapa en ny resursgrupp eller använda en befintlig resursgrupp för Azure File Sync. (Vi rekommenderar att du använder resursgrupper som behållare för att isolera resurser logiskt för din organisation, till exempel gruppera HR-resurser eller resurser för ett visst projekt.)
- **Plats**: Den region där du vill distribuera Azure File Sync. Endast regioner som stöds är tillgängliga i den här listan.

När du är klar väljer du **Skapa** för att distribuera tjänsten Lagringssynkronisering.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Ersätt `<Az_Region>` `<RG_Name>`, `<my_storage_sync_service>` och med dina egna värden och använd sedan följande kommandon för att skapa och distribuera en storage sync-tjänst:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Du kan hämta agenten från [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När hämtningen är klar dubbelklickar du på MSI-paketet för att starta Azure File Sync-agentinstallationen.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Sync-agenten installeras på alla noder i klustret. Varje nod i klustret måste vara registrerad för att fungera med Azure File Sync.

Vi rekommenderar att du gör följande:
- Lämna standardinstallationssökvägen (C:\Program\Azure\StorageSyncAgent) för att förenkla felsökning och serverunderhåll.
- Aktivera Microsoft Update för att hålla Azure File Sync uppdaterat. Alla uppdateringar, till Azure File Sync agent, inklusive funktionsuppdateringar och snabbkorrigeringar, inträffar från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen till Azure File Sync. Mer information finns i [Azure File Sync update policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure File Sync-agenten är klar öppnas användargränssnittet för serverregistrering automatiskt. Du måste ha en lagringssynkroniseringstjänst innan du registrerar dig. se nästa avsnitt om hur du skapar en lagringssynkroniseringstjänst.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Kör följande PowerShell-kod för att hämta rätt version av Azure File Sync-agenten för ditt operativsystem och installera den på ditt system.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Sync-agenten installeras på alla noder i klustret. Varje nod i klustret måste registreras för att fungera med Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server i tjänsten för synkronisering av lagring
När du registrerar Windows Server med en tjänst för synkronisering av lagring etableras en förtroenderelation mellan din server (eller ditt kluster) och tjänsten för synkronisering av lagring. En server kan bara registreras på en tjänst för synkronisering av lagring och kan synkronisera med andra servrar och Azure-filresurser som associeras med samma tjänst för synkronisering av lagring.

> [!Note]
> Serverregistrering använder dina Azure-autentiseringsuppgifter för att skapa en förtroenderelation mellan Storage Sync Service och din Windows Server, men därefter skapar och använder servern sin egen identitet som är giltig så länge servern förblir registrerad och aktuella token för signature för delad åtkomst (Lagrings-SAS) är giltig. En ny SAS-token kan inte utfärdas till servern när servern är oregistrerad, vilket tar bort serverns möjlighet att komma åt dina Azure-filresurser, vilket stoppar all synkronisering.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Användargränssnittet för serverregistrering ska öppnas automatiskt efter installationen av Azure File Sync-agenten. Om det inte gör det kan du öppna det från dess filplats: C:\Program\Azure\StorageSyncAgent\ServerRegistration.exe. När användargränssnittet för serverregistrering öppnas väljer du **Logga in för** att börja.

När du har loggat in uppmanas du att ange följande information:

![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration:** Prenumerationen som innehåller storage sync-tjänsten (se [Distribuera tjänsten Storage Sync](#deploy-the-storage-sync-service)). 
- **Resursgrupp**: Resursgruppen som innehåller tjänsten Lagringssynkronisering.
- **Lagringssynkroniseringstjänst:** Namnet på den lagringssynkroniseringstjänst som du vill registrera dig med.

När du har valt lämplig information väljer du **Registrera för** att slutföra serverregistreringen. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en molnslutpunkt
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på en registrerad server. En server kan ha serverslutpunkter i flera synkroniseringsgrupper. Du kan skapa så många synkroniseringsgrupper som du behöver för att beskriva önskad synkroniseringstopologi på rätt sätt.

En molnslutpunkt är en pekare till en Azure-filresurs. Alla serverslutpunkter synkroniseras med en molnslutpunkt, vilket gör molnslutpunkten till hubben. Lagringskontot för Azure-filresursen måste finnas i samma region som storage sync-tjänsten. Hela Azure-filresursen synkroniseras, med ett undantag: En särskild mapp, jämförbar med den dolda mappen "Systemvolyminformation" på en NTFS-volym, kommer att etableras. Denna katalog kallas ". SystemShareInformation". Den innehåller viktiga synkroniseringsmetadata som inte synkroniseras med andra slutpunkter. Använd eller ta inte bort den!

> [!Important]  
> Du kan göra ändringar i alla molnslutpunkter eller serverslutpunkter i synkroniseringsgruppen och synkronisera dina filer till de andra slutpunkterna i synkroniseringsgruppen. Om du gör en ändring i molnslutpunkten (Azure-filresurs) direkt måste ändringar först identifieras av ett identifieringsjobb för ändring av Azure File Sync.If you make a change to the cloud endpoint (Azure file share) directly, changes first need to be discovered by an Azure File Sync change detection job. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt endast en gång var 24:e timme. Mer information finns i [Vanliga frågor och svar i Azure-filer](storage-files-faq.md#afs-change-detection).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa en synkroniseringsgrupp går du till din Storage Sync-tjänst i [Azure-portalen](https://portal.azure.com/)och väljer sedan **+ Synkroniseringsgrupp:**

![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

- **Namn på synkroniseringsgrupp**: Namnet på den synkroniseringsgrupp som ska skapas. Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig.
- **Prenumeration:** Prenumerationen där du distribuerade tjänsten Storage Sync i [Distribuera tjänsten Storage Sync](#deploy-the-storage-sync-service).
- **Lagringskonto**: Om du väljer **Välj lagringskonto**visas en annan ruta där du kan välja det lagringskonto som har den Azure-filresurs som du vill synkronisera med.
- **Azure-filresurs**: Namnet på den Azure-filresurs som du vill synkronisera med.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om du vill skapa synkroniseringsgruppen kör du följande PowerShell. Kom ihåg `<my-sync-group>` att ersätta med det önskade namnet på synkroniseringsgruppen.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

När synkroniseringsgruppen har skapats kan du skapa din molnslutpunkt. Var noga `<my-storage-account>` med `<my-file-share>` att ersätta och med de förväntade värdena.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Skapa en serverslutpunkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. En serverslutpunkt måste vara en sökväg på en registrerad server (i stället för en monterad resurs) och för att använda molnnivådelning måste sökvägen vara på en icke-systemvolym. Nätverksansluten lagring (NAS) stöds inte.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill lägga till en serverslutpunkt går du till den nyskapade synkroniseringsgruppen och väljer sedan **Lägg till serverslutpunkt**.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

- **Registrerad server:** Namnet på den server eller det kluster där du vill skapa serverslutpunkten.
- **Sökväg**: Sökvägen till Windows Server som ska synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivådelning**: En växel för att aktivera eller inaktivera molnnivåhantering. Med molnnivådelning kan sällan använda eller använda filer delas upp i Azure-filer.
- **Ledigt utrymme för volym:** Mängden ledigt utrymme för att reservera på den volym som serverslutpunkten finns på. Om volymfritt utrymme till exempel är inställt på 50 % på en volym som har en enda serverslutpunkt, nivåindelad ungefär hälften av mängden data till Azure-filer. Oavsett om molnnivådelning är aktiverat har din Azure-filresurs alltid en fullständig kopia av data i synkroniseringsgruppen.

Om du vill lägga till serverslutpunkten väljer du **Skapa**. Dina filer synkroniseras nu mellan din Azure-filresurs och Windows Server. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Kör följande PowerShell-kommandon för att skapa serverslutpunkten `<your-server-endpoint-path>` och `<your-volume-free-space>` se till att ersätta och med önskade värden.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurera inställningar för brandvägg och virtuellt nätverk

### <a name="portal"></a>Portalen
Om du vill konfigurera synkroniseringen av din Azure File så att den fungerar med brandväggs- och virtuella nätverksinställningar gör du följande:

1. Från Azure-portalen navigerar du till det lagringskonto som du vill skydda.
1. Välj knappen **Brandväggar och virtuella nätverk** på den vänstra menyn.
1. Välj **Markerade nätverk** under Tillåt åtkomst **från**.
1. Kontrollera att servrarnas IP eller virtuella nätverk visas under lämpligt avsnitt.
1. Kontrollera att **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagringskontot.**
1. Välj **Spara** om du vill spara inställningarna.

![Konfigurera brandväggs- och inställningar för virtuella nätverk så att de fungerar med Azure File-synkronisering](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Introduktion med Azure File Sync
De rekommenderade stegen till introduktion på Azure File Sync för den första med noll driftstopp samtidigt som fullständig filåtergivning och åtkomstkontrollista (ACL) bevaras är följande:
 
1. Distribuera en lagringssynkroniseringstjänst.
2. Skapa en synkroniseringsgrupp.
3. Installera Azure File Sync-agent på servern med hela datauppsättningen.
4. Registrera servern och skapa en serverslutpunkt på resursen. 
5. Låt synkronisering göra fullständig överföring till Azure-filresursen (molnslutpunkt).  
6. När den första överföringen är klar installerar du Azure File Sync-agenten på var och en av de återstående servrarna.
7. Skapa nya filresurser på var och en av de återstående servrarna.
8. Skapa serverslutpunkter på nya filresurser med principen för molnnivådelning, om så önskas. (Det här steget kräver ytterligare lagringsutrymme för att vara tillgängligt för den första installationen.)
9. Låt Azure File Sync-agenten göra en snabb återställning av det fullständiga namnområdet utan den faktiska dataöverföringen. När den fullständiga namnområdessynkroniseringen har fyllts det lokala diskutrymmet baserat på molnnivåprincipen för serverslutpunkten. 
10. Se till att synkroniseringen är klar och testa din topologi som önskas. 
11. Omdirigera användare och program till den nya resursen.
12. Du kan också ta bort eventuella dubblettresurser på servrarna.
 
Om du inte har extra lagringsutrymme för inledande introduktion och vill koppla till befintliga resurser kan du förseedade data i Azure-filer som delars. Den här metoden föreslås, om och endast om du kan acceptera driftstopp och absolut garantera inga dataändringar på serverresurserna under den första introduktionsprocessen. 
 
1. Kontrollera att data på någon av servrarna inte kan ändras under introduktionsprocessen.
2. Azure-fil som förutsäde till exempel med serverdata med hjälp av dataöverföringsverktyg via SMB, Robocopy, direkt SMB-kopia. Eftersom AzCopy inte ladda upp data över SMB så det kan inte användas för pre-seedning.
3. Skapa Azure File Sync-topologi med önskade serverslutpunkter som pekar på befintliga resurser.
4. Låt synkronisering avsluta avstämningsprocessen på alla slutpunkter. 
5. När avstämningen är klar kan du öppna resurser för ändringar.
 
För närvarande har försådd metod några begränsningar - 
- Fullständig återgivning av filer bevaras inte. Filer förlorar till exempel ACL:er och tidsstämplar.
- Dataändringar på servern innan synkroniseringstopologin är igång helt och hållet kan orsaka konflikter på serverns slutpunkter.  
- När molnslutpunkten har skapats kör Azure File Sync en process för att identifiera filerna i molnet innan den första synkroniseringen startas. Den tid det tar att slutföra den här processen varierar beroende på olika faktorer som nätverkshastighet, tillgänglig bandbredd och antal filer och mappar. För grov uppskattning i förhandsversionen körs identifieringsprocessen ungefär vid 10 filer/sek.  Därför, även om försåddkörning går snabbt, kan den totala tiden för att få ett helt fungerande system vara betydligt längre när data är försådd i molnet.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Självbetjäningsåterställning via tidigare versioner och VSS (Volume Shadow Copy Service)

> [!IMPORTANT]
> Följande information kan endast användas med version 9 (eller högre) av lagringssynkroniseringsagenten. Versioner som är lägre än 9 har inte cmdlets för StorageSyncSelfService.

Tidigare versioner är en Windows-funktion som låter dig använda vss-ögonblicksbilder på serversidan av en volym för att presentera återställningsbara versioner av en fil till en SMB-klient.
Detta möjliggör ett kraftfullt scenario, vanligen kallad självbetjäningsåterställning, direkt för informationsarbetare i stället för beroende på återställningen från en IT-administratör.

VSS-ögonblicksbilder och tidigare versioner fungerar oberoende av Azure File Sync. Molnnivådelning måste dock ställas in på ett kompatibelt läge. Många Azure File Sync-serverslutpunkter kan finnas på samma volym. Du måste ringa följande PowerShell-anrop per volym som till och med har en serverslutpunkt där du planerar att eller använder molnnivåhantering.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-ögonblicksbilder tas av en hel volym. Som standard kan upp till 64 ögonblicksbilder finnas för en viss volym, givet att det finns tillräckligt med utrymme för att lagra ögonblicksbilderna. VSS hanterar detta automatiskt. Standardschemat för ögonblicksbilder tar två ögonblicksbilder per dag, måndag till fredag. Det schemat kan konfigureras via en schemalagd aktivitet i Windows. Ovanstående PowerShell cmdlet gör två saker:
1. Den konfigurerar Azure File Syncs cloud tiering på den angivna volymen för att vara kompatibel med tidigare versioner och garanterar att en fil kan återställas från en tidigare version, även om den var nivåindelning till molnet på servern. 
2. Det aktiverar standard-VSS-schemat. Du kan sedan bestämma dig för att ändra den senare. 

> [!Note]  
> Det finns två viktiga saker att notera:
>- Om du använder parametern -Force och VSS är aktiverat, skrivs det över det aktuella VSS-ögonblicksbildschemat och ersätter det med standardschemat. Se till att du sparar din anpassade konfiguration innan du kör cmdlet.
> - Om du använder den här cmdleten på en klusternod måste du också köra den på alla andra noder i klustret! 

För att se om självbetjäningsåterställningskompatibilitet är aktiverad kan du köra följande cmdlet.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Det kommer att lista alla volymer på servern samt antalet molnnivåhantering kompatibla dagar för varje. Det här numret beräknas automatiskt baserat på högsta möjliga ögonblicksbilder per volym och standardschemat för ögonblicksbilder. Så som standard kan alla tidigare versioner som presenteras för en informationsarbetare användas för att återställa från. Detsamma gäller om du ändrar standardschemat för att ta fler ögonblicksbilder.
Men om du ändrar schemat på ett sätt som resulterar i en tillgänglig ögonblicksbild på volymen som är äldre än värdet för kompatibla dagar, kan användarna inte använda den här äldre ögonblicksbilden (tidigare version) för att återställa från.

> [!Note]
> Aktivera självbetjäningsåterställning kan påverka din Azure-lagringsförbrukning och faktura. Den här effekten är begränsad till filer som för närvarande är nivåindelade på servern. Om du aktiverar den här funktionen säkerställer du att det finns en filversion tillgänglig i molnet som kan refereras via en tidigare version (VSS snapshot) post.
>
> Om du inaktiverar funktionen minskar Azure-lagringsförbrukningen långsamt tills fönstret kompatibla dagar har passerats. Det finns inget sätt att påskynda detta. 

Standardt maximum antal VSS-ögonblicksbilder per volym (64) samt standardschemat för att ta dem, resulterar i högst 45 dagar av tidigare versioner som en informationsarbetare kan återställa från, beroende på hur många VSS-ögonblicksbilder du kan lagra på volymen.

Om max. 64 VSS ögonblicksbilder per volym är inte rätt inställning för dig, kan du [ändra det värdet via en registernyckel](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
För att den nya gränsen ska börja gälla måste du köra cmdleten igen för att aktivera föregående versionskompatibilitet på varje volym som den tidigare var aktiverad, med flaggan -Force för att ta hänsyn till det nya maximala antalet VSS-ögonblicksbilder per volym. Detta resulterar i ett nyligen beräknat antal kompatibla dagar. Observera att den här ändringen endast börjar gälla på nyligen nivåindelada filer och skriva över eventuella anpassningar på VSS-schemat som du kan ha gjort.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en DFS-replikeringsdistribution (DFS-R) till Azure File Sync
Så här migrerar du en DFS-R-distribution till Azure File Sync:

1. Skapa en synkroniseringsgrupp som representerar den DFS-R-topologi som du ersätter.
2. Börja på servern som har den fullständiga uppsättningen data i DFS-R-topologin för att migrera. Installera Azure File Sync på den servern.
3. Registrera servern och skapa en serverslutpunkt för den första servern som ska migreras. Aktivera inte molnnivåhantering.
4. Låt all data synkroniseras med din Azure-filresurs (molnslutpunkt).
5. Installera och registrera Azure File Sync-agenten på var och en av de återstående DFS-R-servrarna.
6. Inaktivera DFS-R. 
7. Skapa en serverslutpunkt på var och en av DFS-R-servrarna. Aktivera inte molnnivåhantering.
8. Se till att synkroniseringen är klar och testa din topologi som önskas.
9. Pensionera DFS-R.
10. Molnnivådelning kan nu aktiveras på valfri serverslutpunkt som önskat.

Mer information finns i [Interop för Azure File Sync med Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort en slutpunkt för Azure File Sync Server](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
