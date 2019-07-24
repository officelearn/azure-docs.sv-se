---
title: Distribuera Azure File Sync | Microsoft Docs
description: Lär dig hur du distribuerar Azure File Sync från början till slut.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9c05f3cf9a4c6fc916f1c9578de7aee6d0190ee5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327141"
---
# <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync
Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Azure File Sync transformerar Windows Server till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser [planering för en Azure Files distribution](storage-files-planning.md) och [planering för en Azure File Sync distribution](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regions tillgänglighet](storage-sync-files-planning.md#region-availability) för Azure File Sync.
    - [Skapa en fil resurs](storage-how-to-create-file-share.md) för en steg-för-steg-beskrivning av hur du skapar en fil resurs.
* Minst en instans av Windows Server eller Windows Server-klustret som stöds för synkronisering med Azure File Sync. Mer information om vilka versioner av Windows Server som stöds finns i [samverkan med Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* PowerShell-modulen AZ kan användas med antingen PowerShell 5,1 eller PowerShell 6 +. Du kan använda AZ PowerShell-modulen för Azure File Sync på alla system som stöds, inklusive icke-Windows-system, men Server registrerings cmdleten måste alltid köras på den Windows Server-instans som du registrerar (detta kan göras direkt eller via PowerShell fjärr kommunikation). På Windows Server 2012 R2 kan du kontrol lera att du kör minst PowerShell 5,1. genom att titta på värdet för egenskapen **PSVersion** för $PSVersionTable-objektet:  \*

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Om ditt PSVersion-värde är mindre än 5,1. , precis som är fallet med de flesta nya installationer av Windows Server 2012 R2, kan du enkelt uppgradera genom att ladda ned och installera [Windows Management Framework (WMF) 5,1.](https://www.microsoft.com/download/details.aspx?id=54616) \* Lämpligt paket för att ladda ned och installera för Windows Server 2012 R2 **är Win 8.1 andw2k12r2-\*KB\*\*\*\*\*\*-x64. msu**. 

    PowerShell 6 + kan användas med alla system som stöds och kan hämtas via dess GitHub- [sida](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Om du planerar att använda användar gränssnittet för Server registrering i stället för att registrera direkt från PowerShell måste du använda PowerShell 5,1.

* Om du har valt att använda PowerShell 5,1 kontrollerar du att minst .NET 4.7.2 är installerat. Läs mer om [.NET Framework versioner och beroenden](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) i systemet.

    > [!Important]  
    > Om du installerar .NET 4.7.2 + på Windows Server Core måste du installera med `quiet` -och `norestart` -flaggorna eller så Miss söker installationen. Om du till exempel installerar .NET 4,8 ser kommandot ut så här:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* AZ PowerShell-modulen som kan installeras genom att följa anvisningarna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Modulen AZ. StorageSync installeras nu automatiskt när du installerar AZ PowerShell-modulen.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server för användning med Azure File Sync
Inaktivera **förbättrad säkerhets konfiguration i Internet Explorer**för varje server som du vill använda med Azure File Sync, inklusive varje servernod i ett kluster för växling vid fel. Detta krävs endast för inledande Server registrering. Du kan aktivera det igen när servern har registrerats.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Du kan hoppa över det här steget om du distribuerar Azure File Sync på Windows Server Core.

1. Öppna Serverhanteraren.
2. Klicka på **lokal server**:  
    !["Lokal server" på vänster sida av Serverhanteraren gränssnittet](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  
    ![Fönstret förbättrad säkerhets konfiguration i Internet Explorer i Serverhanteraren användar gränssnitt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. I dialog rutan **förbättrad säkerhets konfiguration i Internet Explorer** väljer du **av** för **Administratörer** och **användare**:  
    ![Popup-fönstret för förbättrad säkerhets konfiguration i Internet Explorer med alternativet "av" valt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill inaktivera Förbättrad säkerhets konfiguration i Internet Explorer kör du följande från en upphöjd PowerShell-session:

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
Distributionen av Azure File Sync börjar med att placera en resurs för **lagrings-synkroniseringstjänsten** i en resurs grupp för den valda prenumerationen. Vi rekommenderar etablering så att det är så viktigt som det behövs. Du kommer att skapa en förtroende relation mellan dina servrar och den här resursen och en server kan bara registreras till en lagrings tjänst för synkronisering. Därför rekommenderar vi att du distribuerar så många tjänster för synkronisering av lagring som du behöver för att skilja grupper av servrar. Tänk på att servrar från olika lagrings Sync-tjänster inte kan synkroniseras med varandra.

> [!Note]
> Tjänsten Storage Sync har ärvt åtkomst behörigheter från prenumerationen och resurs gruppen som den har distribuerats till. Vi rekommenderar att du noga kontrollerar vem som har åtkomst till den. Entiteter med skriv åtkomst kan börja synkronisera nya uppsättningar av filer från servrar som är registrerade i den här tjänsten för synkronisering av lagring och leda till att data flödar till Azure Storage som är tillgängligt för dem.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Om du vill distribuera en tjänst för synkronisering av lagring går du till [Azure Portal](https://portal.azure.com/), klickar på *skapa en resurs* och söker sedan efter Azure File Sync. I Sök resultaten väljer du **Azure File Sync**och väljer sedan **skapa** för att öppna fliken **distribuera synkronisering av lagring** .

I fönstret som öppnas anger du följande information:

- **Namn på**: Ett unikt namn (per prenumeration) för tjänsten för synkronisering av lagring.
- **Prenumeration**: Den prenumeration där du vill skapa tjänsten för synkronisering av lagring. Beroende på din organisations konfigurations strategi kan du ha åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje moln tjänst (till exempel Azure Files).
- **Resursgrupp**: En resurs grupp är en logisk grupp av Azure-resurser, till exempel ett lagrings konto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resurs grupp eller använda en befintlig resurs grupp för Azure File Sync. (Vi rekommenderar att du använder resurs grupper som behållare för att isolera resurser logiskt för din organisation, till exempel för att gruppera HR-resurser eller resurser för ett bestämt projekt.)
- **Plats**: Den region där du vill distribuera Azure File Sync. Endast regioner som stöds är tillgängliga i den här listan.

När du är färdig väljer du **skapa** för att distribuera tjänsten för synkronisering av lagring.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Ersätt **< Az_Region >** , **< RG_Name >** och **< my_storage_sync_service >** med dina egna värden och Använd sedan följande cmds för att skapa och distribuera en tjänst för synkronisering av lagring:

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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Du kan ladda ned agenten från [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När hämtningen är slutförd, dubbelklickar du på MSI-paketet för att starta installationen av Azure File Sync-agenten.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Syncs agenten installeras på varje nod i klustret. Varje nod i klustret måste ha registrerats för att fungera med Azure File Sync.

Vi rekommenderar att du gör följande:
- Lämna standard installations Sök vägen (C:\Program Files\Azure\StorageSyncAgent) för att förenkla fel sökning och Server underhåll.
- Aktivera Microsoft Update för att hålla Azure File Sync uppdaterat. Alla uppdateringar till Azure File Sync agent, inklusive funktions uppdateringar och snabb korrigeringar, sker från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen för Azure File Sync. Mer information finns i [Azure File Sync Update policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure File Sync agent har slutförts öppnas användar gränssnittet för Server registrering automatiskt. Du måste ha en tjänst för synkronisering av lagring före registreringen. Se nästa avsnitt om hur du skapar en tjänst för synkronisering av lagring.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kod för att ladda ned rätt version av Azure File Sync agent för operativ systemet och installera den på systemet.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Syncs agenten installeras på varje nod i klustret. Varje nod i klustret måste ha registrerats för att fungera med Azure File Sync.

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
> Server registreringen använder dina Azure-autentiseringsuppgifter för att skapa en förtroende relation mellan tjänsten för synkronisering av lagring och Windows Server, men därefter skapar servern och använder sin egen identitet som är giltig så länge som servern förblir registrerad och aktuell token för signatur för delad åtkomst (lagrings-SAS) är giltig. Det går inte att utfärda en ny SAS-token till servern när servern har avregistrerats, och därför kan du ta bort serverns möjlighet att komma åt dina Azure-filresurser och stoppa eventuell synkronisering.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Server registrerings gränssnittet bör öppnas automatiskt när du har installerat Azure File Sync agenten. Om det inte öppna kan du öppna det manuellt från dess filplats: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. När Server registrerings gränssnittet öppnas väljer du **Logga** in för att börja.

När du har loggat in uppmanas du att ange följande information:

![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: Den prenumeration som innehåller synkroniseringstjänsten för lagring (se [distribuera tjänsten för synkronisering av lagring](#deploy-the-storage-sync-service)). 
- **Resursgrupp**: Den resursgrupp som innehåller tjänsten för synkronisering av lagring.
- **Tjänsten för synkronisering av lagring**: Namnet på den tjänst för synkronisering av lagring som du vill registrera.

När du har valt lämplig information väljer du **Registrera** för att slutföra Server registreringen. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en molnslutpunkt
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En server slut punkt representerar en sökväg på den registrerade servern. En server kan ha Server slut punkter i flera Sync-grupper. Du kan skapa så många Sync-grupper som du behöver för att beskriva önskad frågetopologi.

En moln slut punkt är en pekare till en Azure-filresurs. Alla Server slut punkter synkroniseras med en moln slut punkt, vilket gör moln slut punkten till hubben. Lagrings kontot för Azure-filresursen måste finnas i samma region som tjänsten för synkronisering av lagring. Hela Azure-filresursen kommer att synkroniseras, med ett undantag: En särskild mapp, som är jämförbar med den dolda mappen "System Volume Information" på en NTFS-volym, kommer att tillhandahållas. Den här katalogen kallas ". SystemShareInformation". Den innehåller viktiga metadata för synkronisering som inte kommer att synkroniseras med andra slut punkter. Använd inte eller ta inte bort!

> [!Important]  
> Du kan göra ändringar i alla moln slut punkter eller Server slut punkter i synkroniseringsresursen och låta filerna vara synkroniserade med de andra slut punkterna i den synkroniserade gruppen. Om du gör en ändring i moln slut punkten (Azure-filresursen) direkt måste ändringarna först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras bara för en moln slut punkt var 24: e timme. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa en Sync-grupp går du till tjänsten lagrings-synkronisering i [Azure Portal](https://portal.azure.com/)och väljer sedan **+ Sync-grupp**:

![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

- **Sync-grupp namn**: Namnet på den Sync-grupp som ska skapas. Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig.
- **Prenumeration**: Den prenumeration där du distribuerade synkroniseringstjänsten för lagring i [distribuera tjänsten för synkronisering av lagring](#deploy-the-storage-sync-service).
- **Lagringskonto**: Om du väljer **Välj lagrings konto**visas en annan ruta där du kan välja det lagrings konto som har den Azure-filresurs som du vill synkronisera med.
- **Azure-fil resurs**: Namnet på den Azure-filresurs som du vill synkronisera med.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell om du vill skapa en Sync-grupp. Kom ihåg att `<my-sync-group>` ersätta med det önskade namnet på Sync-gruppen.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

När du har skapat Sync-gruppen kan du skapa din moln slut punkt. Se till att ersätta `<my-storage-account>` och `<my-file-share>` med de förväntade värdena.

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
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. En server slut punkt måste vara en sökväg på en registrerad Server (i stället för en monterad resurs) och för att använda moln nivåer måste sökvägen vara på en volym som inte är en system volym. NAS (Network Attached Storage) stöds inte.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Om du vill lägga till en server slut punkt går du till den nya Sync-gruppen och väljer sedan **Lägg till Server slut punkt**.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

- **Registrerad Server**: Namnet på den server eller det kluster där du vill skapa server slut punkten.
- **Sökväg**: Sökvägen till Windows Server som ska synkroniseras som en del av Sync-gruppen.
- **Moln nivå**: En växel för att aktivera eller inaktivera moln nivåer. Med moln nivåer kan använda filer som används sällan eller som används i nivå till Azure Files.
- **Ledigt utrymme på volym**: Mängden ledigt utrymme som ska reserveras på den volym där Server slut punkten finns. Om till exempel volym ledigt utrymme är inställt på 50% på en volym som har en enda server slut punkt, är ungefär hälften av data i nivå till Azure Files. Oavsett om moln nivån är aktive rad, har Azure-filresursen alltid en fullständig kopia av data i Sync-gruppen.

Välj **skapa**om du vill lägga till Server slut punkten. Filerna hålls nu synkroniserade över Azure-filresursen och Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kommandon för att skapa server slut punkten och se till att ersätta `<your-server-endpoint-path>` och `<your-volume-free-space>` med önskade värden.

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

## <a name="configure-firewall-and-vnet-settings"></a>Konfigurera inställningar för brand vägg och VNet

### <a name="portal"></a>Portalen
Gör så här om du vill konfigurera Azure File Sync så att den fungerar med brand vägg och inställningar för virtuellt nätverk:

1. Från Azure Portal navigerar du till det lagrings konto som du vill skydda.
1. Välj knappen **brand väggar och virtuella nätverk** på lefthand-menyn.
1. Välj **valda nätverk** under **Tillåt åtkomst från**.
1. Kontrol lera att dina servrars IP-adresser eller virtuella nätverk visas under lämpligt avsnitt.
1. Kontrol lera att **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** är markerat.
1. Spara inställningarna genom att välja **Spara** .


## <a name="onboarding-with-azure-file-sync"></a>Onboarding med Azure File Sync
De rekommenderade stegen för att integrera Azure File Sync för det första med noll stillestånd samtidigt som du behåller fullständig fil åter givning och åtkomst kontrol lista (ACL) är följande:
 
1. Distribuera en tjänst för synkronisering av lagring.
2. Skapa en synkroniseringsgrupp.
3. Installera Azure File Sync agent på servern med fullständig data uppsättning.
4. Registrera servern och skapa en server slut punkt på resursen. 
5. Låt synkronisering utföra fullständig uppladdning till Azure-filresursen (moln slut punkt).  
6. När den första överföringen är klar installerar du Azure File Sync agent på var och en av de återstående servrarna.
7. Skapa nya fil resurser på var och en av de återstående servrarna.
8. Skapa server slut punkter på nya fil resurser med princip för moln skikt, om det behövs. (Det här steget kräver att ytterligare lagrings utrymme är tillgängligt för den första installationen.)
9. Låt Azure File Sync agent göra en snabb återställning av hela namn området utan den faktiska data överföringen. När den fullständiga namn rymden synkroniseras fyller Synkroniseringsmotorn det lokala disk utrymmet baserat på moln nivå principen för Server slut punkten. 
10. Se till att synkroniseringen är klar och testa din topologi som önskad. 
11. Omdirigera användare och program till den här nya resursen.
12. Du kan också ta bort alla duplicerade resurser på servrarna.
 
Om du inte har extra lagrings utrymme för inledande onboarding och vill ansluta till de befintliga resurserna kan du i förväg dirigera data i Azure Files-resurserna. Den här metoden föreslås, om du och bara om du kan godkänna stillestånds tid och absolut garanti på att inga data ändras på Server resurserna under den inledande onboarding-processen. 
 
1. Se till att data på någon av servrarna inte kan ändras under onboarding-processen.
2. I förväg dirigerar du Azure-filresurser med Server data med hjälp av alla data överförings verktyg via SMB till exempel Robocopy, Direct SMB Copy. Eftersom AzCopy inte överför data via SMB så det kan inte användas för för dirigering.
3. Skapa Azure File Sync topologi med önskade Server slut punkter som pekar på de befintliga resurserna.
4. Låt avstämnings processen för synkronisering slutföras på alla slut punkter. 
5. När avstämningen är klar kan du öppna resurser för ändringar.
 
För närvarande har för-seeding-metoden några begränsningar – 
- Fullständig åter givning av filer bevaras inte. Filerna förlorar till exempel ACL: er och tidsstämplar.
- Data ändringar på servern innan topologin för synkronisering är helt igång kan orsaka konflikter på serverns slut punkter.  
- När moln slut punkten har skapats kör Azure File Sync en process för att identifiera filerna i molnet innan du startar den första synkroniseringen. Den tid det tar att slutföra den här processen varierar beroende på de olika faktorer som nätverks hastighet, tillgänglig bandbredd och antal filer och mappar. För en grov uppskattning i för hands versionen körs identifierings processen ungefär vid 10 filer/SEK.  Även om för indirigering körs snabbt, kan den totala tiden för att få ett fullständigt operativ system vara betydligt längre när data försätts i molnet.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en DFS Replication-distribution (DFS-R) till Azure File Sync
Så här migrerar du en DFS-R-distribution till Azure File Sync:

1. Skapa en Sync-grupp som representerar den DFS-R-topologi som du ersätter.
2. Starta på den server som har en fullständig uppsättning data i din DFS-R-topologi för att migrera. Installera Azure File Sync på den servern.
3. Registrera servern och skapa en server slut punkt för den första servern som ska migreras. Aktivera inte moln nivåer.
4. Låt all data synkroniseras med din Azure-filresurs (moln slut punkt).
5. Installera och registrera Azure File Sync-agenten på var och en av de återstående DFS-R-servrarna.
6. Disable DFS-R. 
7. Skapa en server slut punkt på varje DFS-R-Server. Aktivera inte moln nivåer.
8. Se till att synkroniseringen är klar och testa din topologi som önskad.
9. Retire DFS-R.
10. Moln nivåer kan nu aktive ras på alla Server slut punkter efter behov.

Mer information finns i [Azure File Sync interop med Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort en Azure File Sync Server slut punkt](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
