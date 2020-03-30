---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061515"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

I den här artikeln beskrivs begränsningar och kända problem med Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Blob-lagringsfunktioner som stöds

Ett ökande antal Blob-lagringsfunktioner fungerar nu med konton som har ett hierarkiskt namnområde. En fullständig lista finns [i Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integreringar av Azure-tjänster som stöds

Gen2 för DataSjölagring stöder flera Azure-tjänster som du kan använda för att använda data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Se [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plattformar med öppen källkod som stöds

Flera plattformar med öppen källkod stöder Data Lake Storage Gen2. En fullständig lista finns i [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Se [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API:er för bloblagring

Blob API:er och Data Lake Storage Gen2 API:er kan fungera på samma data.

I det här avsnittet beskrivs problem och begränsningar med hur du använder blob API:er och API:er för datasjölagring gen2 för att fungera på samma data.

* Du kan inte använda både Blob API:er och DataSjölagrings-API:er för att skriva till samma instans av en fil. Om du skriver till en fil med hjälp av Data Lake Storage Gen2 API:er visas inte filens block för anrop till blob-API:et [Hämta blockeringslista.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Du kan skriva över en fil med hjälp av api:er för Data Lake Storage Gen2 eller Blob API:er. Detta påverkar inte filegenskaperna.

* När du använder åtgärden [Lista Blobbar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) utan att ange en avgränsare, kommer resultaten att innehålla både kataloger och blobbar. Om du väljer att använda en avgränsare använder`/`du bara ett snedstreck ( ). Detta är den enda avgränsare som stöds.

* Om du använder [Ta bort Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API för att ta bort en katalog, tas den katalogen bara bort om den är tom. Det innebär att du inte kan använda Blob API-borttagningskataloger rekursivt.

Dessa Blob REST API:er stöds inte:

* [Sätta Blob (sida)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Placera sida](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Hämta sidintervall](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Inkrementell kopia Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Placera sida från URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Sätt Blob (Lägg till)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Lägg till block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Lägg till block från URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Ohanterat VM-diskar stöds inte i konton som har ett hierarkiskt namnområde. Om du vill aktivera ett hierarkiskt namnområde på ett lagringskonto placerar du ohanterade VM-diskar i ett lagringskonto som inte har den hierarkiska namnområdesfunktionen aktiverad.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Stöd för filsystem i SDK:er

Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Stöd för filsystem i PowerShell och Azure CLI

- [Support för PowerShell](data-lake-storage-directory-file-acl-powershell.md) och [Azure CLI](data-lake-storage-directory-file-acl-cli.md) är i offentlig förhandsversion.
- Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="lifecycle-management-policies"></a>Principer för livscykelhantering

* Borttagningen av blob-ögonblicksbilder stöds ännu inte.  

## <a name="archive-tier"></a>Arkiv nivå

Det finns för närvarande ett fel som påverkar arkivåtkomstnivån.


## <a name="blobfuse"></a>Blus

Blus stöds inte.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Tidigare versioner av AzCopy, till exempel AzCopy v8.1, stöds inte.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Använd endast `1.6.0` versioner eller högre.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Lagringsutforskaren i Azure-portalen

ACL stöds ännu inte.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Ansökningar från tredje part

Program från tredje part som använder REST-API:er för att fungera fortsätter att fungera om du använder dem med Data Lake Storage Gen2-program som anropar Blob-API:er fungerar troligen.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Åtkomstkontrollistor (ACL) och anonym läsåtkomst

Om [anonym läsåtkomst](storage-manage-access-to-resources.md) har beviljats till en behållare har ACL:er ingen effekt på behållaren eller filerna i behållaren.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure Storage Blob (WASB) drivrutin

För närvarande finns det flera problem med att använda WASB-drivrutinen tillsammans med konton som har ett hierarkiskt namnområde. Vi rekommenderar att du använder [ABFS-drivrutinen (Azure Blob File System)](data-lake-storage-abfs-driver.md) i dina arbetsbelastningar. 





