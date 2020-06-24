---
title: Lagringsreferens för Azure-kö
description: Hitta Azure Storage API-referens, README-filer och klient biblioteks paket.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/02/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: fbf20b92edf5d8dce8438731c2812a78e457a402
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976667"
---
# <a name="azure-queue-storage-reference"></a>Lagringsreferens för Azure-kö

Hitta Azure Queue Storage API-referens, biblioteks paket, viktigt-filer och komma igång-artiklar.

## <a name="net-client-libraries"></a>.NET-klient bibliotek

I följande tabell visas referens och exempel dokumentation för Azure Queue Storage .NET-API: er.

|  Version  | Referens dokumentation | Paket | Snabbstart |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Namn område för Azure. Storage. köer](/dotnet/api/azure.storage.queues) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/) | [Snabb start: Azure Queue Storage klient bibliotek V12 för .NET](/azure/storage/queues/storage-quickstart-queues-dotnet) |
| 11. x | [Microsoft. Azure. Storage. Queue-namnrymd](/dotnet/api/microsoft.azure.storage.queue) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) | [Snabb start: Använd Azure Storage SDK-V11 för .NET för att hantera en kö](/azure/storage/queues/storage-quickstart-queues-dotnet-legacy) |

### <a name="storage-management"></a>Lagrings hantering

I följande tabell visas referens dokumentation för Azure Storage Management .NET-API: er.

|  Version  | Referens dokumentation | Paket |
| :-------: | ----------------------- | ------- |
| 16. x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>Dataförflyttning

I följande tabell visas referens dokumentation för Azure Storage data förflyttning av .NET API: er.

|  Version  | Referens dokumentation | Paket |
| :-------: | ----------------------- | ------- |
| 1.x | [Data förflyttning](/dotnet/api/microsoft.azure.storage.datamovement) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>Java-klient bibliotek

I följande tabell visas referens och exempel dokumentation för Azure Queue Storage Java-API: er.

|  Version  | Referens dokumentation | Paket | Snabbstart |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Klient bibliotek för Azure Storage kö för Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [Paket (maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [Snabb start: Azure Queue Storage klient bibliotek V12 för Java](/azure/storage/queues/storage-quickstart-queues-java) |
| 8.x | [com. Microsoft. Azure. Storage. Queue](/java/api/com.microsoft.azure.storage.queue) | [Paket (maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) | [Snabb start: hantera blobbar med Java V8 SDK](/azure/storage/blobs/storage-quickstart-blobs-java-legacy) |

### <a name="storage-management"></a>Lagrings hantering

I följande tabell visas referens dokumentation för Azure Storage Management Java-API: er.

|  Version  | Referens dokumentation | Paket |
| :-------: | ----------------------- | ------- |
| 0,9. x | [com. Microsoft. Azure. Management. Storage](/java/api/overview/azure/storage/management) | [Paket (maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) |

## <a name="python-client-libraries"></a>Python-klient bibliotek

I följande tabell visas referens och exempel dokumentation för python-API: er för Azure Queue Storage.

|  Version  | Referens dokumentation | Paket | Snabbstart |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Azure Storage klient bibliotek V12 för python](/azure/developer/python/sdk/storage/overview?view=storage-py-v12) | [Paket (PyPI)](https://pypi.org/project/azure-storage-queue/) | [Snabb start: Azure Queue Storage klient bibliotek V12 för python](/azure/storage/queues/storage-quickstart-queues-python) |
| 2x | [Azure Storage klient bibliotek v2 för python](/azure/developer/python/sdk/storage/overview?view=storage-py-v2) | [Paket (PyPI)](https://pypi.org/project/azure-storage-queue/2.1.0/) | [Så här använder du Azure Queue Storage v 2.1 från python](/azure/storage/queues/storage-python-how-to-use-queue-storage) |

## <a name="javascript-client-libraries"></a>Java Script-klient bibliotek

I följande tabell visas referens och exempel dokumentation för JavaScript-API: er för Azure Queue Storage.

|  Version  | Referens dokumentation | Paket | Snabbstart |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Klient bibliotek för Azure Storage kö för Java Script](/javascript/api/overview/azure/storage-queue-readme) | [Paket (NPM)](https://www.npmjs.com/package/@azure/storage-queue) | [Snabb start: Azure Queue Storage klient bibliotek V12 för Java Script](/azure/storage/queues/storage-quickstart-queues-nodejs) |
| 10. x | [@azure/storage-queue](/javascript/api/@azure/storage-queue/?view=azure-node-legacy) | [Paket (NPM)](https://www.npmjs.com/package/@azure/storage-queue/v/10.3.0) | [Använd Azure Queue Service för att skapa och ta bort köer från Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues) |

## <a name="rest-apis"></a>REST API:er

I följande tabell visas referens och exempel dokumentation för REST-API: er i Azure Queue Storage.

| Referens dokumentation | Översikt |
| ----------------------- | -------- |
| [Kötjänst REST API](/rest/api/storageservices/queue-service-rest-api) | [Kötjänst begrepp](/rest/api/storageservices/queue-service-concepts) |

### <a name="other-rest-reference"></a>Annan REST-referens

- [Azure Storage import-export REST API](/rest/api/storageimportexport/) hjälper dig att hantera import/export-jobb för att överföra data till eller från Blob Storage.

## <a name="other-languages-and-platforms"></a>Andra språk och plattformar

Följande lista innehåller länkar till bibliotek för andra programmeringsspråk och plattformar.

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

Följande tabell innehåller länkar till den senaste versionen av referens innehållet.

| Version | Plattform |
| ------- | -------- |
|  3.x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-3.8.0) |
|  2x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-2.8.0) |

## <a name="azure-cli"></a>Azure CLI

- [Azure CLI](/cli/azure/storage)
