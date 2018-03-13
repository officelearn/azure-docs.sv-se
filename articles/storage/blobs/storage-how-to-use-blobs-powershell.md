---
title: "Utföra åtgärder på Azure Blob storage (objektlagring) med PowerShell | Microsoft Docs"
description: "Kursen - utföra åtgärder på Azure Blob storage (objektlagring) med PowerShell"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 09/14/2017
ms.author: tamram
ms.openlocfilehash: 8b87ecc3fefe5338269a6ad584e9a097ff3fb633
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Utföra Azure Blob storage-åtgärder med Azure PowerShell

Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerad objektdata, exempelvis text eller binär data som kan nås från var som helst i världen via HTTP eller HTTPS. Den här artikeln beskriver grunderna i Azure Blob storage, till exempel ladda upp, hämta och ta bort blobbar. Lär dig att:

> [!div class="checklist"]
> * Skapa en behållare 
> * Överför blobbar
> * Visa en lista över blobbarna i en behållare 
> * Ladda ned blobbar
> * Kopiera BLOB
> * Ta bort blobbar
> * Visa och ange en blob metadata och egenskaper
> * Hantera säkerhet med signaturer för delad åtkomst

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Skapa en behållare

Blobar laddas alltid upp till en behållare. Behållare liknar kataloger på datorn så att du kan ordna grupper med blobbar i behållare som du ordna dina filer i mappar på din dator. Ett lagringskonto kan ha valfritt antal behållare. begränsas bara av mängden utrymme som tas upp i storage-konto (upp till 500TB). 

När du skapar en behållare kan ange du åtkomstnivå, vilket bidrar till att definiera vem som kan komma åt blobbar i behållaren. De kan till exempel vara privat (åtkomstnivå = `Off`), vilket innebär att ingen kommer åt dem utan en signatur för delad åtkomst eller åtkomstnycklar för lagringskontot. Om du inte anger åtkomstnivån när du skapar behållaren, standard privata.

Du kanske vill bilder i dina behållaren ska vara offentligt tillgängliga. Till exempel om du vill lagra avbildningar som ska visas på webbplatsen vill du att de ska vara offentlig. I så fall måste du ange behållare till `blob`, och vem som helst med en URL som pekar på en blobb i behållaren har åtkomst till blob.

