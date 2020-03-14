---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Läs mer om begränsningar och kända problem med Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fd76be8d17dc1c632e555a56d038d4f5c1e1486
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268514"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

I den här artikeln beskrivs begränsningar och kända problem med Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funktioner för blob-lagring som stöds

Nu fungerar ett ökande antal Blob Storage-funktioner med konton som har ett hierarkiskt namn område. En fullständig lista finns [i Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Azure Service-integration som stöds

Data Lake Storage Gen2 stöder flera Azure-tjänster som du kan använda för att mata in data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Se [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plattformar för öppen källkod som stöds

Data Lake Storage Gen2 har stöd för flera plattformar för öppen källkod. En fullständig lista finns i [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Se [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API: er för BLOB storage

BLOB-API: er och Data Lake Storage Gen2-API: er kan köras på samma data.

I det här avsnittet beskrivs problem och begränsningar med att använda BLOB-API: er och Data Lake Storage Gen2 API: er för att använda samma data.

* Du kan inte använda både BLOB-API: er och Data Lake Storage-API: er för att skriva till samma instans av en fil. Om du skriver till en fil med hjälp av Data Lake Storage Gen2 API: er visas inte filens block för anrop till BLOB-API: t [Get block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Du kan skriva över en fil med hjälp av antingen Data Lake Storage Gen2-API: er eller BLOB-API: er. Detta påverkar inte fil egenskaperna.

* När du använder [list-bloben](https://docs.microsoft.com/rest/api/storageservices/list-blobs) utan att ange en avgränsare, kommer resultatet att inkludera både kataloger och blobbar. Om du väljer att använda en avgränsare använder du bara ett snedstreck (`/`). Detta är den enda avgränsare som stöds.

* Om du använder [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API för att ta bort en katalog tas katalogen bara bort om den är tom. Det innebär att du inte kan använda BLOB API-borttagningarna rekursivt.

Dessa BLOB REST-API: er stöds inte:

* [Lägg till BLOB (sida)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Placerings sida](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Hämta sid intervall](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB för stegvis kopiering](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Lägg till sida från URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Lägg till BLOB (append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Lägg till block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Lägg till block från URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Ohanterade VM-diskar stöds inte i konton som har ett hierarkiskt namn område. Om du vill aktivera ett hierarkiskt namn område på ett lagrings konto placerar du ohanterade virtuella dator diskar i ett lagrings konto där funktionen för hierarkiskt namn område inte är aktive rad.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Stöd för fil system i SDK: er

- [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) och [python](data-lake-storage-directory-file-acl-python.md)och [Java Script](data-lake-storage-directory-file-acl-javascript.md) och support finns i offentlig för hands version. Andra SDK: er stöds inte för närvarande.
- Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Stöd för fil system i PowerShell och Azure CLI

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) -och [Azure CLI](data-lake-storage-directory-file-acl-cli.md) -stöd finns i offentlig för hands version.
- Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="lifecycle-management-policies"></a>Principer för livs cykel hantering

* Borttagning av BLOB-ögonblicksbilder stöds inte ännu.  

* Det finns för närvarande vissa buggar som påverkar livs cykel hanterings principer och Arkiv åtkomst nivå. 

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Storage Explorer 1.10. x kan inte användas för att Visa diagnostikloggar. Om du vill visa loggar använder du AzCopy eller SDK: er.

## <a name="blobfuse"></a>Blobfuse

Blobfuse stöds inte.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v 8.1, stöds inte.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Använd endast versioner `1.6.0` eller högre. För närvarande finns det ett lagrings fel som påverkar version `1.11.0` som kan leda till autentiseringsfel i vissa scenarier. En korrigering för lagrings felet är insamlad, men som en lösning rekommenderar vi att du använder version `1.10.x` som är tillgänglig som en [kostnads fri nedladdning](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` påverkas inte av lagrings felet.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer i Azure Portal

ACL: er stöds inte ännu.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Program från tredje part

Program från tredje part som använder REST-API: er för arbete fortsätter att fungera om du använder dem med Data Lake Storage Gen2 program som anropar BLOB-API: er kommer att fungera.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Åtkomst kontrol listor (ACL) och anonym Läs åtkomst

Om [Anonym Läs åtkomst](storage-manage-access-to-resources.md) har beviljats till en behållare, har ACL: er ingen påverkan på den behållaren eller filerna i den behållaren.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Driv rutin för Windows Azure Storage Blob (WASB)

För närvarande finns det flera problem som är kopplade till att använda WASB-drivrutinen tillsammans med konton som har ett hierarkiskt namn område. Vi rekommenderar att du använder [ABFS-drivrutinen (Azure Blob File System)](data-lake-storage-abfs-driver.md) i dina arbets belastningar. 





