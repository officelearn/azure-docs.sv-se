---
title: Verktyg för lagring av Azure Stack | Microsoft Docs
description: Lär dig mer om Azure Stack lagringsdata verktyg för överföring
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 46bc043681bbba37a2dfb6476e1dd0784e1abf10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966710"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Använd verktyg för överföring av data för Azure Stack-lagring

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Microsoft Azure Stack tillhandahåller en uppsättning lagringstjänster för diskar, blobbar, tabeller, köer och hanteringsfunktioner för kontot. Du kan använda en uppsättning verktyg för Azure storage om du vill hantera eller flytta data till eller från Azure Stack-lagring. Den här artikeln innehåller en översikt över tillgängliga verktyg.

Dina krav fastställer vilka av följande verktyg fungerar bäst för dig:

* [AzCopy](#azcopy)

    Ett storage-specifika, kommandoradsverktyg verktyg som du kan ladda ned för att kopiera data från ett objekt till ett annat objekt i ditt lagringskonto eller mellan lagringskonton.

* [Azure PowerShell](#azure-powershell)

    Ett uppgiftsbaserat, kommandorads-gränssnittet och skriptspråk som utformats specifikt för systemadministration.

* [Azure CLI](#azure-cli)

    En öppen källkod, plattformsoberoende verktyg som innehåller en uppsättning kommandon för att arbeta med Azure och Azure Stack-plattformar.

* [Microsoft Lagringsutforskaren](#microsoft-azure-storage-explorer)

    En fristående app lätt att använda med ett användargränssnitt.

* [Blobfuse ](#blobfuse)

    En virtuell filsystemsdrivrutin för Azure Blob Storage, vilket ger dig tillgång till dina befintliga block blob-data i ditt Storage-konto via filsystemet Linux. 

På grund av storage services skillnaderna mellan Azure och Azure Stack, kan det finnas vissa krav för varje verktyg som beskrivs i följande avsnitt. En jämförelse mellan Azure Stack storage och Azure storage finns i [Azure Stack-lagring: skillnader och överväganden](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure blob- och table storage med hjälp av enkla kommandon med optimala prestanda. Du kan kopiera data från ett objekt till en annan i ditt lagringskonto eller mellan lagringskonton.

### <a name="download-and-install-azcopy"></a>Ladda ned och installera AzCopy

Det finns två versioner av AzCopy-verktyget: AzCopy på Windows och AzCopy i Linux.

 - **AzCopy i Windows**
    - Ladda ned versionen som stöds av AzCopy för Azure Stack. Du kan installera och använda AzCopy på Azure Stack på samma sätt som Azure. Mer information finns i [AzCopy på Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
        - För 1802 uppdatering eller nyare versioner [hämta AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
        - För tidigare versioner [hämta AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy i Linux**

    - AzCopy i Linux stöder Azure Stack 1802 update eller senare versioner. Du kan installera och använda AzCopy på Azure Stack på samma sätt som Azure. Mer information finns i [AzCopy i Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy kommandoexempel för dataöverföring

I följande exempel följer några vanliga scenarier för att kopiera data till och från Azure Stack-blobar. Mer information finns i [AzCopy på Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) och [AzCopy i Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Ladda ned alla blobar till en lokal disk

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>Ladda upp en fil till virtuell katalog

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Flytta data mellan Azure och Azure Stack-lagring

Asynkron dataöverföring mellan Azure storage och Azure Stack stöds inte. Måste du ange att överföra med den **/SyncCopy** eller **--synkroniserad kopia** alternativet.

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy kända problem

 - Alla AzCopy-åtgärder på ett filarkiv är inte tillgänglig eftersom fillagring ännu inte är tillgängliga i Azure Stack.
 - Asynkron dataöverföring mellan Azure storage och Azure Stack stöds inte. Du kan ange att överföra med den **/SyncCopy** alternativet att kopiera data.
 - Linux-versionen av Azcopy stöder bara 1802 uppdateringen eller senare versioner. Och det stöder inte tabelltjänsten.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera tjänster på både Azure och Azure Stack. Det är ett uppgiftsbaserat, kommandorads-gränssnittet och skriptspråk som utformats specifikt för systemadministration.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installera och konfigurera PowerShell för Azure Stack

Azure Stack-kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure Stack. Mer information finns i [installera PowerShell för Azure Stack](azure-stack-powershell-install.md) och [konfigurerar du Azure Stack PowerShell-miljö](azure-stack-powershell-configure-user.md) vill veta mer.

### <a name="powershell-sample-script-for-azure-stack"></a>PowerShell-exempelskript för Azure Stack 

Det här exemplet förutsätter att du har lyckats [installerat PowerShell för Azure Stack](azure-stack-powershell-install.md). Det här skriptet kan du slutföra konfigurationen och be Azure Stack-klientorganisation autentiseringsuppgifter för att lägga till ditt konto i lokala PowerShell-miljön. Sedan kommer skriptet standardvärdet Azure-prenumeration, skapa ett nytt lagringskonto i Azure, skapa en ny behållare i den här nya storage-konto och ladda upp en befintlig fil (blob) till behållaren. När skriptet visar en lista över alla blobar i behållaren, kommer den skapa en ny målkatalog på den lokala datorn och hämta image-filen.

1. Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).
2. Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).
3. Öppna **Windows PowerShell ISE** och **kör som administratör**, klickar du på **filen** > **New** att skapa en ny skriptfil.
4. Kopiera skriptet nedan och klistra in till den nya skriptfilen.
5. Uppdatera skriptvariabler baserat på dina inställningar.
   > [!NOTE]
   > Det här skriptet måste köras i rotkatalogen för **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
````

### <a name="powershell-known-issues"></a>PowerShell kända problem

Den aktuella kompatibla Azure PowerShell-modulversionen för Azure Stack är 1.2.11 för användaråtgärder. Den skiljer sig från den senaste versionen av Azure PowerShell. Den här skillnaden påverkar storage services-åtgärd:

* Returvärdet formatet `Get-AzureRmStorageAccountKey` i version 1.2.11 har två egenskaper: `Key1` och `Key2`, medan den aktuella versionen av Azure returnerar en matris som innehåller alla nycklar för kontot.

   ```
   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   Mer information finns i [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan installera det på macOS, Linux och Windows och köra den från kommandoraden.

Azure CLI är optimerad för att hantera och administrera Azure-resurser från kommandoraden och för att skapa automatiseringsskript som fungerar mot Azure Resource Manager. Den innehåller många av funktionerna i Azure Stack-portalen, inklusive omfattande dataåtkomst.

Azure Stack kräver Azure CLI version 2.0 eller senare. Läs mer om att installera och konfigurera Azure CLI med Azure Stack, [installera och konfigurera Azure Stack CLI](azure-stack-version-profiles-azurecli2.md). Mer information om hur du använder Azure CLI för flera uppgifter ska arbeta med resurser i ditt lagringskonto i Azure Stack finns i [med hjälp av Azure CLI med Azure storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure CLI-exempelskriptet för Azure Stack

När du har slutfört CLI-installationen och konfigurationen du följande steg för att arbeta med ett litet shell-exempelskript för att interagera med Azure Stack-lagringsresurser. Skriptet har följande åtgärder:

* Skapar en ny behållare i ditt storage-konto.
* Överför en befintlig fil (som en blob) till behållaren.
* Visar en lista över alla blobar i behållaren.
* Laddar ned filen till ett mål på din lokala dator som du anger.

Innan du kör det här skriptet, se till att du kan har ansluta till, och logga in på target Azure Stack.

1. Öppna valfri textredigerare och sedan kopiera och klistra in det här skriptet i redigeraren.
2. Uppdatera skriptvariabler för att återspegla konfigurationsinställningarna.
3. När du har uppdaterat de nödvändiga variablerna, spara skriptet och avsluta redigeringsprogram. Nästa steg förutsätter att du har gett ditt skript **my_storage_sample.sh**.
4. Markera skriptet som körbara, om det behövs: `chmod +x my_storage_sample.sh`
5. Kör skriptet. Till exempel i Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
````

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Lagringsutforskaren

Microsoft Azure storage explorer är en fristående app från Microsoft. Det kan du enkelt att arbeta med både Azure storage och Azure Stack lagring data i Windows, macOS och Linux-datorer. Om du vill att ett enkelt sätt att hantera dina data för lagring av Azure Stack, Överväg att använda Microsoft Azure Lagringsutforskaren.

* Mer information om hur du konfigurerar Azure storage explorer för att arbeta med Azure Stack finns [Anslut storage explorer till en Azure Stack-prenumeration](azure-stack-storage-connect-se.md).
* Läs mer om Microsoft Azure storage explorer i [Kom igång med storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) är en virtuell filsystemsdrivrutin för Azure Blob Storage, vilket ger dig tillgång till dina befintliga block blob-data i ditt Storage-konto via filsystemet Linux. Azure Blob Storage är en tjänst för lagring av objekt och därför inte ett hierarkiskt namnområde. Blobfuse ger det här namnområdet med virtuella direcectory med hjälp av snedstreck `/` som avgränsare. Blobfuse fungerar på både Azure och Azure Stack. 

Mer information om hur du monterar Blob storage som ett filsystem med Blobfuse på Linux finns [så montera Blob storage som ett filsystem med Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

För Azure Stack, **blobEndpoint** måste anges förutom accountName, accountKey/sasToken, containerName, när du konfigurerar autentiseringsuppgifterna för ditt Lagringskonto i steg i att förbereda för montering. 

I Azure Stack development Kit, blobEndpoint vara `myaccount.blob.local.azurestack.external`. Kontakta molnadministratören och om du inte vet om slutpunkten i integrerade Azure Stack-system. 

Tänk på att accountKey och sasToken kan endast vara konfigurerade en i taget. När din lagringskontonyckel ges är konfigurationsfilen autentiseringsuppgifter i följande format: 

```text  
    accountName myaccount 
    accountKey myaccesskey== 
    containerName mycontainer 
    blobEndpoint myaccount.blob.local.azurestack.external
```

När token för delad åtkomst ges är konfigurationsfilen autentiseringsuppgifter i följande format:

```text  
    accountName myaccount 
    sasToken ?mysastoken 
    containerName mycontainer 
    blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Nästa steg

* [Anslut storage explorer till en Azure Stack-prenumeration](azure-stack-storage-connect-se.md)
* [Kom igång med storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure-konsekvent: skillnader och överväganden](azure-stack-acs-differences.md)
* [Introduktion till Microsoft Azure storage](../../storage/common/storage-introduction.md)