Ange behållarnamnet på för att skapa behållaren, och sedan skapa behållaren måste ange behörigheter till ”blob”. Behållarnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och bindestreck (-). Flera regler för namngivning av blobbar och behållare finns [namnge och referera till behållare, Blobbar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en ny behållare med [ny AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Ange åtkomstnivå offentlig. Behållarens namn i det här exemplet är *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Ladda upp blobbar i en behållare

Azure Blob Storage stöder blockblobbar, tilläggsblobbar och sidblobbar.  VHD-filer som används för att backa upp virtuella datorer i IaaS är sidblobar. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta filer som lagras i Blob Storage är blockblobar. 

Om du vill ladda upp en fil till en blockblob ska du hämta en referens för behållaren och sedan hämta en referens för blockbloben i den behållaren. När du har en blobreferensen kan du ladda upp data till den med hjälp av [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Den här åtgärden skapas blobben om den finns inte eller skriver över den om den redan finns.

Nedan visas hur du laddar upp en blobb till en behållare. Innan du kan definiera variabler som pekar på katalogen på den lokala datorn där filerna finns, och ange en variabel för namnet på den fil som ska överföras. Detta är användbart när du vill utföra samma åtgärd flera gånger. Ladda upp ett antal filer så att du kan se flera poster när du visar en lista över blobbar i behållaren.

I följande exempel läsa in bild001.jpg och Image002.png från D:\\_TestImages på den lokala disken till behållaren som du nyss skapade.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Ladda upp en annan fil. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Ladda upp så många filer som du vill innan du fortsätter.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Hämta en lista över blobbar i behållaren med [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) och välja blobbnamnet som ska visas.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobarna till den lokala disken. Innan du kan definiera en variabel som refererar till den lokala mappen som du vill hämta blobbarna. Ange namn och anrop för varje blob hämtas [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) att hämta blobben.

Det här exemplet kopierar blobar till D:\\_TestImages\Downloads på den lokala disken. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Kopiera BLOB

Det finns flera saker att tänka på när du kopierar blobar. Du kan bara kopiera blob till ett nytt namn på samma plats. Du kan kopiera blobben till ett separat lagringskonto. Du kan kopiera en stor blob (till exempel en VHD-fil) till ett annat lagringskonto. Dessa görs varje på olika sätt.

### <a name="simple-blob-copy"></a>Enkel blob-kopia

Du kan göra en kopia av en blobbarna i en behållare genom att kopiera den till en ny blob. Det här exemplet kopierar den till samma behållare med ett annat namn, men du kan lika enkelt skapa en annan behållare och kopiera den det i stället. Det här exemplet visar hur du använder [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) att kopiera blob. Du måste ange både källa och mål-blob-namnet och behållarnamn.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Kopiera en blobb till ett annat lagringskonto 

Du kanske vill kopiera en blobb till en separat lagringskonto. Ett exempel för att göra detta är att kopiera en VHD-fil som säkerhetskopierar en av dina virtuella datorer till ett annat lagringskonto för att säkerhetskopiera den. 

Skapa en andra lagringskonto, hämta kontexten, ställa in en behållare i det storage-kontot och utföra kopieringen. Den här delen av skriptet är nästan identisk med skriptet ovan utom med andra storage-konto i stället för först.

```powershell
#create new storage account, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Kopiera mycket stora blobbar asynkront

Om du kopierar en mycket stor blob som är flera GB, kan du dra nytta av asynkrona kopian genom att starta den, och sedan komma tillbaka och kontrollerar statusen tills den är klar. På så sätt kan du inte kopplas när den utför kopieringen.

Om du kopierar inom ett lagringskonto är kopian mycket snabbt. Om du kopierar mellan två storage-konton i samma region är kopian ganska snabbt. Om käll- och målservrar finns i olika områden, kan det ta flera timmar att slutföra kopia, beroende på avståndet och storleken på din fil. 

Det här skriptet använder samma variabler som definieras i det sista exemplet kopia. Skillnaden är att den här samlar in statusen för kopian och frågar den upprepade gånger varje 10 sekunder tills den är klar. Du kan behöva ladda upp en mycket stor blob storage-konto kan se den här ta mer än en iteration ska slutföras.

Använd [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) att fråga status för kopian. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Ta bort blobbar

Ta bort blobbar från ett lagringskonto med [ta bort AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Läsa och skriva en blob-egenskaper och metadata

Åtkomst till en uppsättning egenskaper och metoder för en returnerad **IListBlobItem**, du måste konvertera den (eller konvertera den) till en **CloudBlockBlob**, **CloudPageBlob**, eller **CloudBlobDirectory** objekt. Om typen är okänd kan du använda en typkontroll för att avgöra till vilket objekt den ska skickas. Följande kod visar hur du hämtar och returnerar egenskaperna för en blob som överförts tidigare med hjälp av [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) och kastar resultatet till ett CloudBlockBlob-objekt.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Fylla Systemegenskaper genom att anropa FetchAttributes och sedan visa egenskaperna. Vissa egenskaper är skrivbara och du kan ändra deras värden. Det här exemplet visar hur du ändrar innehållstypen, även kallat MIME-typen.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Varje blobb har sin egen metadata som du kan ange. Du kan till exempel lagra namnet på den användare som har överförts blob eller tidsvärdet överfördes först. Metadata består av nyckel/värde-par. Detta kan ändras med hjälp av PowerShell.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Hantera säkerheten för blobbar 

Standard skydda Azure Storage dina data genom att begränsa åtkomst till kontoägaren, som innehar åtkomstnycklar för lagring konto. När du behöver dela blobbdata i ditt lagringskonto är det viktigt att du gör det utan att äventyra åtkomstnycklarnas säkerhet. För att göra detta, kan du använda en delad åtkomst signatur-URL, vilket är en URL till den tillgång som innehåller frågeparametrar och en säkerhetstoken som gör att en viss nivå av behörigheten för en viss tidsperiod. Du kanske vill ge läsbehörighet till en privat blob för 5 minuter så att någon kan visa den. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Ange åtkomstnivån för behållaren och dess blobbar som privat

Ange först åtkomstnivån för behållaren `Off`, vilket betyder att det finns ingen åtkomst utan en signatur för delad åtkomst eller nyckeln för kontot. Använd den [Set AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Testa privat åtkomst

Kontrollera att du har ingen åtkomst till blobbar i behållaren, skapa URL-Adressen till en blobar utan en signatur för delad åtkomst och försök att visa blob. Med hjälp av HTTPS-protokollet, blir Webbadressen i följande format:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Här visas hur du skapar blob-URL.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Kopiera blob-URL och klistra in den i ett privat webbläsarfönster – visas ett behörighetsfel eftersom blob är privata och du har inte inkluderat en signatur för delad åtkomst. 

### <a name="create-the-sas-uri"></a>Skapa SAS-URI

Skapa ett SAS-URI - detta är en länk till blob inklusive frågeparametrar och säkerhetstoken som utgör SAS. Klistra in den här URI i ett privat webbläsarfönster--bilden visas. 

Skapa först startdatum /-tid och förfallodatum för datum/tid för åtkomst. Den använder ett fönster med 2 minuter. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Skapa SAS-URI med [ny AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). Du behöver behållarens namn, blob-namnet, kontexten för lagringskontot, behörigheter beviljas (i det här fallet, läsa, skriva och ta bort) och start- och tiden för åtkomst. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Kopiera SAS-URI och placera den i ett privat webbläsarfönster; bilden visas.

Vänta tillräckligt länge för att URL som ska gälla (2 minuter i det här exemplet) och sedan klistra in Webbadressen till en annan privat webbläsarfönster. Den här gången du får ett felmeddelande för auktorisering och det visas inte bilden, eftersom SAS-URI har upphört att gälla.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat. Om du vill göra detta måste du ta bort resursgruppen, som tar också bort alla resurser som ingår i gruppen. I det här fallet den tar bort alla lagringskonton och resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om grundläggande Blob storage management, till exempel hur du:

> [!div class="checklist"]
> * Skapa en behållare 
> * Överför blobbar
> * Visa en lista över blobbarna i en behållare 
> * Ladda ned blobbar
> * Kopiera BLOB
> * Ta bort blobbar
> * Läsa och skriva en blob metadata och egenskaper
> * Hantera säkerhet med signaturer för delad åtkomst

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell lagrings-cmdletar
* [Storage PowerShell cmdletar](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
