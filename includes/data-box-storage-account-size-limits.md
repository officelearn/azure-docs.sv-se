---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027690"
---
Här följer gränserna för storleken på de data som kopieras till lagrings kontot. Kontrol lera att de data som du överför följer dessa gränser. Den senaste informationen om dessa begränsningar finns i [skalbarhets-och prestanda mål för Blob Storage](../articles/storage/blobs/scalability-targets.md) och [Azure Files skalbarhet och prestanda mål](../articles/storage/files/storage-files-scale-targets.md).

| Storlek på data som kopieras till Azure Storage-kontot                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Block Blob och Page BLOB                                            | Max gränsen är samma som den [lagrings gräns som definierats för Azure-prenumerationen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) och innehåller data från alla källor, inklusive data Box-enhet. |
| Azure Files                                                          | Data Box-enhet stöder stora fil resurser (100TiB) om det är aktiverat innan Data Box-enhets ordningen skapas. <br> Om den här inställningen inte är aktive rad innan du skapar en order är den maximala storleken som stöds för fil resursen 5 <br> Premium File-resurser stöds inte ännu.<br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen. <br> Mer information finns i [Aktivera och skapa stora fil resurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |