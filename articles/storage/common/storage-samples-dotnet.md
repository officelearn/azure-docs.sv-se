---
title: Azure Storage exempel med hjälp av .NET | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Identifiera exempel på att komma igång med blobbar, köer, tabeller och filer med hjälp av klient biblioteken för .NET-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 50c5067c3db2f07da225b72d9ba0a8f0bdc44368
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748134"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage exempel med .NET

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

> [!NOTE]
> De här exemplen använder Azure Storage .NET V11-biblioteket. V12-kod finns i [exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) i GitHub-lagringsplatsen.

## <a name="blob-samples-v11"></a>BLOB-exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Lägg till BLOB | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blockblob | [Webb programmet Azure Blob Storage foto galleri](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Kryptering av klientsidan | [Exempel på BLOB-kryptering](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Kopiera Blob | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Skapa behållare | [Webb programmet Azure Blob Storage foto galleri](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Ta bort Blob | [Webb programmet Azure Blob Storage foto galleri](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Ta bort behållare | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB-metadata/egenskaper/statistik | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Behållar-ACL/metadata/egenskaper | [Webb programmet Azure Blob Storage foto galleri](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Hämta sid intervall | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Lease BLOB/container | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Lista BLOB/container | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Sid-BLOB | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Tjänstegenskaper | [Komma igång med blobbar](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Ögonblicks bilds-BLOB | [Säkerhetskopiera virtuella Azure-datorer med stegvisa ögonblicks bilder](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples-v11"></a>Fil exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Skapa resurser/kataloger/filer | [Azure Storage .NET File Storage-exempel](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Ta bort resurser/kataloger/filer | [Komma igång med Azure File Service i .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Katalog egenskaper/metadata | [Azure Storage .NET File Storage-exempel](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Hämta filer | [Azure Storage .NET File Storage-exempel](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Fil egenskaper/metadata/mått | [Azure Storage .NET File Storage-exempel](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Egenskaper för fil tjänst | [Azure Storage .NET File Storage-exempel](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Lista över kataloger och filer | [Azure Storage .NET File Storage-exempel](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| List resurser | [Azure Storage .NET File Storage-exempel](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Resurs egenskaper/metadata/statistik | [Azure Storage .NET File Storage-exempel](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples-v11"></a>Queue-exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Lägg till meddelande | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Kryptering av klientsidan | [Azure Storage kryptering på klient sidan i .NET-kö](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Skapa köer | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Ta bort meddelande/kö | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Granska meddelande | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Kös-ACL/metadata/statistik | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Egenskaper för Queue Service | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Uppdatera meddelande | [Komma igång med Azure Queue Service i .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples-v11"></a>Tabell exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Skapa tabell | [Hantera samtidighet med hjälp av Azure Storage-exempel program](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Ta bort entitet/tabell | [Komma igång med Azure Table Storage i .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Infoga/sammanfoga/Ersätt entitet | [Hantera samtidighet med hjälp av Azure Storage-exempel program](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Fråga entiteter | [Komma igång med Azure Table Storage i .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Frågetabeller | [Komma igång med Azure Table Storage i .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tabell-ACL/egenskaper | [Komma igång med Azure Table Storage i .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Uppdatera entitet | [Hantera samtidighet med hjälp av Azure Storage-exempel program](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Bibliotek för Azure kod exempel

Om du vill visa det kompletta exempel biblioteket går du till biblioteket [Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=storage) , som innehåller exempel för Azure Storage som du kan hämta och köra lokalt. Kod exempel biblioteket innehåller exempel kod i zip-format. Du kan också bläddra och klona GitHub-lagringsplatsen för varje exempel.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång guider

Kolla in följande guider om du behöver instruktioner om hur du installerar och kommer igång med Azure Storage klient bibliotek.

* [Komma igång med Azure Blob service i .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Komma igång med Azure Queue Service i .NET](../storage-dotnet-how-to-use-queues.md)
* [Komma igång med Azure Table service i .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure File Service i .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

Information om exempel för andra språk:

* Java: [Azure Storage exempel med Java](storage-samples-java.md)
* Java Script/Node. js: [Azure Storage exempel med hjälp av Java Script](storage-samples-javascript.md)
* Python: [Azure Storage exempel med python](storage-samples-python.md)
* Alla andra språk: [Azure Storage exempel](../storage-samples.md)
