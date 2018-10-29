---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/17/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7889fbc9373cbdfdfab891bf8b1cd610523c7032
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088099"
---
Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, exempelvis text eller binära data.

Blob Storage är perfekt för:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Skriva till loggfiler.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Användare eller klientprogram kan få åtkomst till Blob Storage-objekt via HTTP eller HTTPS&mdash;från valfri plats i världen&mdash;via URL:er, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage), eller ett Azure Storage-klientbibliotek. Det finns lagringsklientbibliotek för flera språk, bland annat [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) och [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Blob Service-koncept

Blob Storage innehåller tre resurser: ditt lagringskonto, kontots containrar och containerns blobbar. Följande diagram visar relationen mellan de här resurserna.

![Diagram över Blob Storage-arkitektur (objekt)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Lagringskonto

All åtkomst till dataobjekt i Azure Storage sker via ett lagringskonto. Mer information finns i [kontoöversikten för Azure Storage](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Container

I en container ordnas en uppsättning blobbar, ungefär som en mapp i ett filsystem. Alla blobbar finns i en container. Ett lagringskonto kan omfatta ett obegränsat antal containrar, och varje container kan lagra ett obegränsat antal blobar. 

  > [!NOTE]
  > Containerns namn får endast innehålla gemener.

### <a name="blob"></a>Blob
 
Azure Storage erbjuder tre typer av blobar&mdash;blockblobar, tilläggsblobar och [sidblobar](../articles/storage/blobs/storage-blob-pageblob-overview.md) (används för VHD-filer).

* I blockblobbar lagras text och binära data, upp till ungefär 4,7 TB. Blockblobbar består av datablock som kan hanteras individuellt.
* Tilläggsblobbar består av block precis som blockblobbarna, men är optimerade för tilläggsåtgärder. Tilläggsblobbar passar utmärkt för åtgärder som att logga data från virtuella datorer.
* I sidblobbar lagras filer med slumpmässig åtkomst på upp till 8 TB. Sidblobbar lagrar VHD-filerna som stöder virtuella datorer.

Alla blobbar finns i en container. En container är ungefär som en mapp i ett filsystem. Du kan ordna blobar i virtuella kataloger och bläddra i dem precis som i ett filsystem. 

Det kan finnas tillfällen då stora datauppsättningar och nätverksbegränsningar gör att det inte går att ladda upp data till Blob Storage via överföring. Du kan använda [Azure Data Box Disk](../articles/databox/data-box-disk-overview.md) för att begära SSD-diskar från Microsoft. Sedan kan du kopiera dina data till diskarna och skicka tillbaka dem till Microsoft för uppladdning till Blob Storage.

Om du behöver exportera stora mängder data från ditt lagringskonto kan du läsa informationen i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](../articles/storage/common/storage-import-export-service.md).
  
Mer information om namngivning av containrar och blobar finns i informationen [Namnge och referera till containrar, blobar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
