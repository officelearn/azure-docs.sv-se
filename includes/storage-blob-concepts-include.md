---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
Azure Blob storage är Microsofts objektet lagringslösning för molnet. BLOB-lagring är optimerad för att lagra stora mängder Ostrukturerade data, till exempel text eller binära data.

BLOB storage är idealisk för:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Skrivningen till loggfiler.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en lokal eller Azure-värdtjänsten.

Objekt i Blob storage kan nås från var som helst i världen via HTTP eller HTTPS. Användare eller program åtkomst till blobbar via URL: er, den [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage), eller ett Azure Storage-klientbibliotek. Storage-klientbibliotek är tillgängliga för flera språk, inklusive [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/), och [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Blob Service-koncept

BLOB storage exponerar tre resurser: ditt lagringskonto, behållare för kontot och blobbar i en behållare. Följande diagram visar relationen mellan resurserna.

![Diagram över (objekt) för Blob storage-arkitektur](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Lagringskonto

All åtkomst till dataobjekt i Azure Storage sker via ett lagringskonto. Mer information finns i [om Azure storage-konton](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Behållare

En behållare ordnar en uppsättning blobbar, som en mapp i ett filsystem. Alla blobbar finns i en behållare. Ett lagringskonto kan innehålla ett obegränsat antal behållare och en behållare kan lagra ett obegränsat antal blobbar. Observera att behållarens namn får innehålla endast gemener.

### <a name="blob"></a>Blob
 
Azure Storage erbjuder tre typer av blobbar: blockblobbar, tilläggsblobbar och [sidblobbar](../articles/storage/blobs/storage-blob-pageblob-overview.md) (används för VHD-filer).

* Blockblobbar lagra text och binära data, upp till ungefär 4,7 TB. Blockblobbar består av block med data som kan hanteras individuellt.
* Lägg till BLOB består av block som blockblobbar, men är optimerade för tilläggsåtgärder. Lägg till BLOB lämpar sig för scenarier, till exempel loggningsdata från virtuella datorer.
* Sidan BLOB store direktåtkomst filer upp till 8 TB i storlek. Sidblobbar lagra VHD-filer som stöder virtuella datorer.

Alla blobbar finns i en behållare. En behållare liknar en mapp i ett filsystem. Ytterligare kan du organisera blobbar i virtuella kataloger och passerar dem precis som ett filsystem. 

För mycket stora datamängder där nätverksbegränsningar gör det orealistiskt att överföra eller hämta data till Blob Storage via kabel kan du skicka en uppsättning hårddiskar till Microsoft för att importera eller exportera data direkt från datacentret. Mer information finns i [använda tjänsten Microsoft Azure Import/Export för att överföra Data till Blob Storage](../articles/storage/common/storage-import-export-service.md).
  
Mer information om namngivning av behållare och blobbar finns i [Namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
