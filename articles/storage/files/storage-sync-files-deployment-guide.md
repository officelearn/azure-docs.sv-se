---
title: Distribuera Azure File Sync | Microsoft Docs
description: Lär dig hur du distribuerar Azure File Sync, från början till slut.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: eeb9765cfd6242ecdc14dd59dd9b5337cc56c597
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481223"
---
# <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync
Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser [planera för distribution av Azure Files](storage-files-planning.md) och [planera för distribution av Azure File Sync](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
* Ett Azure storage-konto och ett Azure filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regiontillgänglighet](storage-sync-files-planning.md#region-availability) för Azure File Sync.
    - [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en stegvis beskrivning av hur du skapar ett lagringskonto.
    - [Skapa en filresurs](storage-how-to-create-file-share.md) för en stegvis beskrivning av hur du skapar en filresurs.
* Minst en instans av Windows Server eller Windows Server-kluster för att synkronisera med Azure File Sync som stöds. Mer information om vilka versioner av Windows Server finns i [samverkan med Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Kontrollera PowerShell 5.1 är installerad på Windows Server. Om du använder Windows Server 2012 R2 kan du se till att du kör minst PowerShell 5.1. \*. Du kan på ett säkert sätt hoppa över den här kontrollen på Windows Server 2016 PowerShell 5.1 är den standard version out-of-box. Du kan kontrollera att du kör PowerShell 5.1 på Windows Server 2012 R2. \* genom att titta på värdet för den **PSVersion** egenskapen för den **$PSVersionTable** objekt:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Om ditt PSVersion värde är mindre än 5.1. \*, som kommer att vara fallet med de flesta nya installationer av Windows Server 2012 R2, är lätt att uppgradera genom att ladda ned och installera [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Ladda ned och installera för Windows Server 2012 R2 är lämpligt paket **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Azure File Sync stöder ännu inte PowerShell 6 + på Windows Server 2012 R2 eller Windows Server 2016.
* Az- och AzureRM PowerShell-moduler.
    - Az-modulen kan installeras genom att följa instruktionerna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
    - Modulen AzureRM PowerShell kan installeras genom att köra följande PowerShell-cmdlet:
    
        ```powershell
        Install-Module AzureRM
        ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server för användning med Azure File Sync
För varje server som du planerar att använda med Azure File Sync, inklusive varje servernoden i ett redundanskluster, inaktivera **Förbättrad säkerhetskonfiguration i Internet Explorer**. Detta krävs endast för inledande serverregistrering. Du kan aktivera det igen när servern har registrerats.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Öppna Serverhanteraren.
2. Klicka på **lokal Server**:  
    ![”Lokal Server” på vänster sida av Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  
    ![Fönstret ”Förbättrad säkerhetskonfiguration” i Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. I den **Förbättrad säkerhetskonfiguration i Internet Explorer** dialogrutan **av** för **administratörer** och **användare**:  
    ![Förbättrad säkerhetskonfiguration i Internet Explorer pop-fönstret med ”av” markerad](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill inaktivera Förbättrad säkerhetskonfiguration i Internet Explorer, kör du följande från en upphöjd PowerShell-session:

```powershell
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
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Distribuera tjänsten för synkronisering av lagring 
Distributionen av Azure File Sync som börjar med att placera en **Lagringssynkroniseringstjänst** resurs i en resursgrupp för din valda prenumeration. Vi rekommenderar att du etablerar så lite av dessa efter behov. Du skapar en förtroenderelation mellan dina servrar och den här resursen och en server kan bara registreras för en Lagringssynkroniseringstjänst. Vi rekommenderar därför att distribuera så många lagringstjänster för synkronisering som du behöver till olika grupper av servrar. Tänk på att servrar från olika lagringstjänster för synkronisering inte kan synkronisera med varandra.

> [!Note]
> Storage Sync-tjänsten ärvda åtkomstbehörigheter från prenumeration och resursgrupp som den har distribuerats till. Vi rekommenderar att du noggrant kontrollera vem som har åtkomst till den. Entiteter med skrivåtkomst kan börja synkronisera nya grupper av filer från servrar som registrerats till den här lagringen synkroniseringstjänsten och orsaka data kan flöda till Azure storage som är tillgänglig för dem.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Om du vill distribuera en tjänst för Lagringssynkronisering, går du till den [Azure-portalen](https://portal.azure.com/), klickar du på *skapa en resurs* och söker sedan efter Azure File Sync. I sökresultaten väljer **Azure File Sync**, och välj sedan **skapa** att öppna den **distribuera synkronisering av lagring** fliken.

I fönstret som öppnas anger du följande information:

- **Namn**: Ett unikt namn (per prenumeration) för tjänsten för synkronisering av lagring.
- **Prenumeration**: Den prenumeration där du vill skapa Storage Sync-tjänsten. Beroende på organisationens Konfigurationsstrategi, kanske du har åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje tjänst i molnet (till exempel Azure Files).
- **Resursgrupp**: En resursgrupp är en logisk grupp av Azure-resurser, till exempel ett lagringskonto eller en Lagringssynkroniseringstjänst. Du kan skapa en ny resursgrupp eller Använd en befintlig resursgrupp för Azure File Sync. (Vi rekommenderar att använda resursgrupper som behållare för att isolera resurser logiskt för din organisation, till exempel gruppera HR resurser eller resurser för ett specifikt projekt.)
- **Plats**: Den region där du vill distribuera Azure File Sync. Endast regioner som stöds är tillgängliga i den här listan.

När du är klar väljer du **skapa** att distribuera Storage Sync-tjänsten.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Innan du interagerar med Azure File Sync management-cmdletar, behöver du importera en DLL-fil och skapa en kontext för hantering av Azure File Sync. Detta är nödvändigt eftersom de Azure File Sync cmdletarna inte ännu en del av Azure PowerShell-moduler.

> [!Note]  
> StorageSync.Management.PowerShell.Cmdlets.dll paketet, som innehåller management-cmdletar för Azure File Sync (avsiktligt) innehåller en cmdlet med ett icke-godkända verb (`Login`). Namnet `Login-AzureStorageSync` valdes för att matcha den `Login-AzAccount` alias för cmdleten i Azure PowerShell-modulen. Det här felmeddelandet (och cmdleten) tas bort när Azure File Sync-agenten har lagts till i Azure PowerShell-modulen.

```powershell
$acctInfo = Login-AzAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

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

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    -SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

När du har skapat Azure File Sync-kontext med den `Login-AzureR,StorageSync` cmdlet, som du kan skapa Storage Sync-tjänsten. Se till att ersätta `<my-storage-sync-service>` med önskad namnet på Storage Sync-tjänsten.

```powershell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Du kan ladda ned agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När nedladdningen är klar dubbelklickar du på MSI-paketet för att starta installationen av Azure File Sync-agenten.

> [!Important]  
> Om du planerar att använda Azure File Sync med ett redundanskluster, måste Azure File Sync-agenten installeras på varje nod i klustret. Varje nod i klustret måste vara registrerad för att fungera med Azure File Sync.

Vi rekommenderar att du gör följande:
- Lämna standardinstallationssökväg (C:\Program Files\Azure\StorageSyncAgent) för att förenkla underhållet för felsökning och server.
- Aktivera Microsoft Update för att hålla Azure File Sync uppdaterade. Alla uppdateringar sker av Azure File Sync-agenten, inklusive funktionsuppdateringar och snabbkorrigeringar, från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen till Azure File Sync. Mer information finns i [Azure File Sync-uppdateringsprincip](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure File Sync-agent är klar öppnas automatiskt Användargränssnittet för registrering av servern. Du måste ha en tjänst för Lagringssynkronisering innan du registrerar; Se nästa avsnitt om hur du skapar en Lagringssynkroniseringstjänst.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kod för att hämta den aktuella versionen av Azure File Sync-agenten för ditt operativsystem och installera den på datorn.

> [!Important]  
> Om du planerar att använda Azure File Sync med ett redundanskluster, måste Azure File Sync-agenten installeras på varje nod i klustret. Varje nod i klustret måste vara registrerad för att fungera med Azure File Sync.

```powershell
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

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server i tjänsten för synkronisering av lagring
När du registrerar Windows Server med en tjänst för synkronisering av lagring etableras en förtroenderelation mellan din server (eller ditt kluster) och tjänsten för synkronisering av lagring. En server kan bara registreras på en tjänst för synkronisering av lagring och kan synkronisera med andra servrar och Azure-filresurser som associeras med samma tjänst för synkronisering av lagring.

> [!Note]
> Registrera servern använder dina Azure-autentiseringsuppgifter för att skapa en förtroenderelation mellan Storage Sync-tjänsten och Windows Server, men sedan servern skapar och använder sin egen identitet som är giltiga så länge som servern är registrerad och aktuell signatur för delad åtkomst-token (Storage SAS) är giltig. En ny SAS-token kan inte utfärdas till servern när servern är avregistrerade, vilket tar bort serverns möjlighet att komma åt Azure-filresurser, stoppar alla synkronisering.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Användargränssnittet för servern registrering ska öppnas automatiskt efter installationen av Azure File Sync-agenten. Om det inte öppna kan du öppna det manuellt från dess filplats: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. När Användargränssnittet för servern registrering öppnas väljer **inloggning** att börja.

När du loggar in uppmanas du att ange följande information:

![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: Den prenumeration som innehåller Storage Sync-tjänsten (se [distribuera Storage Sync-tjänsten](#deploy-the-storage-sync-service)). 
- **Resursgrupp**: Den resursgrupp som innehåller tjänsten för synkronisering av lagring.
- **Tjänst för lagringssynkronisering**: Namnet på Storage Sync-tjänsten som du vill registrera.

När du har valt informationen som krävs, Välj **registrera** att slutföra registreringen för servern. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en molnslutpunkt
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på registrerad server. En server kan ha serverslutpunkter i flera synkroniseringsgrupper. Du kan skapa så många synkroniseringsgrupper som du behöver på lämpligt sätt beskriva dina önskade sync-topologi.

En molnslutpunkt finns en pekare till en Azure-filresurs. Alla serverslutpunkter synkroniseras med en slutpunkt, vilket gör molnslutpunkten hubben. Storage-konto för Azure-filresursen måste finnas i samma region som Storage Sync-tjänsten. Att kommer synkroniseras i sin helhet i Azure-filresursen med ett undantag: En särskild mapp jämförbara för dolda ”System Volume Information”-mapp på en NTFS-volym, kommer att tillhandahållas. Den här katalogen kallas ”. SystemShareInformation ”. Den innehåller viktiga synka metadata som inte synkroniseras till andra slutpunkter. Använd inte eller ta bort den!

> [!Important]  
> Du kan göra ändringar till molnslutpunkt eller serverslutpunkt i synkroniseringsgruppen och ha dina filer synkroniseras till de andra slutpunkterna i synkroniseringsgruppen. Om du gör en ändring till molnslutpunkten (Azure-filresurs) direkt, måste ändringar först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång var 24: e timme. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Skapa en synkroniseringsgrupp i den [Azure-portalen](https://portal.azure.com/)går du till Storage Sync-tjänsten och välj sedan **+ synkroniseringsgruppen**:

![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

- **Namn på synkroniseringsgrupp**: Namnet på synkroniseringsgruppen skapas. Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig.
- **Prenumeration**: Den prenumeration där du har distribuerat Storage Sync-tjänsten i [distribuera Storage Sync-tjänsten](#deploy-the-storage-sync-service).
- **Lagringskonto**: Om du väljer **Välj lagringskonto**, ett annat fönster visas där du kan välja det lagringskonto som har Azure-filresursen som du vill synkronisera med.
- **Azure-filresurs**: Namnet på Azure-filresursen som du vill synkronisera.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell för att skapa synkroniseringsgruppen. Kom ihåg att ersätta `<my-sync-group>` med önskad namnet på synkroniseringsgruppen.

```powershell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

När synkroniseringsgruppen har skapats, kan du skapa din slutpunkt i molnet. Se till att ersätta `<my-storage-account>` och `<my-file-share>` med de förväntade värdena.

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
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id `
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Skapa en serverslutpunkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. En serverslutpunkt måste vara en sökväg på en registrerad server (i stället för en ansluten resurs) och om du vill använda lagringsnivåer för moln sökvägen måste finnas på en systemvolym. Nätverksansluten lagring (NAS) stöds inte.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Om du vill lägga till en serverslutpunkt, gå till den nyligen skapade synkroniseringsgruppen och välj sedan **Lägg till serverslutpunkt**.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

- **Registrerad server**: Namnet på servern eller klustret där du vill skapa Serverslutpunkten.
- **Sökväg**: Windows Server-sökvägen till synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivå**: En växel för att aktivera eller inaktivera lagringsnivåer. Med molnet lagringsnivåer, sällan används eller filer kan vara nivåindelad till Azure Files.
- **Ledigt utrymme på volym**: Mängden ledigt utrymme för att reservera på volymen där Serverslutpunkten finns. Till exempel om volymens lediga utrymme är inställt på 50% på en volym som har en enskild server-slutpunkt, är ungefär hälften av data nivåindelad till Azure Files. Oavsett om molnet lagringsnivåer har aktiverats kan din Azure-filresurs har alltid en fullständig kopia av data i synkroniseringsgruppen.

Om du vill lägga till Serverslutpunkten, Välj **skapa**. Dina filer nu synkroniseras i Azure-filresurs- och Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kommandon för att skapa Serverslutpunkten och se till att ersätta `<your-server-endpoint-path>` och `<your-volume-free-space>` med önskade värden.

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

## <a name="onboarding-with-azure-file-sync"></a>Registrering med Azure File Sync
Rekommenderade åtgärder för att publicera på Azure File Sync för första med noll avbrottstid och behålla fullständig exakthet och åtkomstkontrollistan (ACL) är följande:
 
1. Distribuera en Lagringssynkroniseringstjänst.
2. Skapa en synkroniseringsgrupp.
3. Installera Azure File Sync-agenten på servern med fullständiga data.
4. Registrera servern och skapa en serverslutpunkt för resursen. 
5. Låt synkronisering göra fullständiga överföringen till Azure-filresurs (molnslutpunkt).  
6. När den inledande överföringen är klar, kan du installera Azure File Sync-agenten på var och en av de återstående servrarna.
7. Skapa nya filresurser på var och en av de återstående servrarna.
8. Skapa serverslutpunkter på nya filresurser med principer för cloud lagringsnivåer, om så önskas. (Det här steget kräver ytterligare lagring som ska vara tillgängliga för den första installationen.)
9. Låt Azure File Sync-agenten om du vill göra en snabb återställning av fullständig namnområdet utan faktiska dataöverföringen. När den fullständiga namnområde synkroniseringen kommer Synkroniseringsmotorn fyller du det lokala diskutrymmet baserat på principer för lagringsnivåer för moln för Serverslutpunkten. 
10. Se till att synkroniseringen är klar och testa din topologi som du vill. 
11. Omdirigera användare och program till den här nya resursen.
12. Du kan också ta bort alla dubbla resurser på servrarna.
 
Om du inte har extra lagring för inledande registrering och vill ansluta till de befintliga resurserna, kan du förekomma data i Azure-filresurser. Den här metoden rekommenderas om du kan acceptera driftstopp och garanterar absolut inga dataändringar på server-resurser under den inledande onboarding-processen. 
 
1. Se till att data på servern inte kan ändras under onboarding-processen.
2. Före utsäde Azure-filresurser med server-data med valfritt verktyg för överföring av data över SMB t.ex. Robocopy direkt SMB-kopiering. Eftersom AzCopy inte överför data över SMB, så den inte kan användas för förväg seeding.
3. Skapa Azure File Sync-topologi med önskad serverslutpunkter som pekar på de befintliga resurserna.
4. Låt synkronisering Slutför avstämningsprocessen för alla slutpunkter. 
5. När avstämningen är klar kan öppna du resurser för ändringar.
 
Tänk på att för närvarande förväg seeding metoden har vissa begränsningar – 
- Fullständig återgivning på filer bevaras inte. Till exempel förlora filer ACL: er och tidsstämplar.
- Dataändringar på servern innan synkronisering topologin är helt upp och körs kan orsaka konflikter på serverslutpunkter.  
- När molnslutpunkten har skapats kan kör Azure File Sync en process för att identifiera filerna i molnet innan du påbörjar den första synkroniseringen. Den tid det tar att slutföra den här processen varierar beroende på de olika faktorerna som nätverkshastigheten, tillgänglig bandbredd och antalet filer och mappar. För grov uppskattning i förhandsversionen körs identifieringsprocessen cirka med 10 filer/sek.  Därför även om seeding förväg körs snabbt kan den totala tid att hämta ett fullständigt system vara betydligt längre när data är förinstallerade i molnet.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en distribution av DFS Replication (DFS-R) till Azure File Sync
Att migrera en DFS-R-distribution till Azure File Sync:

1. Skapa en synkroniseringsgrupp för att representera DFS-R-topologi som ska ersättas.
2. Starta på den server som har en fullständig uppsättning data i din DFS-R-topologi för att migrera. Installera Azure File Sync på den servern.
3. Registrera servern och skapa en serverslutpunkt för den första servern som ska migreras. Aktivera inte cloud lagringsnivåer.
4. Låt alla datasynkronisering till din Azure-filresurs (molnslutpunkt).
5. Installera och registrera Azure File Sync-agenten på var och en av de återstående DFS-R-servrarna.
6. Disable DFS-R. 
7. Skapa en serverslutpunkt på var och en av DFS-R-servrar. Aktivera inte cloud lagringsnivåer.
8. Se till att synkroniseringen är klar och testa din topologi som du vill.
9. Retire DFS-R.
10. Molnet lagringsnivåer kan nu aktiveras på valfri serverslutpunkt som du vill.

Mer information finns i [Azure File Sync interop med distribuerade System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägg till eller ta bort en Serverslutpunkt för Azure File Sync](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
