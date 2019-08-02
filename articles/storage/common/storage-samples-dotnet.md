---
title: Azure Storage exempel med hjälp av .NET | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Identifiera exempel på att komma igång med blobbar, köer, tabeller och filer med hjälp av klient biblioteken för .NET-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: cec6b0498adf55428fade7ae00db6550496a20bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721855"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage exempel med .NET

## <a name="net-sample-index"></a>.NET-exempel index

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Slutpunkt</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Exempelkod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Bifoga blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Komma igång med blobbar</a></td> 
</tr> 
<tr> 
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webb programmet Azure Blob Storage foto galleri</a></td>
</tr> 
<tr> 
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exempel på BLOB-kryptering</a></td>
</tr> 
<tr> 
<td>Kopiera Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>Skapa behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webb programmet Azure Blob Storage foto galleri</a></td>
</tr> 
<tr> 
<td>Ta bort Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webb programmet Azure Blob Storage foto galleri</a></td>
</tr> 
<tr> 
<td>Ta bort behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>BLOB-metadata/egenskaper/statistik</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>Behållar-ACL/metadata/egenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webb programmet Azure Blob Storage foto galleri</a></td>
</tr> 
<tr> 
<td>Hämta sid intervall</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>Lease BLOB/container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>Lista BLOB/container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med blobbar</a></td>
</tr> 
<tr> 
<td>Sidblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med blobbar</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr>   
<tr> 
<td>Tjänstegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med blobbar</a></td>
</tr>           
<tr> 
<td>Ögonblicks bilds-BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Säkerhetskopiera virtuella Azure-datorer med stegvisa ögonblicks bilder</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fil</b></td>
<td>Skapa resurser/kataloger/filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr>
<tr> 
<td>Ta bort resurser/kataloger/filer</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Komma igång med Azure File Service i .NET</a></td> 
</tr> 
<tr> 
<td>Katalog egenskaper/metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Hämta filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Fil egenskaper/metadata/mått</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Egenskaper för fil tjänst</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Lista över kataloger och filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr>
<tr> 
<td>List resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr>
<tr> 
<td>Resurs egenskaper/metadata/statistik</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage-exempel</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Köjobb</b></td>
<td>Lägg till meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Kryptering av klientsidan</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage kryptering på klient sidan i .NET-kö</a></td> 
</tr> 
<tr> 
<td>Skapa köer</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Ta bort meddelande/kö</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Granska meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Kös-ACL/metadata/statistik</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Egenskaper för Queue Service</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure Queue Service i .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabell</b></td>
<td>Skapa tabell</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage-exempel program</a></td> 
</tr> 
<tr> 
<td>Ta bort entitet/tabell</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Infoga/sammanfoga/Ersätt entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage-exempel program</a></td> 
</tr> 
<tr> 
<td>Fråga entiteter</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Frågetabeller</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Tabell-ACL/egenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage-exempel program</a></td> 
</tr> 
</tbody> 
</table>
<br/>

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

* Java: [Azure Storage-exempel med Java](storage-samples-java.md)
* Alla andra språk: [Azure Storage exempel](../storage-samples.md)
