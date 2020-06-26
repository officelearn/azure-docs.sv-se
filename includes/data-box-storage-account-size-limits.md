---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378485"
---
Här följer gränserna för storleken på de data som kopieras till lagrings kontot. Kontrol lera att de data som du överför följer dessa gränser. Den senaste informationen om dessa begränsningar finns i [skalbarhets-och prestanda mål för Blob Storage](../articles/storage/blobs/scalability-targets.md) och [Azure Files skalbarhet och prestanda mål](../articles/storage/files/storage-files-scale-targets.md).

| Storlek på data som kopieras till Azure Storage-kontot                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Block Blob och Page BLOB                                            | Max gränsen är samma som den [lagrings gräns som definierats för Azure-prenumerationen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) och innehåller data från alla källor, inklusive data Box-enhet.|
| Azure Files                                                          | Maximal storlek på standard fil resurser är 5 TB <br> Maximal storlek för Premium fil resurser är 100TiB per resurs.<br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen.       |
