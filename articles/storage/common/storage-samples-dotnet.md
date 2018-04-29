---
title: Azure Storage-exempel med hjälp av .NET | Microsoft Docs
description: Visa, hämta och köra exempelkod och program för Azure Storage. Identifiera komma igång prover för blobbar, köer, tabeller och filer, använda lagringsklientbiblioteken för .NET.
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 1e6973f0decc448657d869afb8823dd03c62d272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-exempel med hjälp av .NET

## <a name="net-sample-index"></a>Index för .NET-exempel

Följande tabell innehåller en översikt över scenarier som tas upp i varje prov och våra exempel-databasen. Klicka på länkar för att visa motsvarande exempelkoden i GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Slutpunkt</th><th style="font-size:110%">Scenario</th><th style="font-size:110%">Exempelkod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>BLOB</b></td>
<td>Lägg till Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Metoden CloudBlobContainer.GetAppendBlobReference exempel</a></td> 
</tr> 
<tr> 
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery webbprogram</a></td>
</tr> 
<tr> 
<td>Kryptering av klientsidan</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exempel för BLOB-kryptering</a></td>
</tr> 
<tr> 
<td>Kopiera Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>Skapa en behållare</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery webbprogram</a></td>
</tr> 
<tr> 
<td>Ta bort blobben</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery webbprogram</a></td>
</tr> 
<tr> 
<td>Ta bort behållaren</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>BLOB Metadata/egenskaper/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>Behållaren-ACL/metadataegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery webbprogram</a></td>
</tr> 
<tr> 
<td>Get-sidintervall</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>Lånet Blobbehållaren</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>Lista Blobbehållaren</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med Blobbar</a></td>
</tr> 
<tr> 
<td>Sidblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Komma igång med Blobbar</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr>   
<tr> 
<td>Tjänstegenskaper</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Komma igång med Blobbar</a></td>
</tr>           
<tr> 
<td>Snapshot-Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Säkerhetskopiering Azure virtuella diskar med inkrementell ögonblicksbilder</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fil</b></td>
<td>Skapa resurser-kataloger-filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET lagring exempel</a></td> 
</tr>
<tr> 
<td>Ta bort resurser-kataloger-filer</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Komma igång med Azure File Service i .NET</a></td> 
</tr> 
<tr> 
<td>Directory egenskaper/Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET lagring exempel</a></td> 
</tr> 
<tr> 
<td>Hämta filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET lagring exempel</a></td> 
</tr> 
<tr> 
<td>Filen mått-egenskaper/Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET lagring exempel</a></td> 
</tr> 
<tr> 
<td>Egenskaper för filtjänsten</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET lagring exempel</a></td> 
</tr> 
<tr> 
<td>Lista över kataloger och filer</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET lagring exempel</a></td> 
</tr>
<tr> 
<td>Lista över resurser</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET lagring exempel</a></td> 
</tr>
<tr> 
<td>Dela Stats-egenskaper/Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET lagring exempel</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Kön</b></td>
<td>Lägga till meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Kryptering av klientsidan</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET kön klientsidan kryptering</a></td> 
</tr> 
<tr> 
<td>Skapa köer</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Ta bort meddelandekö /</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Granska meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Kön ACL/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Egenskaper för kön</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera meddelande</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Komma igång med Azure-kötjänsten i .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabell</b></td>
<td>Skapa tabell</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogram</a></td> 
</tr> 
<tr> 
<td>Ta bort en entitet/tabell</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Infoga/Merge/ersätta entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogram</a></td> 
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
<td>ACL/Tabellegenskaper</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Komma igång med Azure Table Storage i .NET</a></td> 
</tr> 
<tr> 
<td>Uppdatera entitet</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Hantera samtidighet med hjälp av Azure Storage - exempelprogram</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Azure-kodexempel-bibliotek

Om du vill visa hela exemplet biblioteket, gå till den [Azure kodexempel](https://azure.microsoft.com/resources/samples/?service=storage) biblioteket, som innehåller exempel för Azure Storage som du kan hämta och kör lokalt. Koden exempel biblioteket innehåller exempelkod i ZIP-format. Du kan också bläddra och klona lagringsplatsen för varje prov GitHub.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång guider

Se följande guider om du letar efter information om hur du installerar och kom igång med Azure Storage-klientbibliotek.

* [Komma igång med Azure Blob-tjänsten i .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Komma igång med Azure-kötjänsten i .NET](../storage-dotnet-how-to-use-queues.md)
* [Komma igång med Azure-tabellen Service i .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure File Service i .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

Information om prover för andra språk:

* Java: [Azure Storage-exempel som använder Java](storage-samples-java.md)
* Alla andra språk: [Azure Storage-exempel](../storage-samples.md)
