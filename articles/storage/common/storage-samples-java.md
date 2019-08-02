---
title: Azure Storage exempel med Java | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Upptäck kom igång-exempel för blobbar, köer, tabeller och filer med hjälp av klient biblioteken för Java-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 4348400e66e4b975d1eeb899cab8bb5024260149
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721840"
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage exempel med Java

## <a name="java-sample-index"></a>Java-exempel index

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Slutpunkt</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Exempelkod</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Blob</b></td>
<td>Bifoga blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Komma igång med Azure client side Encryption i Java</a></td>
</tr>
<tr>
<td>Kopiera Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Skapa behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Ta bort Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Ta bort behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>BLOB-metadata/egenskaper/statistik</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Behållar-ACL/metadata/egenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Hämta sid intervall</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Lease BLOB/container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Lista BLOB/container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Sidblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Exempel på SAS-tester</a></td>
</tr>   
<tr>
<td>Tjänstegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td>Ögonblicks bilds-BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob service i Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>Fil</b></td>
<td>Skapa resurser/kataloger/filer</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Ta bort resurser/kataloger/filer</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Katalog egenskaper/metadata</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Hämta filer</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Fil egenskaper/metadata/mått</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Egenskaper för fil tjänst</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Lista över kataloger och filer</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>List resurser</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td>Resurs egenskaper/metadata/statistik</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>Köjobb</b></td>
<td>Lägg till meddelande</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Komma igång med Azure client side Encryption i Java</a></td>
</tr>
<tr>
<td>Skapa köer</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Ta bort meddelande/kö</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Granska meddelande</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Kös-ACL/metadata/statistik</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Egenskaper för Queue Service</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td>Uppdatera meddelande</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabell</b></td>
<td>Skapa tabell</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Ta bort entitet/tabell</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Infoga/sammanfoga/Ersätt entitet</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Fråga entiteter</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Frågetabeller</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Tabell-ACL/egenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
<tr>
<td>Uppdatera entitet</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Komma igång med Azure Table Service i Java</a></td>
</tr>
</tbody>
</table>
<br/>

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

* .NET: [Azure Storage-exempel med .NET](storage-samples-dotnet.md)
* Alla andra språk: [Azure Storage exempel](storage-samples.md)
