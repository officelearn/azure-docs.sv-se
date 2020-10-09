---
title: inkludera fil
description: inkludera fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cb4cac18f887a72c05fe520d6103dc00399cc05e
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829290"
---
Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss data modell eller definition, till exempel text eller binära data.

## <a name="about-blob-storage"></a>Om blobblagring

Blobblagring är utformat för att:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Skriva till loggfiler.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-värdbaserad.

Användare eller klientprogram kan komma åt objekt i blobblagring via HTTP/HTTPS från var som helst i världen. Objekt i blobblagring nås via [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Klient bibliotek är tillgängliga för olika språk, bland annat:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Kör](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Om Azure Data Lake Storage Gen2

Blobblagring stöder Azure Data Lake Storage Gen2, Microsofts lösning för stordataanalys för företag i molnet. Azure Data Lake Storage Gen2 erbjuder ett hierarkiskt fil system samt fördelarna med Blob Storage, inklusive:

* Låg kostnad, skiktad lagring
* Hög tillgänglighet
* Stark konsekvens
* Funktioner för haveri beredskap

Mer information om Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
