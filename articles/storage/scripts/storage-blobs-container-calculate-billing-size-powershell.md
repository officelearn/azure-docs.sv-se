---
title: Azure PowerShell-skriptexempel – Beräkna den totala faktureringsstorleken på en blob-container | Microsoft Docs
description: Beräkna den totala storleken på en container i Azure Blob Storage för faktureringsändamål.
services: storage
documentationcenter: na
author: WenJason
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
origin.date: 11/07/2017
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 02b4cfcc6d88430701f653665269532a4eb7092f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460647"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Beräkna den totala faktureringsstorleken på en blob-container

Det här skriptet beräknar storleken på en container i Azure Blob Storage för att ge en uppskattning av faktureringskostnaderna. Skriptet summerar storleken på blobarna i containern.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Den här PowerShell-skriptet beräknar storleken på en container för faktureringsändamål. Om du beräknar containerns storlek för andra ändamål hittar du ett enklare skript som ger en beräkning i [Beräkna den totala storleken på en Blob Storage-container](../scripts/storage-blobs-container-calculate-size-powershell.md).

## <a name="determine-the-size-of-the-blob-container"></a>Beräkna storleken på blob-containern

Den totala storleken på blob-containern inkluderar storleken på själva containern och storleken på alla blobar i containern.

Följande avsnitt beskriver hur lagringskapaciteten beräknas för blob-containrar och blobar. I följande avsnitt betyder Len(X) antalet tecken i strängen.

### <a name="blob-containers"></a>Blob-containrar

Följande beräkning beskriver hur du beräknar den mängd lagringsutrymme som förbrukas per blob-container:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Här följer analysen:
* 48 byte med tillhörande information för varje container innehåller tid för senaste ändring, behörigheter, offentliga inställningar och vissa systemmetadata.

* Containerns namn lagras som Unicode, så ta antalet tecken och multiplicera med två.

* För varje block med blob-containermetadata som lagras så lagrar vi namnets längd (ASCII) samt längden på strängvärdet.

* 512 byte per signerat ID innehåller namnet på det signerade ID:t, starttiden, förfallotiden och behörigheter.

### <a name="blobs"></a>Blobar

Följande beräkningar visar hur du beräknar den mängd lagringsutrymme som förbrukas per blob.

* Blockblob (grundläggande blob eller ögonblicksbild):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Sidblob (grundläggande blob eller ögonblicksbild):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Här följer analysen:

* 124 byte med tillhörande information för blob, vilket innefattar:
    - Ändrades senast
    - Storlek
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - Behörigheter
    - Ögonblicksbildsinformation
    - Livslängd
    - Vissa systemmetadata

* Blobnamnet lagras som Unicode, så ta antalet tecken och multiplicera med två.

* För varje block med metadata som lagras lägger du till namnets längd (lagras som ASCII) samt längden på strängvärdet.

* För blockblobarna:
  * 8 byte för blocklistan.
  * Antalet block gånger block-ID-storleken i byte.
  * Storleken på data i alla incheckade och ej incheckade block.

    >[!NOTE]
    >När ögonblicksbilder används innehåller den här storleken bara unika data för den här grundläggande bloben eller ögonblicksbloben. Om de ej incheckade blocken inte används efter en vecka samlas de upp som skräp. Efter det räknas de inte mot fakturering.

* För sidblobar:
  * Antalet flera ej efterföljande sidintervall med data gånger 12 byte. Detta är antalet unika sidintervall som du ser när du anropar **GetPageRanges**-API:et.

  * Storleken på data i byte för alla lagrade sidor.

    >[!NOTE]
    >När ögonblicksbilder används omfattar den här storleken bara unika sidor för den grundläggande blob eller ögonblicksblob som räknas.

## <a name="sample-script"></a>Exempelskript

