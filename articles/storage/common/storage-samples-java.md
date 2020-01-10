---
title: Azure Storage exempel med Java | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Upptäck kom igång-exempel för blobbar, köer, tabeller och filer med hjälp av klient biblioteken för Java-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748042"
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage exempel med Java

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

> [!NOTE]
> De här exemplen använder Azure Storage Java V11-biblioteket. V12-kod finns i [exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) i GitHub-lagringsplatsen.

## <a name="blob-samples-v11"></a>BLOB-exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Lägg till BLOB | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blockblob | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Kryptering av klientsidan | [Komma igång med Azure client side Encryption i Java](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| Kopiera Blob | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Skapa behållare | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Ta bort Blob | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Ta bort behållare | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB-metadata/egenskaper/statistik | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Behållar-ACL/metadata/egenskaper | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Hämta sid intervall | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| Lease BLOB/container | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Lista BLOB/container | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Sid-BLOB | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [Exempel på SAS-tester](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| Tjänstegenskaper | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Ögonblicks bilds-BLOB | [Komma igång med Azure Blob service i Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>Fil exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Skapa resurser/kataloger/filer | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Ta bort resurser/kataloger/filer | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Katalog egenskaper/metadata | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Hämta filer | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Fil egenskaper/metadata/mått | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Egenskaper för fil tjänst | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Lista över kataloger och filer | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| List resurser | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Resurs egenskaper/metadata/statistik | [Komma igång med Azure File Service i Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>Queue-exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Lägg till meddelande | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| Kryptering av klientsidan | [Komma igång med Azure client side Encryption i Java](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| Skapa köer | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Ta bort meddelande/kö | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Granska meddelande | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Kös-ACL/metadata/statistik | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Egenskaper för Queue Service | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Uppdatera meddelande | [Komma igång med Azure Queue Service i Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>Tabell exempel (V11)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Skapa tabell | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Ta bort entitet/tabell | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Infoga/sammanfoga/Ersätt entitet | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Fråga entiteter | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Frågetabeller | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Tabell-ACL/egenskaper | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| Uppdatera entitet | [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Bibliotek för Azure kod exempel

Om du vill visa det kompletta exempel biblioteket går du till biblioteket [Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=storage) , som innehåller exempel för Azure Storage som du kan hämta och köra lokalt. Kod exempel biblioteket innehåller exempel kod i zip-format. Du kan också bläddra och klona GitHub-lagringsplatsen för varje exempel.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång guider

Kolla in följande guider om du behöver instruktioner om hur du installerar och kommer igång med Azure Storage klient bibliotek.

* [Komma igång med Azure Blob service i Java](../blobs/storage-quickstart-blobs-java.md)
* [Komma igång med Azure Queue Service i Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Komma igång med Azure Table Service i Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Komma igång med Azure File Service i Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Nästa steg

Information om exempel för andra språk:

* .NET: [Azure Storage exempel med .net](storage-samples-dotnet.md)
* Java Script/Node. js: [Azure Storage exempel med hjälp av Java Script](storage-samples-javascript.md)
* Python: [Azure Storage exempel med python](storage-samples-python.md)
* Alla andra språk: [Azure Storage exempel](storage-samples.md)
