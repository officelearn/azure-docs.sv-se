---
title: Lagringsöverväganden för Azure-funktioner
description: Lär dig mer om lagringskraven för Azure Functions och om kryptering av lagrade data.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984171"
---
# <a name="storage-considerations-for-azure-functions"></a>Lagringsöverväganden för Azure-funktioner

Azure Functions kräver ett Azure Storage-konto när du skapar en funktionsappinstans. Följande lagringstjänster kan användas av din funktionsapp:


|Lagringstjänst  | Användning av funktioner  |
|---------|---------|
| [Azure Blob-lagring](../storage/blobs/storage-blobs-introduction.md)     | Underhåll bindningar tillstånd och funktionstangenter.  <br/>Används även av [aktivitetshubbar i varaktiga funktioner](durable/durable-functions-task-hubs.md). |
| [Azure-filer](../storage/files/storage-files-introduction.md)  | Filresurs som används för att lagra och köra funktionsappkoden i en [förbrukningsplan](functions-scale.md#consumption-plan). |
| [Lagring av Azure-kö](../storage/queues/storage-queues-introduction.md)     | Används av [aktivitetshubbar i varaktiga funktioner](durable/durable-functions-task-hubs.md).   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Används av [aktivitetshubbar i varaktiga funktioner](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en funktionsapp måste du skapa eller länka till ett Azure Storage-konto med allmänt syfte som stöder Blob-, kö- och Tabelllagring. Detta beror på att funktioner är beroende av Azure Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller. Dessa konton inkluderar lagringskonton med endast blob, Azure Premium Storage och lagringskonton för allmänt ändamål med ZRS-replikering. Dessa konton som inte stöds filtreras bort från bladet Lagringskonto när du skapar en funktionsapp.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services). 

Även om du kan använda ett befintligt lagringskonto med din funktionsapp måste du se till att det uppfyller dessa krav. Lagringskonton som skapas som en del av funktionsappens skapa flöde garanteras uppfylla dessa lagringskontokrav.  

## <a name="storage-account-guidance"></a>Vägledning för lagringskonto

Varje funktionsapp kräver ett lagringskonto för att fungera. Om det kontot tas bort körs inte funktionsappen. Information om felsökning av lagringsrelaterade problem finns i [Felsöka lagringsrelaterade problem](functions-recover-storage-account.md). Följande ytterligare överväganden gäller för lagringskontot som används av funktionsappar.

### <a name="storage-account-connection-setting"></a>Anslutningsinställning för lagringskonto

Lagringskontoanslutningen underhålls i [programinställningen AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Anslutningssträngen för lagringskontot måste uppdateras när du återskapar lagringsnycklar. [Läs mer om hantering av lagringsnyckel här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Konton för delad lagring

Det är möjligt för flera funktionsappar att dela samma lagringskonto utan problem. I Visual Studio kan du till exempel utveckla flera appar med Azure Storage Emulator. I det här fallet fungerar emulatorn som ett enda lagringskonto. Samma lagringskonto som används av funktionsappen kan också användas för att lagra dina programdata. Den här metoden är dock inte alltid en bra idé i en produktionsmiljö.

### <a name="optimize-storage-performance"></a>Optimera lagringsprestanda

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Kryptering av lagringsdata

Azure Storage krypterar alla data i ett lagringskonto i vila. Mer information finns i [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md).

Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kundhanterade nycklar som ska användas för kryptering av blob- och fildata. Dessa nycklar måste finnas i Azure Key Vault för funktioner för att kunna komma åt lagringskontot. Mer information finns i [Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure-portalen](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Mount filresurser (Linux)

Du kan montera befintliga Azure Files-resurser i dina Linux-funktionsappar. Genom att montera en resurs i din Linux-funktionsapp kan du utnyttja befintliga maskininlärningsmodeller eller andra data i dina funktioner. Du kan [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) använda kommandot för att montera en befintlig resurs i din Linux-funktionsapp. 

I det `share-name` här kommandot är namnet på den `custom-id` befintliga Azure Files-resursen och kan vara vilken sträng som helst som unikt definierar resursen när den är monterad till funktionsappen. Dessutom `mount-path` är sökvägen som resursen nås från i din funktionsapp. `mount-path`måste vara i `/dir-name`formatet och det kan `/home`inte börja med .

Ett fullständigt exempel finns i skripten i [Skapa en Python-funktionsapp och montera en Azure Files-resurs](scripts/functions-cli-mount-files-storage-linux.md). 

För närvarande `storage-type` stöds `AzureFiles` endast en av. Du kan bara montera fem resurser i en viss funktionsapp. Om du monterar en filresurs kan det öka kallstarttiden med minst 200-300 ms, eller ännu mer när lagringskontot finns i en annan region.

Den monterade resursen är tillgänglig för `mount-path` din funktionskod vid angiven. När `mount-path` är `/path/to/mount`till exempel kan du komma åt målkatalogen efter filsystem-API:er, som i följande Python-exempel:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions hosting-alternativ.

> [!div class="nextstepaction"]
> [Skala och var värd i Azure Functions](functions-scale.md)