```powershell

# this script will show how to get the total size of the blobs in a container
# before running this, you need to create a storage account, create a container,
#    and upload some blobs into the container
# note: this retrieves all of the blobs in the container in one command.
#       connect Azure with Login-AzAccount -EnvironmentName AzureChinaCloud before you run the script.
#       requests sent as part of this tool will incur transactional costs. 
# command line usage: script.ps1 -ResourceGroup {YourResourceGroupName} -StorageAccountName {YourAccountName} -ContainerName {YourContainerName}
#


param(
    [Parameter(Mandatory=$true)]
    [string]$ResourceGroup,

    [Parameter(Mandatory=$true)]
    [string]$StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]$ContainerName
)

#Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
#Set-StrictMode -Version 2

$VerbosePreference = "Continue"

if((Get-Module -ListAvailable Az.Storage) -eq $null)
{
    throw "Azure Powershell not found! Please install from https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps"
}

# function Retry-OnRequest
function Retry-OnRequest
{
    param(
        [Parameter(Mandatory=$true)]
        $Action)
    
    # It could encounter various of temporary errors, like network errors, or storage server busy errors.
    # Should retry the request on transient errors

    # Retry on storage server timeout errors
    $clientTimeOut = New-TimeSpan -Minutes 15
    $retryPolicy = New-Object -TypeName Microsoft.WindowsAzure.Storage.RetryPolicies.ExponentialRetry -ArgumentList @($clientTimeOut, 10)        
    $requestOption = @{}
    $requestOption.RetryPolicy = $retryPolicy

    # Retry on temporary network errors
    $shouldRetryOnException = $false
    $maxRetryCountOnException = 3

    do
    {
        try
        {
            return $Action.Invoke($requestOption)
        }
        catch
        {
            if ($_.Exception.InnerException -ne $null -And $_.Exception.InnerException.GetType() -Eq [System.TimeoutException] -And $maxRetryCountOnException -gt 0)
            {
                $shouldRetryOnException = $true
                $maxRetryCountOnException--
            }
            else
            {
                $shouldRetryOnException = $false
                throw
            }
        }
    }
    while ($shouldRetryOnException)

}

# function Get-BlobBytes

function Get-BlobBytes
{
    param(
        [Parameter(Mandatory=$true)]
        $Blob,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + blobname
    $blobSizeInBytes = 124 + $Blob.Name.Length * 2

    # Get size of metadata
    $metadataEnumerator=$Blob.ICloudBlob.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $blobSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    if (!$IsPremiumAccount)
    {
        if($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::BlockBlob)
        {
            $blobSizeInBytes += 8
            # Default is Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter.Committed. Need All
            $action = { param($requestOption) return $Blob.ICloudBlob.DownloadBlockList([Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter]::All, $null, $requestOption) }                

            $blocks=Retry-OnRequest $action      

            if ($null -eq $blocks)
            {
                $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
            }
            else
            {
                $blocks | ForEach-Object { $blobSizeInBytes += $_.Length + $_.Name.Length }
            }  
        }
        elseif($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::PageBlob)
        {
            # It could cause server time out issue when trying to get page ranges of highly fragmented page blob 
            # Get page ranges in segment can mitigate chance of meeting such kind of server time out issue
            # See https://blogs.msdn.microsoft.com/windowsazurestorage/2012/03/26/getting-the-page-ranges-of-a-large-page-blob-in-segments/ for details.
            $pageRangesSegSize = 148 * 1024 * 1024L
            $totalSize = $Blob.ICloudBlob.Properties.Length
            $pageRangeSegOffset = 0
        
            $pageRangesTemp = New-Object System.Collections.ArrayList
        
            while ($pageRangeSegOffset -lt $totalSize)
            {
                $action = {param($requestOption) return $Blob.ICloudBlob.GetPageRanges($pageRangeSegOffset, $pageRangesSegSize, $null, $requestOption) }

                Retry-OnRequest $action | ForEach-Object { $pageRangesTemp.Add($_) }  | Out-Null
                $pageRangeSegOffset += $pageRangesSegSize
            }

            $pageRanges = New-Object System.Collections.ArrayList

            foreach ($pageRange in $pageRangesTemp)
            {
                if($lastRange -eq $Null)
                {
                    $lastRange = New-Object PageRange
                    $lastRange.StartOffset = $pageRange.StartOffset
                    $lastRange.EndOffset =  $pageRange.EndOffset
                }
                else
                {
                    if (($lastRange.EndOffset + 1) -eq $pageRange.StartOffset)
                    {
                        $lastRange.EndOffset = $pageRange.EndOffset
                    }
                    else
                    {
                        $pageRanges.Add($lastRange)  | Out-Null
                        $lastRange = New-Object PageRange
                        $lastRange.StartOffset = $pageRange.StartOffset
                        $lastRange.EndOffset =  $pageRange.EndOffset
                    }
                }
            }

            $pageRanges.Add($lastRange) | Out-Null
            $pageRanges |  ForEach-Object { 
                    $blobSizeInBytes += 12 + $_.EndOffset - $_.StartOffset 
                }
        }
        else
        {
            $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
        }
        return $blobSizeInBytes
    }
    else
    {
        $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
    }
    return $blobSizeInBytes
}

# function Get-ContainerBytes

function Get-ContainerBytes
{
    param(
        [Parameter(Mandatory=$true)]
        [Microsoft.WindowsAzure.Storage.Blob.CloudBlobContainer]$Container,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + name of container
    $containerSizeInBytes = 48 + $Container.Name.Length*2

    # Get size of metadata
    $metadataEnumerator = $Container.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $containerSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    # Get size for SharedAccessPolicies
    $containerSizeInBytes += $Container.GetPermissions().SharedAccessPolicies.Count * 512

    # Calculate size of all blobs.
    $blobCount = 0
    $Token = $Null
    $MaxReturn = 5000

    do {
        $Blobs = Get-AzStorageBlob -Context $storageContext -Container $Container.Name -MaxCount $MaxReturn -ContinuationToken $Token
        if($Blobs -eq $Null) { break }

        #Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
        if($Blobs.GetType().Name -eq "AzureStorageBlob")
        {
            $Token = $Null
        }
        else
        {
            $Token = $Blobs[$Blobs.Count - 1].ContinuationToken;
        }

        $Blobs | ForEach-Object {
                $blobSize = Get-BlobBytes $_ $IsPremiumAccount
                $containerSizeInBytes += $blobSize
                $blobCount++

                if(($blobCount % 1000) -eq 0)
                {
                    Write-Verbose("Counting {0} Sizing {1} " -f $blobCount, $containerSizeInBytes)
                }
            }
    }
    While ($Token -ne $Null)

    return @{ "containerSize" = $containerSizeInBytes; "blobCount" = $blobCount }
}

#Login-AzAccount -EnvironmentName AzureChinaCloud

$storageAccount = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -ErrorAction SilentlyContinue
if($storageAccount -eq $null)
{
    throw "The storage account specified does not exist in this subscription."
}

$storageContext = $storageAccount.Context

if (-not ([System.Management.Automation.PSTypeName]'PageRange').Type)
{
    $Source = "
        public class PageRange
        {
            public long StartOffset;
            public long EndOffset;
        }"
    Add-Type -TypeDefinition $Source
}

$containers = New-Object System.Collections.ArrayList
if($ContainerName.Length -ne 0)
{
    $container = Get-AzStorageContainer -Context $storageContext -Name $ContainerName -ErrorAction SilentlyContinue |
        ForEach-Object { $containers.Add($_) } | Out-Null
}
else
{
    Get-AzStorageContainer -Context $storageContext | ForEach-Object { $containers.Add($_) } | Out-Null
}

$sizeInBytes = 0
$IsPremiumAccount = ($storageAccount.Sku.Tier -eq "Premium")

if($containers.Count -gt 0)
{
    $containers | ForEach-Object {
        Write-Output("Calculating container {0} ..." -f $_.CloudBlobContainer.Name)
        $result = Get-ContainerBytes $_.CloudBlobContainer $IsPremiumAccount
        $sizeInBytes += $result.containerSize

        Write-Output("Container '{0}' with {1} blobs has a sizeof {2:F2} MB." -f $_.CloudBlobContainer.Name,$result.blobCount,($result.containerSize/1MB))
    }
}
else
{
    Write-Warning "No containers found to process in storage account '$StorageAccountName'."
}
```

## <a name="next-steps"></a>Nästa steg

- Ett enkelt skript som ger en beräkning av containerstorleken finns i [Beräkna den totala storleken på en Blob Storage-container](../scripts/storage-blobs-container-calculate-size-powershell.md).

- Mer information om Azure Storage-fakturering finns i [Förstå Windows Azure Storage-fakturering](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

- Ytterligare Storage PowerShell-skriptexempel finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
