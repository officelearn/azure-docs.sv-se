---
title: Vidarebefordra Azure Blob storage-händelser till en anpassad webbplats slutpunkt - Powershell | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 6d7ccd94243d7064008197518f6194d5837b17be
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Vidarebefordra Blob storage-händelser till en anpassad webbplats slutpunkt med PowerShell

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder Azure PowerShell för att prenumerera på Blob storage-händelser, utlösa en händelse och visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. För att förenkla exemplet som visas i den här artikeln, skickas händelser till en URL som endast samlar in meddelanden. Du skapar den här URL:en med ett tredjepartsverktyg från [Hookbin](https://hookbin.com/).

> [!NOTE]
> **Hookbin** är inte avsedd för användning med hög genomströmning. Här används verktyget endast i instruktionssyfte. Om du push-överför fler än en händelse i taget kanske du inte ser alla händelser i verktyget.

När du slutför stegen som beskrivs i den här artikeln ser du att händelsedata har skickats till en slutpunkt.

## <a name="setup"></a>Konfiguration

Den här artikeln kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Tillgänglighet för Storage-händelser som är knutna till händelse rutnätet [tillgänglighet](../../event-grid/overview.md) och ska vara tillgänglig i andra regioner som händelsen rutnätet.

Det här exemplet används **westus2** och lagrar markeringen i en variabel för hela.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

I följande exempel skapas en resursgrupp med namnet **gridResourceGroup** på platsen **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Om du vill använda Blob storage-händelser, måste du antingen en [Blob-lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) eller en [Allmänt v2 lagringskonto](../common/storage-account-options.md#general-purpose-v2). **Allmänna syften v2 (GPv2)** är lagringskonton som stöder alla funktioner för alla lagringstjänster, inklusive Blobbar, filer, köer och tabeller. En **Blob-lagringskonto** är ett specialiserat lagringskonto för att lagra Ostrukturerade data som blobbar (objekt) i Azure Storage. BLOB storage-konton fungerar som allmänna lagringskonton och dela alla bra hållbarhet, tillgänglighet, skalbarhet och prestanda funktioner du använder idag, inklusive 100 procent konsekvent API för blockblobar och tilläggsblobar. För program som bara behöver lagring av block- eller tilläggsblobbar, rekommenderar vi att du använder Blob-lagringskonton.  

Skapa ett Blob storage-konto med LRS replikering [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), hämta kontexten för lagringskontot som definierar lagringskontot som ska användas. När fungerar på ett lagringskonto, referera kontexten i stället för att tillhandahålla autentiseringsuppgifterna flera gånger. Det här exemplet skapar ett lagringskonto som kallas **gridstorage** med lokalt redundant lagring (LRS). 

> [!NOTE]
> Namn på lagringskonton finns i ett globalt namnområde så du behöver lägga till vissa slumpmässiga tecken i namnet i skriptet.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. I stället för att skriva kod för att svar på händelsen ska vi skapa en slutpunkt som samlar in meddelandena så att du kan visa dem. Hookbin är ett tredjepartsverktyg som låter dig skapa en slutpunkt och visa förfrågningar som skickas till den. Gå till [Hookbin](https://hookbin.com/) och klicka på **Skapa ny slutpunkt**. Kopiera URL som bin och Ersätt `<bin URL>` i skriptet nedan.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Prenumerera på ditt lagringskonto

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. I följande exempel prenumererar på storage-konto som du har skapat och överför URL: en från Hookbin som slutpunkt för händelseavisering. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Utlösa en händelse från Blob Storage

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först ska vi skapa en behållare och ett objekt. Sedan vi överföra objektet till behållaren.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Bläddra till slutpunktsadress du skapade tidigare. Du kan också klicka på Uppdatera i webbläsaren du har öppen. Du kan se den händelse du just skickade. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med det här lagringskontot och händelseprenumerationen ska du inte rensa upp bland de resurser som skapades i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om Blob Storage-händelser och vad Event Grid kan hjälpa dig med:

- [Reagera på Blob Storage-händelser](storage-blob-event-overview.md)
- [Om Event Grid](../../event-grid/overview.md)
