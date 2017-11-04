---
title: "Verktyg för Azure-stacken lagring"
description: "Lär dig mer om Stack för Azure storage-data transfer-verktyg"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>Verktyg för Azure-stacken lagring

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Stacken för Microsoft Azure tillhandahåller en uppsättning lagringstjänster för diskar, blobbar, tabeller, köer och hanteringsfunktioner för kontot. Du kan använda en uppsättning av verktyg för Azure Storage om du vill hantera eller flytta data till eller från Azure Storage i stacken. Den här artikeln ger en snabb överblick över verktyg som finns tillgängliga.

Ett verktyg som passar dig bäst beror på dina behov:
* [AzCopy](#azcopy)

    Ett storage-specifika kommandoradsverktyg som du kan hämta för att kopiera data från ett objekt till en annan inom ditt lagringskonto eller mellan lagringskonton.

* [Azure PowerShell](#azure-powershell)

    Ett aktivitetsbaserat kommandoradsskal och skriptspråk som är särskilt utformat för systemadministration.

* [Azure CLI](#azure-cli)

    En öppen källkod, plattformsoberoende verktyg som innehåller en uppsättning kommandon för att arbeta med Azure och Azure Stack-plattformar.

* [Microsoft Lagringsutforskaren (förhandsversion)](#microsoft-azure-storage-explorer)

    En lättanvänd fristående app med ett användargränssnitt.

På grund av Storage services skillnaderna mellan Azure och Azure-stacken kanske vissa krav för varje verktyg som beskrivs i följande avsnitt. En jämförelse mellan Azure Stack lagringsutrymmet och Azure storage finns [Azure Stack Storage: skillnader och överväganden](azure-stack-acs-differences.md).


## <a name="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg för att kopiera data till och från Microsoft Azure Blob- och Table storage med hjälp av enkla kommandon med optimal prestanda. Du kan kopiera data från ett objekt till en annan inom ditt lagringskonto eller mellan lagringskonton. Det finns två versionen av AzCopy: AzCopy i Windows och AzCopy på Linux. Azure-stacken stöder endast Windows-versionen. 
 
### <a name="download-and-install-azcopy"></a>Hämta och installera AzCopy 
[Hämta](https://aka.ms/azcopyforazurestack) den Windows-versionen som stöds av AzCopy för Azure-stacken. Du kan installera och använda AzCopy på Azure-stacken på samma sätt som Azure. Läs mer i [överföra data med kommandoradsverktyget Azcopy](../../storage/common/storage-use-azcopy.md). 

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy kommandoexempel för dataöverföring
Följande exempel visar några vanliga scenarier för att kopiera data till och från Azure Stack-BLOB. Läs mer i [överföra data med kommandoradsverktyget Azcopy](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Hämta alla BLOB till lokal disk
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Överför en fil till virtuell katalog 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Flytta data mellan Azure och Azure-stacken Storage 
Asynkron dataöverföring mellan Azure Storage och Azure Stack stöds inte. Du måste ange att överföra med den `/SyncCopy` alternativet. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Azcopy kända problem
* En AzCopy-åtgärd på File storage är inte tillgänglig eftersom File Storage inte finns ännu i Azure-stacken.
* Asynkron dataöverföring mellan Azure Storage och Azure Stack stöds inte. Du kan ange att överföra med den `/SyncCopy` alternativet att kopiera data.
* Linux-versionen av Azcopy stöds inte för Azure Storage i stacken. 

## <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell är en modul som innehåller cmdlets för att hantera tjänster på både Azure och Azure-stacken. Det är ett uppgiftsbaserat kommandoradsgränssnitt och skriptspråk som utformats specifikt för systemadministration.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installera och konfigurera PowerShell för Azure-Stack
Azure Stack kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure-stacken. Mer information finns i [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md) och [konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md) vill veta mer.

### <a name="powershell-sample-script-for-azure-stack"></a>PowerShell-exempelskript för Azure-stacken 
Det här exemplet förutsätter att du har [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md). Det här skriptet kommer att conplete konfigurationen och be din Azure Stack-klient autentiseringsuppgifter för att lägga till ditt konto i den lokala PowerShell environemnt. Sedan kommer skriptet standardvärdet Azure-prenumeration, skapa ett nytt lagringskonto i Azure, skapa en ny behållare i den här nya storage-konto och ladda upp en befintlig bildfil (blob) till behållaren. När skriptet visar alla blobbar i behållaren, kommer den skapa en ny målkatalog i den lokala datorn och hämta avbildningsfilen.

1. Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
2. Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  
3. Öppna **Windows PowerShell ISE** och **kör som administratör**, klickar du på **filen** > **ny** att skapa en ny skriptfil.
4. Kopiera skriptet nedan och klistra in i en ny skriptfilen.
5. Uppdatera skriptvariabler baserat på inställningarna. 
6. Obs: det här skriptet måste köras under roten för hämtade **AzureStack_Tools**. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
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
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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
```

### <a name="powershell-known-issues"></a>PowerShell kända problem 
Den aktuella kompatibla Azure PowerShell-modulen versionen för Azure-stacken är 1.2.10. Den skiljer sig från den senaste versionen av Azure PowerShell. Denna skillnad påverkar Lagringsåtgärden tjänster:

* Returvärdet format `Get-AzureRmStorageAccountKey` i version 1.2.10 har två egenskaper: `Key1` och `Key2`, medan den aktuella versionen av Azure returnerar en matris som innehåller alla nycklar för kontot.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Mer information finns i [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI
Azure CLI är Azures kommandoradsverktyget upplevelse för att hantera Azure-resurser. Du kan installera den på macOS, Linux och Windows och kör det från kommandoraden. 

Azure CLI är optimerad för att hantera och administrera Azure-resurser från kommandoraden och för att skapa automatiseringsskript arbeta mot Azure Resource Manager. Det ger många av de funktioner i Azure Stack-portal, inklusive omfattande dataåtkomst.

Azure-stacken kräver Azure CLI version 2.0. Läs mer om att installera och konfigurera Azure CLI med Azure-stacken [installera och konfigurera Azure CLI för stacken](azure-stack-connect-cli.md). Mer information om hur du använder Azure CLI 2.0 för att utföra flera uppgifter arbeta med resurserna i Azure-stacken Storage-konto finns [med hjälp av Azure-CLI2.0 med Azure Storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure CLI-exempelskript för Azure-Stack 
När du har slutfört CLI installation och konfiguration kan du följande steg för att arbeta med en liten shell-exempelskript för att interagera med Azure-stacken lagringsresurser. Skriptet först skapar en ny behållare på ditt lagringskonto och sedan överför en befintlig fil (som en blob) till behållaren, visas alla blobbar i behållaren och slutligen hämtas filen till en plats på den lokala datorn som du anger. Innan du kör det här skriptet, kontrollera att du kan ansluta och logga in på målet Azure stacken. 
1. Öppna valfri textredigerare, och sedan kopiera och klistra in det här skriptet i redigeraren.
2. Uppdatera skriptvariabler för att återspegla konfigurationsinställningarna. 
3. När du har uppdaterat de nödvändiga variablerna, spara skriptet och avsluta redigeringsprogram. Nästa steg förutsätter att du har namngett skript-my_storage_sample.sh.
4. Markera skriptet som körbara, om det behövs:`chmod +x my_storage_sample.sh`
5. Kör skriptet. Till exempel i Bash:`./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

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
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Microsoft Azure Lagringsutforskaren är en fristående app från Microsoft. Det gör att du kan enkelt arbeta med Azure Storage- och Azure-stacken Storage-data i Windows, macOS och Linux. Om du vill att ett enkelt sätt att hantera dina Azure-stacken Storage data bör du använda Microsoft Azure Lagringsutforskaren.

Mer information om hur du konfigurerar Azure Lagringsutforskaren för att arbeta med Azure-stacken finns [ansluta Lagringsutforskaren till en prenumeration på Azure-stacken](azure-stack-storage-connect-se.md).

Läs mer om Microsoft Azure Lagringsutforskaren [Kom igång med Lagringsutforskaren (förhandsversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Nästa steg
* [Ansluta Lagringsutforskaren till en Azure-Stack-prenumeration](azure-stack-storage-connect-se.md)
* [Kom igång med Lagringsutforskaren (förhandsversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Konsekvent Azure storage: skillnader och överväganden](azure-stack-acs-differences.md)
* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)

