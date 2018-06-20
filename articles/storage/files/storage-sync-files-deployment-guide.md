---
title: Distribuera Azure filsynkronisering (förhandsversion) | Microsoft Docs
description: Lär dig hur du distribuerar Azure filsynkronisering från början till slut.
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
ms.date: 06/05/2017
ms.author: wgries
ms.openlocfilehash: f1230cbc4d654bfb59bb328ed7d75c6fa76ff10c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268250"
---
# <a name="deploy-azure-file-sync-preview"></a>Distribuera Azure filsynkronisering (förhandsgranskning)
Använda Azure filsynkronisering (förhandsgranskning) för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB och NFS FTPS. Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser [planera för distribution av en Azure-filer](storage-files-planning.md) och [planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
* Ett Azure storage-konto och ett Azure filresurs i samma region som du vill distribuera Azure filsynkronisering. Mer information finns i:
    - [Regional tillgänglighet](storage-sync-files-planning.md#region-availability) för Azure filsynkronisering.
    - [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en stegvis beskrivning av hur du skapar ett lagringskonto.
    - [Skapa en filresurs](storage-how-to-create-file-share.md) för en stegvis beskrivning av hur du skapar en filresurs.
* Minst en stöds instans av Windows Server eller Windows Server-klustret för att synkronisera med synkronisering av Azure-filen. Mer information om vilka versioner av Windows Server finns [samverkan med Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).
* Kontrollera PowerShell 5.1 är installerad på Windows Server. Om du använder Windows Server 2012 R2 kan du se till att du kör minst PowerShell 5.1. \*. Du kan på ett säkert sätt hoppa över kontrollen på Windows Server 2016 PowerShell 5.1 är den standard version out-of-box. Du kan kontrollera att du kör PowerShell 5.1 på Windows Server 2012 R2. \* genom att titta på värdet för den **PSVersion** -egenskapen för den **$PSVersionTable** objekt:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Om din PSVersion-värdet är mindre än 5.1. \*, som kommer att vara fallet med de flesta nya installationer av Windows Server 2012 R2, kan du lätt uppgradera genom att hämta och installera [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Ladda ned och installera för Windows Server 2012 R2 är lämpligt paket **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Azure filsynkronisering stöder ännu inte PowerShell 6 + på Windows Server 2012 R2 eller Windows Server 2016.
* AzureRM PowerShell-modulen på de servrar som du vill använda med synkronisering av Azure-filen. Mer information om hur du installerar AzureRM PowerShell-moduler finns [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Du bör alltid använda den senaste versionen av Azure PowerShell-moduler. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server till Azure filsynkronisering
För varje server som du tänker använda med synkronisering av Azure-filen, inklusive varje servernod i ett redundanskluster, inaktivera **Förbättrad säkerhetskonfiguration i Internet Explorer**. Detta krävs endast för inledande serverregistreringsguiden. Du kan aktivera det igen när servern har registrerats.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
1. Öppna Serverhanteraren.
2. Klicka på **lokal Server**:  
    ![”Lokal Server” på vänster sida av Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. På den **egenskaper** subpane, klicka på länken för **Förbättrad säkerhetskonfiguration**.  
    ![Fönstret ”Förbättrad säkerhetskonfiguration” i Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. I den **Förbättrad säkerhetskonfiguration i Internet Explorer** dialogrutan **av** för **administratörer** och **användare**:  
    ![Förbättrad säkerhetskonfiguration i Internet Explorer popup-fönster med ”av” markerad](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Om du vill inaktivera Förbättrad säkerhetskonfiguration i Internet Explorer, kör du följande från en upphöjd PowerShell-session:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure filsynkronisering agenten
Azure filsynkronisering agenten är hämtningsbara paket som gör det möjligt för Windows Server till att synkroniseras med en Azure-filresursen. 

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Du kan ladda ned agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När hämtningen är klar dubbelklickar du på MSI-paketet för att starta installationen av Azure filsynkronisering.

> [!Important]  
> Om du tänker använda Azure filsynkronisering med ett redundanskluster måste filsynkronisering för Azure-agenten installeras på varje nod i klustret.

Vi rekommenderar att du gör följande:
- Lämna standardinstallationssökvägen (C:\Program Files\Azure\StorageSyncAgent) för att förenkla underhållet för felsökning och server.
- Aktivera Microsoft Update för att hålla Azure filsynkronisering uppdaterade. Alla uppdateringar sker till Azure filsynkronisering agenten, inklusive funktionsuppdateringar och snabbkorrigeringar, från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen ska synkroniseras för Azure-filen. Mer information finns i [Azure filsynkronisering uppdateringsprincip](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure filen Sync är klar öppnas automatiskt Användargränssnittet för servern registrering. Du måste ha en lagring synkroniseringstjänsten innan registrating; finns i nästa avsnitt om hur du skapar en tjänst för synkronisering av lagring.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Kör följande PowerShell-kod för att hämta den aktuella versionen av Azure filsynkronisering agenten för ditt operativsystem och installera den på datorn.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="deploy-the-storage-sync-service"></a>Distribuera synkroniseringstjänsten för lagring 
Distributionen av Azure filsynkronisering börjar med att placera en **lagring synkroniseringstjänsten** resurs i en resursgrupp för valda prenumerationen. Vi rekommenderar att du etablerar som fåtal av dessa efter behov. Skapar du en förtroenderelation mellan servrarna och den här resursen och en server kan endast registreras på en lagring Sync-tjänsten. Därför rekommenderas att distribuera så många sync lagringstjänster som du behöver olika grupper av servrar. Tänk på att servrar från olika lagringsplatser sync services går inte att synkronisera med varandra.

> [!Note]
> Synkroniseringstjänsten lagring ärvda åtkomstbehörigheter från prenumeration och resursgrupp som har distribuerats till. Vi rekommenderar att du noggrant kontrollera vem som har åtkomst till den. Enheter med skrivbehörighet kan starta synkroniserar nya uppsättningar av filer från servrar har registrerats lagringen synkroniseringstjänsten och gör att data kan flöda till Azure-lagring som är tillgänglig för dem.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Om du vill distribuera en tjänst för synkronisering av lagring, gå till den [Azure-portalen](https://portal.azure.com/), klickar du på *ny* och sök sedan efter Azure filsynkronisering. I sökresultaten väljer **filsynkronisering i Azure (förhandsversion)**, och välj sedan **skapa** att öppna den **distribuera lagring Sync** fliken.

I fönstret som öppnas anger du följande information:

- **Namnet**: ett unikt namn (per prenumeration) för lagring Sync-tjänsten.
- **Prenumerationen**: prenumerationen som du vill skapa synkroniseringstjänsten för lagring. Beroende på din organisations Konfigurationsstrategi kanske du har åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje tjänst i molnet (till exempel Azure-filer).
- **Resursgruppen**: en resursgrupp är en logisk grupp med Azure-resurser, till exempel ett lagringskonto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resursgrupp eller Använd en befintlig resursgrupp för Azure filsynkronisering. (Vi rekommenderar att du använder resursgrupper som behållare för att isolera resurser logiskt för din organisation, till exempel gruppera HR resurser eller resurser för ett visst projekt.)
- **Plats**: den region där du vill distribuera Azure filsynkronisering. Endast regioner som stöds är tillgängliga i den här listan.

När du är klar väljer du **skapa** att distribuera lagring Sync-tjänsten.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Innan du interagerar med Azure filsynkronisering management-cmdlets, behöver du importera en DLL-fil och skapa en Azure filsynkronisering management kontext. Detta är nödvändigt eftersom de Azure filsynkronisering cmdletarna inte ännu en del av AzureRM PowerShell-modulen.

> [!Note]  
> StorageSync.Management.PowerShell.Cmdlets.dll paketet, som innehåller Azure filsynkronisering management-cmdlets (avsiktligt) innehåller en cmdlet med ett icke-godkända verb (`Login`). Namnet `Login-AzureRmStorageSync` valdes för att matcha den `Login-AzureRmAccount` alias för cmdleten i AzureRM PowerShell-modulen. Det här felmeddelandet (och cmdlet) kommer att tas bort filsynkronisering för Azure-agenten har lagts till i AzureRM PowerShell-modulen.

```PowerShell
$acctInfo = Login-AzureRmAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzureRmLocation | ForEach-Object { 
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
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzureRmResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

När du har skapat Azure filsynkronisering samband med den `Login-AzureRmStorageSync` cmdlet, som du kan skapa synkroniseringstjänsten för lagring. Se till att ersätta `<my-storage-sync-service>` med önskat namn för Storage-synkroniseringstjänsten.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server med synkroniseringstjänsten för lagring
Registrera din Windows-Server med en Storage-synkroniseringstjänsten upprättar en förtroenderelation mellan din server (eller ett kluster) och lagring-synkroniseringstjänsten. En server kan bara registreras en synkroniseringstjänsten för lagring och kan synkronisera med andra servrar och Azure-filresurser som är kopplade till samma synkroniseringstjänsten för lagring.

> [!Note]
> Registrera servern använder dina Azure-autentiseringsuppgifter för att skapa en förtroenderelation mellan synkroniseringstjänsten för lagring och Windows Server, men därefter servern skapar och använder eget identitet som är giltig så länge som servern är registrerad och aktuell signatur för delad åtkomst-token (SAS-lagring) är giltig. En ny SAS-token kan utfärdas till servern när servern är avregistrera därför att ta bort serverns möjlighet att komma åt din Azure-filresurser, stoppar alla synkronisering.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Användargränssnittet för servern registrering ska öppnas automatiskt efter installationen av Azure filsynkronisering agent. Om den inte kan du öppna det manuellt från dess filplats: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. När Användargränssnittet för servern registrering öppnas, Välj **inloggning** ska börja.

När du loggar in uppmanas du att ange följande information:

![En skärmbild av Användargränssnittet för servern registrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: den prenumeration som innehåller synkroniseringstjänsten lagring (se [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service)). 
- **Resursgruppen**: resursgruppen som innehåller synkroniseringstjänsten för lagring.
- **Storage-synkroniseringstjänsten**: namnet på synkroniseringsservern lagring som du vill registrera.

När du har valt rätt information, Välj **registrera** att slutföra registreringen av servern. Som en del av registreringen efterfrågas ett ytterligare inloggning.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en grupp för synkronisering och en molnslutpunkt
En synkronisering grupp definierar sync-topologin för en uppsättning filer. Slutpunkter inom en synkronisering grupp hålls synkroniserade med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs och en eller server-slutpunkter. En serverslutpunkt representerar en sökväg på registrerad server. En server kan innehålla flera synkroniseringsgrupper server-slutpunkter. Du kan skapa så många sync-grupper som du behöver till korrekt beskriva önskade sync-topologi.

En molnslutpunkt finns en pekare till ett Azure-filresursen. Alla server-slutpunkter kommer att synkroniseras med en molnslutpunkt gör moln slutpunkten hubben. Lagringskontot för Azure-filresursen måste finnas i samma region som synkroniseringstjänsten för lagring. Kommer att synkroniseras i sin helhet Azure-filresursen med ett undantag: en särskild mapp jämförbar dold ”System Volume Information”-mapp på en NTFS-volym, kommer att tillhandahållas. Den här katalogen kallas ”. SystemShareInformation ”. Den innehåller viktiga synka metadata som inte kommer att synkroniseras till andra slutpunkter. Använd inte eller ta bort den!

> [!Important]  
> Du kan göra ändringar till en molnslutpunkt eller Serverslutpunkten i gruppen synkronisering och ha dina filer synkroniseras till de andra slutpunkterna i gruppen synkronisering. Om du gör en ändring i molnslutpunkt (Azure-filresursen) direkt måste ändringar först identifieras av en Azure-filen ändras identifiering synkroniseringsjobb. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång per dygn. Mer information finns i [Azure Files vanliga frågor och](storage-files-faq.md#afs-change-detection).

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Skapa en sync-grupp i den [Azure-portalen](https://portal.azure.com/)går du till lagring-synkroniseringstjänsten och välj sedan **+ Sync grupp**:

![Skapa en ny grupp för synkronisering i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Ange följande information för att skapa en grupp för synkronisering med en molnslutpunkt i fönstret som öppnas:

- **Synkronisera gruppnamn**: namnet på sync-grupp som ska skapas. Det här namnet måste vara unika inom synkroniseringstjänsten lagring, men kan vara vilket namn som är logiska för dig.
- **Prenumerationen**: prenumeration där du har distribuerat Storage Sync-tjänsten i [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service).
- **Lagringskontot**: Om du väljer **Välj lagringskonto**, ett annat fönster visas där du väljer det lagringskonto som har Azure-filresursen som du vill synkronisera med.
- **Azure-filresursen**: namnet på Azure-filresursen som du vill synkronisera.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Kör följande PowerShell om du vill skapa gruppen synkronisering. Kom ihåg att ersätta `<my-sync-group>` med önskat namn i gruppen synkronisering.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

När gruppen synkronisering har skapats, kan du skapa moln slutpunkten. Se till att ersätta `<my-storage-account>` och `<my-file-share>` med de förväntade värdena.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzureRmStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzureStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Skapa en serverslutpunkt
En serverslutpunkt representerar en specifik plats på en registrerad server, till exempel en mapp på en server-volym. En Serverslutpunkten måste vara en sökväg på en registrerad server (i stället för en monterad resurs) och för att använda molnet skiktning sökvägen måste vara på en systemvolym. Nätverksansluten lagring (NAS) stöds inte.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Om du vill lägga till en serverslutpunkt, gå till den nyligen skapade sync-gruppen och välj sedan **Lägg till serverslutpunkt**.

![Lägg till en ny serverslutpunkt i fönstret sync grupp](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I den **Lägg till serverslutpunkt** rutan anger du följande information för att skapa en serverslutpunkt för:

- **Registrerad server**: namnet på servern eller klustret där du vill skapa serverslutpunkt.
- **Sökvägen**: I Windows Server-sökvägen till synkroniseras som en del av gruppen synkronisering.
- **Cloud Tiering**: en växel för att aktivera eller inaktivera molnet skiktning. Kan nivåindelas med molnet skiktning, sällan används eller filer till Azure Files.
- **Ledigt utrymme på volymen**: mängden ledigt utrymme som ska reserveras på volymen där serverns slutpunktens finns. Till exempel om ledigt utrymme på volymen anges till 50% på en volym som har en enda serverslutpunkt är ungefär hälften av data nivåer till Azure Files. Oavsett om molnet skiktning är aktiverat, Azure-filresursen har alltid en fullständig kopia av data i gruppen synkronisering.

Om du vill lägga till Serverslutpunkten, Välj **skapa**. Filerna är nu synkroniseras mellan din Azure-filresursen och Windows Server. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Kör följande PowerShell-kommandon för att skapa Serverslutpunkten och se till att ersätta `<your-server-endpoint-path>` och `<your-volume-free-space>` med önskade värden.

```PowerShell
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
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Onboarding med Azure File synkronisering
Rekommenderade åtgärder ska publiceras på Azure filsynkronisering för först med noll avbrottstid samtidigt som fullständiga återgivning och åtkomstkontrollistan (ACL) är följande:
 
1. Distribuera en tjänst för synkronisering av lagring.
2. Skapa en grupp för synkronisering.
3. Installera Azure filsynkronisering agenten på servern med fullständig datauppsättning.
4. Registrera servern och skapa en serverslutpunkt för resursen. 
5. Kan synkronisera gör fullständig överföringen till Azure-filresursen (molnslutpunkt).  
6. När den första överföringen är klar, installerar du Azure filsynkronisering agenten på de återstående servrarna.
7. Skapa nya filresurser på var och en av de återstående servrarna.
8. Skapa server-slutpunkter på nya filresurser med principer för molnet lagringsnivåer, om så önskas. (Det här steget kräver ytterligare lagringsutrymme ska vara tillgänglig för den första installationen.)
9. Kan Azure filsynkronisering agent om du vill göra en snabb återställning av det fullständiga namnområdet utan faktiska dataöverföringen. Efter fullständig namnområde-synkronisering fylls Synkroniseringsmotorn det lokala diskutrymme baserat på principer för lagringsnivåer för molnet för Serverslutpunkten. 
10. Se till att synkroniseringen har slutförts och testa din topologi efter behov. 
11. Omdirigera användare och program till nya resursen.
12. Du kan eventuellt ta bort alla dubbla resurser på servrarna.
 
Om du inte har extra lagring för inledande onboarding och vill ansluta till de befintliga resurserna, kan du initiera data i Azure filresurser före. Den här metoden föreslås endast om du kan acceptera avbrott och absolut garantera att inga dataändringar för serverresurser under inledande onboarding-processen. 
 
1. Se till att data på någon av servrarna inte kan ändras under onboarding-processen.
2. Före utsäde Azure filresurser med data på servern med ett verktyg för överföring av data över SMB t.ex. Robocopy direkt SMB kopia. Eftersom AzCopy inte ladda upp data via SMB så den kan inte användas för före seeding.
3. Skapa Azure filsynkronisering topologi med önskad server-slutpunkter som pekar på de befintliga resurserna.
4. Kan synkronisera Slutför avstämning process på alla slutpunkter. 
5. När avstämningen är klar kan öppna du resurser för ändringar.
 
Tänk på att för närvarande före seeding metod har några begränsningar- 
- Fullständig återgivning på filer sparas inte. Till exempel förlorar filer ACL: er och tidsstämplar.
- Dataändringar på servern innan sync topologin är helt upp och körs kan orsaka konflikter på server-slutpunkter.  
- När molnet slutpunkten har skapats körs Azure filsynkronisering en process för att identifiera filer i molnet innan du startar den första synkroniseringen. Den tid det tar att slutföra den här processen varierar beroende på de olika faktorerna som nätverkshastigheten, tillgänglig bandbredd och antalet filer och mappar. För grov uppskattning i förhandsversionen körs identifieringsprocessen ungefär vid 10 filer per sekund.  Därför måste även om seeding före körs snabbt vara den totala tid att hämta ett fullständigt system betydligt längre när data är förinstallerade i molnet.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en distribution av DFS Replication (DFS-R) till Azure filsynkronisering
Att migrera en DFS-R-distribution till Azure filsynkronisering:

1. Skapa en grupp för synkronisering för att representera DFS-R-topologi som du ersätter.
2. Starta på den server som har en fullständig uppsättning data i DFS-R-topologi för att migrera. Installera Azure filsynkronisering på servern.
3. Registrera servern och skapa en serverslutpunkt för den första servern som ska migreras. Aktivera inte molnet skiktning.
4. Låt alla datasynkronisering till din Azure-filresurs (molnslutpunkt).
5. Installera och registrera filsynkronisering för Azure-agenten på de återstående DFS-R-servrarna.
6. Inaktivera DFS-r 
7. Skapa en serverslutpunkt på DFS-R-servrarna. Aktivera inte molnet skiktning.
8. Se till att synkroniseringen har slutförts och testa din topologi efter behov.
9. Ta bort DFS-r
10. Molnet lagringsnivåer kan nu aktiveras på valfri serverslutpunkt efter behov.

Mer information finns i [Azure filsynkronisering interop med Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägg till eller ta bort en Azure-filen Sync Serverslutpunkt](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure filsynkronisering](storage-sync-files-server-registration.md)