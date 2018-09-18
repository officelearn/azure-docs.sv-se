---
title: Dirigera Azure Blob storage-händelser till en anpassad webbslutpunkt – Powershell | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
services: storage,event-grid
author: david-stanford
ms.author: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.component: blobs
ms.openlocfilehash: 8482678a9c42fa2d960dee54c9810593cd820553
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732013"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Dirigera Blob storage-händelser till en anpassad webbslutpunkt med PowerShell

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder Azure PowerShell för att prenumerera på Blob storage-händelser, utlösa en händelse och visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en webbapp som samlar in och visar meddelanden.

När du är klar kan se du att händelsedata som har skickats till webbappen.

![Visa resultat](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Konfiguration

Den här artikeln kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzureRmAccount
```

Det här exemplet används **westus2** och lagrar markeringen i en variabel som ska användas i hela.

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

BLOB storage-händelser är tillgängliga i storage-konton för generell användning v2 och Blob storage-konton. **Generell användning v2** storage-konton stöder alla funktioner för alla lagringstjänster, inklusive Blobar, filer, köer och tabeller. Ett **Blob Storage-konto** är ett specialiserat lagringskonto för lagring av ostrukturerade data som blobbar (objekt) i Azure Storage. Blob Storage-konton liknar allmänna lagringskonton och har samma höga hållbarhet, tillgänglighet, skalbarhet och prestanda som du använder idag, inklusive 100 % API-konsekvens för blockblobbar och tilläggsblobbar. Mer information finns i [översikt över Azure storage-konton](../common/storage-account-overview.md).

Skapa ett Blob storage-konto med LRS replikering med hjälp av [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), hämta lagringskontokontexten som definierar lagringskontot som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger. Det här exemplet skapar ett lagringskonto med namnet **gridstorage** med lokalt redundant lagring (LRS). 

> [!NOTE]
> Lagringskontonamn är i en global namnrymd så du måste lägga till vissa slumpmässiga tecken till det namn som i det här skriptet.

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

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Prenumerera på ditt lagringskonto

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. I följande exempel ska vi prenumerera på det lagringskonto du nyss skapat och skicka URL:en från din webbapp som slutpunkten för händelseavisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

![Visa prenumerationshändelse](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Utlösa en händelse från Blob Storage

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först måste vi skapa en behållare och ett objekt. Sedan ska vi överföra objektet till behållaren.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Visa din webbapp om du vill se händelsen som du har skickat.

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
