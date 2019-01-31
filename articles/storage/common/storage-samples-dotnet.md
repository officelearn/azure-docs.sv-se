---
title: Azure Storage-exempel med hjälp av .NET | Microsoft Docs
description: Visa, ladda ned och kör exempelkoden och program för Azure Storage. Upptäck komma igång-exempel för blobbar, köer, tabeller och filer, med hjälp av storage-klientbibliotek för .NET.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: c4dcb3f42d97624e66258228ce27e130a05cac70
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456144"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-exempel med hjälp av .NET

## <a name="net-sample-index"></a>Index för .NET

Följande tabell innehåller en översikt över vår lagringsplats med exempel och scenarier som tas upp i varje exempel. Klicka på länkar för att visa motsvarande kod i GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Slutpunkt</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Exempelkod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Bifoga blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">CloudBlobContainer.GetAppendBlobReference metoden exempel</a></td> 
</tr> 
<tr> 
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exempel för BLOB-kryptering</a></td>
</tr> 
<tr> 
<td>Kopiera Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>Skapa behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Ta bort Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Ta bort behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>BLOB-Metadata/egenskaper/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>Behållare-ACL/metadataegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>Leasa Blobbehållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>Lista/Blobbehållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med Blobs</a></td>
</tr> 
<tr> 
<td>Sidblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med Blobs</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr>   
<tr> 
<td>Tjänstegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobs</a></td>
</tr>           
<tr> 
<td>Ta ögonblicksbild av Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Säkerhetskopiera Azure-Datordiskar med inkrementella ögonblicksbilder</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fil</b></td>
<td>Skapa filer/kataloger/resurser</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr>
<tr> 
<td>Ta bort filer/kataloger/resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Komma igång med Azure File Service i .NET</a></td> 
</tr> 
<tr> 
<td>Directory-egenskaper /-Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Ladda ned filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Filen egenskaper-Metadata-mått</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Egenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr> 
<tr> 
<td>Listan kataloger och filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr>
<tr> 
<td>Lista över resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr>
<tr> 
<td>Dela egenskaper/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET filen Storage-exempel</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>kön</b></td>
<td>Lägg till meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Kryptering av klientsidan</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET kö Client Side Encryption</a></td> 
</tr> 
<tr> 
<td>Skapa köer</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Ta bort meddelandekö /</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Granska meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Kön ACL/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Egenskaper för kötjänst</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure kötjänst i .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabell</b></td>
<td>Skapa tabell</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogrammet</a></td> 
</tr> 
<tr> 
<td>Ta bort entitet eller-tabell</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Infoga/Merge/Ersätt entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogrammet</a></td> 
</tr> 
<tr> 
<td>Kör frågor mot entiteter</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Frågetabeller</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>ACL/Tabellegenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogrammet</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Bibliotek för Azure-kodexempel

Om du vill visa hela exemplet biblioteket, går du till den [kodexempel för Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteket, som innehåller exempel för Azure Storage som du kan hämta och kör lokalt. Kod exempel biblioteket visar exempelkod i ZIP-format. Du kan också bläddra och klona GitHub-lagringsplatsen för varje exempel.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång-guider

Kolla in följande guider om du letar efter information om hur du installerar och kommer igång med Azure Storage-klientbibliotek.

* [Komma igång med Azure Blob Service i .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Komma igång med Azure kötjänst i .NET](../storage-dotnet-how-to-use-queues.md)
* [Komma igång med Azure Table Service i .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure File Service i .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

Mer information om-exempel för andra språk:

* Java: [Azure Storage-exempel med Java](storage-samples-java.md)
* Alla andra språk: [Azure Storage-exempel](../storage-samples.md)
