---
title: Introduktion till Blob Storage – objektlagring i Azure
description: Blob Storage kan lagra stora mängder ostrukturerade objektdata, som text eller binära data. Azure Blob Storage är mycket skalbar och tillgänglig. Klienter kan komma åt dataobjekt i Blob Storage från PowerShell eller Azure CLI programmässigt via Azure Storage-klientbibliotek eller via REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 05/24/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8a68ca87307e1265c200f2e05464ff87983c1e6
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258410"
---
# <a name="introduction-to-azure-blob-storage"></a>Introduktion till Azure Blob storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blobblagringsresurser

I blobblagringen finns tre typer av resurser:

- **Lagringskontot**. 
- En **container** på lagringskontot
- En **blobb** i en container 

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Lagringskonton

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-blobslutpunkten utgör basadressen för objekten i ditt lagringskonto.

Om ditt lagringskonto till exempel heter *mystorageaccount*, kommer standardslutpunkterna för blobblagringen att vara:

```
http://mystorageaccount.blob.core.windows.net 
```

Om du vill skapa ett lagringskonto kan du läsa mer i [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md). Mer information om lagringskonton finns i [Översikt över Azure-lagringskonton](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containrar

I en container finns en uppsättning med blobbar, ungefär som i en katalog i ett filsystem. Ett lagringskonto kan omfatta ett obegränsat antal containrar, och varje container kan lagra ett obegränsat antal blobar. 

  > [!NOTE]
  > Containerns namn får endast innehålla gemener. Mer information om namngivning av containrar finns i [Ge namn och referera till containrar, blobbar och metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobar
 
Azure Storage har stöd för tre typer av blobbar:

* I **blockblobbar** lagras text och binära data, upp till ungefär 4,7 TB. Blockblobbar består av datablock som kan hanteras individuellt.
* **Bilageblobbar** består av block, precis som blockblobbarna, men de är optimerade för bilageåtgärder. Tilläggsblobbar passar utmärkt för åtgärder som att logga data från virtuella datorer.
* I **sidblobbar** lagras filer med slumpmässig åtkomst med en storlek på upp till 8 TB. Sidblobbar lagrar virtuella hårddiskfiler (VHD) som fungerar som diskar för virtuella Azure-datorer. Mer information om sidblobar finns [översikten av Azure-sidblobar](storage-blob-pageblob-overview.md)

Mer information om de olika typerna av blobbar finns i [Förstå blockblobbar, sidblobbar och bilageblobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Flytta data till blobblagring

Det finns flera olika lösningar för att migrera befintliga data till blobblagring:

- **AzCopy** är ett lättanvänt kommandoradsverktyg för Windows och Linux som kopierar data till och från blobblagringen, mellan containrar eller mellan lagringskonton. Mer information om AzCopy finns i [Överföra data med AzCopy v10 (förhandsversion)](../common/storage-use-azcopy-v10.md). 
- **Azure Storages dataflyttbibliotek** är ett .NET-bibliotek som används till att flytta data mellan Azure Storage-tjänster. AzCopy-verktyget skapas med dataflyttbiblioteket. Mer information finns i [referensdokumentationen](/dotnet/api/microsoft.azure.storage.datamovement) för dataflyttbiblioteket. 
- **Azure Data Factory** stöder kopiering av data till och från blobblagring med hjälp av kontonyckel, signatur för delad åtkomst, tjänstens huvudnamn eller hanterade identiteter för Azure-resursernas autentiseringar. Mer information finns i [Kopiera data till och från Azure-blobblagring med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** är en virtuell filsystemsdrivrutin för Azure-blobblagring. Du kan använda blobfuse för att få åtkomst till dina befintliga blockblobbdata på ditt lagringskonto via filsystemet Linux. Mer information finns i [Montera blobblagring som ett filsystem med blobfuse](storage-how-to-mount-container-linux.md).
- **Azure Data Box** tjänsten är tillgänglig för att överföra lokala data till Blob storage när stora datauppsättningar eller nätverksbegränsningar gör överför data i rörelse orealistiskt. Beroende på datastorleken på din, kan du begära [Azure Data Box-Disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md), eller [Azure Data Box tung](../../databox/data-box-heavy-overview.md) enheter från Microsoft. Du kan kopiera data till dessa enheter och skickar tillbaka dem till Microsoft som ska laddas upp till Blob-lagring.
- Den **tjänsten Azure Import/Export** gör det möjligt att importera eller exportera stora mängder data till och från ditt lagringskonto med hjälp av hårddiskar som du anger. Mer information finns i [Använda Microsoft Azure Import/Export till att överföra data till blobblagring](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Skalbarhets- och prestandamål i Azure Storage](../common/storage-scalability-targets.md)
