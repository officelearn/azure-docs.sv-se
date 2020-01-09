---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om begränsningar och kända problem med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 099dc723db44ba71fc4672c382d24ac93ffe742f
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689133"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

Den här artikeln innehåller funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problem och begränsningar med att använda BLOB API: er

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

## <a name="filesystem-support-in-sdks"></a>Filesystem-stöd i SDK: er

- [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) och [python](data-lake-storage-directory-file-acl-python.md) -support finns i offentlig för hands version. Andra SDK: er stöds inte för närvarande.
- Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>Filesystem-stöd i PowerShell och Azure CLI

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) -och [Azure CLI](data-lake-storage-directory-file-acl-cli.md) -stöd finns i offentlig för hands version.
- Hämta och ange ACL-åtgärder är för närvarande inte rekursiva.

## <a name="support-for-other-blob-storage-features"></a>Stöd för andra Blob Storage funktioner

I följande tabell visas alla andra funktioner och verktyg som inte stöds eller som bara delvis stöds med lagrings konton som har ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

| Funktion/verktyg    | Mer information    |
|--------|-----------|
| **Redundansväxling av konto** |Stöds inte ännu|
| **AzCopy** | Versions-/regionsspecifika stöd <br><br>Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v 8.1, stöds inte.|
| **Hanterings principer för Azure Blob Storage Lifecycle** | Policys för livs cykel hantering stöds (för hands version).  Alla åtkomst nivåer stöds. Arkiv åtkomst nivån är för närvarande en för hands version. Borttagning av BLOB-ögonblicksbilder stöds inte ännu. <br><br> Det finns för närvarande vissa buggar som påverkar livs cykel hanterings principer och Arkiv åtkomst nivå.  Registrera dig för för hands versionen av policys för livs cykel hantering och Arkiv åtkomst nivå [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).   |
| **Azure Content Delivery Network (CDN)** | Stöds inte ännu|
| **Azure Search** |Stöds (för hands version)|
| **Azure Storage Explorer** | Versions-/regionsspecifika stöd. <br><br>Använd endast versioner `1.6.0` eller högre. <br> Det finns för närvarande en lagrings enhet som påverkar version `1.11.0` som kan leda till autentiseringsfel i vissa scenarier. En korrigering för lagrings felet är insamlad, men som en lösning rekommenderar vi att du använder version `1.10.x` som är tillgänglig som en [kostnads fri nedladdning](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` påverkas inte av lagrings felet.|
| **Antal ACL: er för BLOB-behållare** |Stöds inte ännu|
| **Blobfuse** |Stöds inte ännu|
| **Anpassade domäner** |Stöds inte ännu|
| **Storage Explorer i Azure Portal** | Begränsat stöd. ACL: er stöds inte ännu. |
| **Diagnostikloggning** |Diagnostikloggar stöds (för hands version).<br><br>Aktivering av loggar i Azure Portal stöds inte för närvarande. Här är ett exempel på hur du aktiverar loggarna med hjälp av PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Se till att ange `Blob` som värde för parametern `-ServiceType` som visas i det här exemplet. <br><br>Azure Storage Explorer kan för närvarande inte användas för att Visa diagnostikloggar. Om du vill visa loggar använder du AzCopy eller SDK: er.
| **Oåterkalleligt lagrings utrymme** |Stöds inte ännu <br><br>Oföränderlig lagring ger möjlighet att lagra data i en [mask (Skriv en gång, läsa många)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Objekt nivå nivåer** |Låg frekvent nivå och Arkiv lag ring stöds. Arkiv nivån är i för hands version. Alla andra åtkomst nivåer stöds inte ännu. <br><br> Det finns för närvarande vissa buggar som påverkar Arkiv åtkomst nivån.  Registrera dig för för hands versionen av Arkiv åtkomst nivån [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).|
| **Statiska webbplatser** |Stöds inte ännu <br><br>Mer specifikt möjlighet att betjäna filer till [statiska webbplatser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Program från tredje part** | Begränsat stöd <br><br>Program från tredje part som använder REST-API: er för arbete fortsätter att fungera om du använder dem med Data Lake Storage Gen2. <br>Program som anropar BLOB-API: er kommer förmodligen att fungera.|
|**Mjuk borttagning** |Stöds inte ännu|
| **Versions funktioner** |Stöds inte ännu <br><br>Detta inkluderar [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)och andra versions funktioner, till exempel [ögonblicks bilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


