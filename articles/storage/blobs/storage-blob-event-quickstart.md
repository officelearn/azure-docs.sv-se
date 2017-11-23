---
title: "Dirigera Azure Blob Storage-händelser till en anpassad webbslutpunkt (förhandsversion)| Microsoft Docs"
description: "Använd Azure Event Grid för att prenumerera på Blob Storage-händelser."
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: 67f262913333fb69f5b862fa3d862c0d773e4172
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Dirigera Blob Storage-händelser till en anpassad webbslutpunkt (förhandsversion)

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure CLI för att prenumerera på Blob Storage-händelser och utlösa händelsen för att visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. För att enkelt beskriva exemplen i den här artikeln kan du skicka händelser till en URL som endast samlar in meddelanden. Du skapar denna URL med hjälp av en öppen källkod, ett tredjepartsverktyg som kallas [RequestBin](https://requestb.in/).

> [!NOTE]
> **RequestBin** är ett verktyg med öppen källkod som inte är avsett för användning med högt dataflöde. Här används verktyget endast i demonstrativt syfte. Om du push-överför mer än en händelse i taget kanske du inte ser alla händelser i verktyget.

När du slutför stegen som beskrivs i den här artikeln ser du att händelsedata har skickats till en slutpunkt.

![Händelsedata](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra den senaste versionen av Azure CLI (2.0.14 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

Om du inte använder Cloud Shell måste du först logga in med `az login`.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). 

I följande exempel skapas en resursgrupp med namnet `<resource_group_name>` på platsen *westcentralus*.  Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Skapa ett Blob Storage-konto

Om du vill använda Azure Storage behöver du ett lagringskonto.  Blob Storage-händelser är i nuläget endast tillgängliga i Blob Storage-konton.

Ett Blob-lagringskonto är ett specialiserat lagringskonto för lagring av ostrukturerad data som blobbar (objekt) i Azure Storage. Blob-lagringskonton liknar dina befintliga allmänna lagringskonton och har samma höga hållbarhet, tillgänglighet, skalbarhet och prestanda som du använder idag, inklusive 100 % API-konsekvens för blockblobbar och tilläggsblobbar. För program som bara behöver lagring av block- eller tilläggsblobbar, rekommenderar vi att du använder Blob-lagringskonton.

> [!NOTE]
> Händelsen rutnätet är för närvarande under förhandsgranskning, och kan bara storage-konton i den **westcentralus** och **westus2** regioner.

Ersätt `<storage_account_name>` med ett unikt namn på ditt lagringskonto och `<resource_group_name>` med resursgruppen du skapade tidigare.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelserna från Blob Storage-kontot ska vi skapa slutpunkten för händelsemeddelandet. I stället för att skriva kod för att svar på händelsen ska vi skapa en slutpunkt som samlar in meddelandena så att du kan visa dem. RequestBin är en öppen källkod, ett tredjepartsverktyg som låter dig skapa en slutpunkt och visa begäran som skickas till den. Gå till [RequestBin](https://requestb.in/) och klicka på **Create a RequestBin** (skapa en lagerplats).  Kopiera lagerplatsens URL eftersom du behöver den för att prenumerera på ämnet.

## <a name="subscribe-to-your-blob-storage-account"></a>Prenumerera på Blob Storage-kontot

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. I följande exempel prenumererar vi på det Blog Storage-konto du just skapat, och URL från RequestBin skickas som slutpunkt för händelseavisering. Ersätt `<event_subscription_name>` med ett unikt namn för din händelseprenumeration och `<URL_from_RequestBin>` med värdet från föregående avsnitt. Genom att ange en slutpunkt när du prenumererar kan Event Grid hantera omdirigeringen av händelser till denna slutpunkt. För `<resource_group_name>` och `<storage_account_name>` använder du det värde du skapade tidigare. 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
```

## <a name="trigger-an-event-from-blob-storage"></a>Utlösa en händelse från Blob Storage

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först ska vi konfigurera namn och nyckel för storage-konto och sedan vi skapa en behållare och sedan skapa och ladda upp en fil. För `<storage_account_name>` och `<resource_group_name>` använder du de värden du skapade tidigare.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Bläddra till den RequestBin-URL du skapade tidigare. Eller klicka på Uppdatera i den RequestBin-webbläsaren du har öppen. Du kan se den händelse du just skickade. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  }
}]

```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med det här lagringskontot och händelseprenumerationen ska du inte rensa upp bland de resurser som skapades i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

Ersätt `<resource_group_name>` med resursgruppen du skapade ovan.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om Blob Storage-händelser och vad Event Grid kan hjälpa dig med:

- [Reagera på Blob Storage-händelser](storage-blob-event-overview.md)
- [Om Event Grid](../../event-grid/overview.md)
