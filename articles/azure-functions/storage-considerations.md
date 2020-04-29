---
title: Lagrings överväganden för Azure Functions
description: Läs mer om lagrings kraven för Azure Functions och om kryptering av lagrade data.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984171"
---
# <a name="storage-considerations-for-azure-functions"></a>Lagrings överväganden för Azure Functions

Azure Functions kräver ett Azure Storage-konto när du skapar en Function App-instans. Följande lagrings tjänster kan användas av din Function-app:


|Lagrings tjänst  | Funktions användning  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Bevara bindnings tillstånd och funktions nycklar.  <br/>Används också av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Fil resurs som används för att lagra och köra din Function app-kod i en [förbruknings plan](functions-scale.md#consumption-plan). |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Används av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Används av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Detta beror på att funktioner förlitar sig på Azure Storage för åtgärder som att hantera utlösare och loggning av funktions körningar. Vissa lagrings konton stöder inte köer och tabeller. Dessa konton inkluderar enbart BLOB-lagrings konton, Azure Premium Storage och allmänna lagrings konton med ZRS-replikering. Dessa konton som inte stöds filtreras bort från bladet lagrings konto när du skapar en Function-app.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services). 

Även om du kan använda ett befintligt lagrings konto med din Function-app måste du kontrol lera att det uppfyller dessa krav. Lagrings konton som skapats som en del av funktionen Skapa flöde i Function-appen garanterar att de uppfyller dessa krav för lagrings kontot.  

## <a name="storage-account-guidance"></a>Vägledning för lagrings konto

Alla Functions-appar kräver ett lagrings konto för att fungera. Om kontot har tagits bort körs inte funktions programmet. Information om hur du felsöker problem med lagring finns i [Felsöka problem som rör lagring](functions-recover-storage-account.md). Följande ytterligare överväganden gäller för det lagrings konto som används av Function-appar.

### <a name="storage-account-connection-setting"></a>Anslutnings inställning för lagrings konto

Lagrings konto anslutningen upprätthålls i [program inställningen AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Anslutnings strängen för lagrings kontot måste uppdateras när du återskapar lagrings nycklar. [Läs mer om hantering av lagrings nycklar här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Delade lagrings konton

Det är möjligt att flera Function-appar delar samma lagrings konto utan problem. I Visual Studio kan du till exempel utveckla flera appar med hjälp av Azure Storage emulatorn. I det här fallet fungerar emulatorn som ett enda lagrings konto. Samma lagrings konto som används av din Function-app kan också användas för att lagra program data. Den här metoden är dock inte alltid en bra idé i en produktions miljö.

### <a name="optimize-storage-performance"></a>Optimera lagringsprestanda

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Kryptering av lagrings data

Azure Storage krypterar alla data i ett lagrings konto i vila. Mer information finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).

Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha mer kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för kryptering av BLOB-och fildata. Dessa nycklar måste finnas i Azure Key Vault för att funktioner ska kunna komma åt lagrings kontot. Mer information finns i [Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure Portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Montera fil resurser (Linux)

Du kan montera befintliga Azure Files resurser i dina Linux Function-appar. Genom att montera en resurs till din Linux Function-app kan du utnyttja befintliga maskin inlärnings modeller eller andra data i dina funktioner. Du kan använda [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) kommandot för att montera en befintlig resurs till din Linux Function-app. 

I det här kommandot `share-name` är namnet på den befintliga Azure Files resursen och `custom-id` kan vara en sträng som unikt definierar resursen när den monteras i Function-appen. `mount-path` Är också sökvägen från vilken resursen nås i din Function-app. `mount-path`måste vara i formatet `/dir-name`och får inte börja med. `/home`

Ett fullständigt exempel finns i skripten i [skapa en python Function-app och montera en Azure Files-resurs](scripts/functions-cli-mount-files-storage-linux.md). 

För närvarande stöds endast `storage-type` en `AzureFiles` av. Du kan bara montera fem resurser till en specifik Function-app. Genom att montera en fil resurs kan du öka den kall start tiden med minst 200-300ms, eller till och med mer om lagrings kontot finns i en annan region.

Den monterade resursen är tillgänglig för funktions koden i den `mount-path` angivna. Om `mount-path` till exempel är `/path/to/mount`, kan du komma åt mål katalogen med API: er för fil system, som i följande python-exempel:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions värd alternativ.

> [!div class="nextstepaction"]
> [Skala och var värd i Azure Functions](functions-scale.md)


