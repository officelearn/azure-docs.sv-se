---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8a85a21d47733bb031ea8ca16f776ea8c2df6da2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291680"
---
Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data.

## <a name="about-blob-storage"></a>Om blobblagring

Blobblagring är utformat för att:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Skriva till loggfiler.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Användare eller klientprogram kan komma åt objekt i blobblagring via HTTP/HTTPS från var som helst i världen. Objekt i blobblagring nås via [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Klientbibliotek finns tillgängliga för flera språk, bland annat [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go](https://github.com/azure/azure-storage-blob-go/), [PHP](http://azure.github.io/azure-storage-php/) och [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Om Azure Data Lake Storage Gen2

Blobblagring stöder Azure Data Lake Storage Gen2, Microsofts lösning för stordataanalys för företag i molnet. Azure Data Lake Storage Gen2 erbjuder ett hierarkisk filsystem samt fördelarna med blobblagring, inklusive låg kostnad, nivåindelad lagring, hög tillgänglighet, stark konsekvens och haveriberedskap.

Läs mer om Data Lake Storage Gen2 [introduktion till Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).