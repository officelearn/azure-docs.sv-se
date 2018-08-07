---
title: Azure Storage-exempel med hjälp av Java | Microsoft Docs
description: Visa, ladda ned och kör exempelkoden och program för Azure Storage. Upptäck komma igång-exempel för blobbar, köer, tabeller och filer, med hjälp av storage-klientbibliotek för Java.
services: storage
author: seguler
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: bdc25a7aeff88d058eaf3fddf6cec023edff3b9e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531797"
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage-exempel med hjälp av Java

## <a name="java-sample-index"></a>Index för Java

Följande tabell innehåller en översikt över vår lagringsplats med exempel och scenarier som tas upp i varje exempel. Klicka på länkar för att visa motsvarande kod i GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Slutpunkt</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Exempelkod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Bifoga Blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td> 
</tr> 
<tr> 
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Komma igång med Azure Client Side Encryption i Java</a></td>
</tr> 
<tr> 
<td>Kopiera Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Skapa behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Ta bort Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Ta bort behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>BLOB-Metadata/egenskaper/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Behållare-ACL/metadataegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Sidblob testar exemplet</a></td>
</tr> 
<tr> 
<td>Leasa Blobbehållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Lista/Blobbehållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td>Sidblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">Exempel för SAS-tester</a></td>
</tr>   
<tr> 
<td>Tjänstegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Komma igång med Azure Blob Service i Java</a></td>
</tr>           
<tr> 
<td>Ta ögonblicksbild av Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Komma igång med Azure Blob Service i Java</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fil</b></td>
<td>Skapa filer/kataloger/resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td> 
</tr>
<tr> 
<td>Ta bort filer/kataloger/resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td> 
</tr> 
<tr> 
<td>Directory-egenskaper /-Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td> 
</tr> 
<tr> 
<td>Ladda ned filer</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td> 
</tr> 
<tr> 
<td>Filen egenskaper-Metadata-mått</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td> 
</tr> 
<tr> 
<td>Egenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td> 
</tr> 
<tr> 
<td>Listan kataloger och filer</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td> 
</tr>
<tr> 
<td>Lista över resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Komma igång med Azure File Service i Java</a></td> 
</tr>
<tr> 
<td>Dela egenskaper/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Komma igång med Azure File Service i Java</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>kön</b></td>
<td>Lägg till meddelande</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Biblioteket Lagringsexempel Java-klienten</a></td> 
</tr> 
<tr> 
<td>Kryptering av klientsidan</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Biblioteket Lagringsexempel Java-klienten</a></td> 
</tr> 
<tr> 
<td>Skapa köer</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td>Ta bort meddelandekö /</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td>Granska meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td>Kön ACL/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td>Egenskaper för kötjänst</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td>Uppdatera meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Komma igång med Azure Queue Service i Java</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabell</b></td>
<td>Skapa tabell</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Komma igång med Azure Table Service i Java</a></td> 
</tr> 
<tr> 
<td>Ta bort entitet eller-tabell</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Komma igång med Azure Table Service i Java</a></td> 
</tr> 
<tr> 
<td>Infoga/Merge/Ersätt entitet</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Biblioteket Lagringsexempel Java-klienten</a></td> 
</tr> 
<tr> 
<td>Kör frågor mot entiteter</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Komma igång med Azure Table Service i Java</a></td> 
</tr> 
<tr> 
<td>Frågetabeller</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Komma igång med Azure Table Service i Java</a></td> 
</tr> 
<tr> 
<td>ACL/Tabellegenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Komma igång med Azure Table Service i Java</a></td> 
</tr> 
<tr> 
<td>Uppdatera entitet</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Biblioteket Lagringsexempel Java-klienten</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Bibliotek för Azure-kodexempel

Om du vill visa hela exemplet biblioteket, går du till den [kodexempel för Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteket, som innehåller exempel för Azure Storage som du kan hämta och kör lokalt. Kod exempel biblioteket visar exempelkod i ZIP-format. Du kan också bläddra och klona GitHub-lagringsplatsen för varje exempel.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång-guider

Kolla in följande guider om du letar efter information om hur du installerar och kommer igång med Azure Storage-klientbibliotek.

* [Komma igång med Azure Blob Service i Java](../blobs/storage-quickstart-blobs-java.md)
* [Komma igång med Azure Queue Service i Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Komma igång med Azure Table Service i Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Komma igång med Azure File Service i Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Nästa steg

Mer information om-exempel för andra språk:

* .NET: [azure Storage-exempel med hjälp av .NET](storage-samples-dotnet.md)
* Alla andra språk: [Azure Storage-exempel](storage-samples.md)
