---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468104"
---
Här följer gränserna för storleken på de data som kopieras till lagrings kontot. Kontrol lera att de data som du överför följer dessa gränser. Den senaste informationen om dessa begränsningar finns i [skalbarhets-och prestanda mål för Blob Storage](../articles/storage/blobs/scalability-targets.md) och [Azure Files skalbarhet och prestanda mål](../articles/storage/files/storage-files-scale-targets.md).

| Storlek på data som kopieras till Azure Storage-kontot                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Block Blob och Page BLOB                                            | 2 PB för USA och Europa.<br>500 TB för alla andra regioner, som innehåller Storbritannien.  <br> Detta inkluderar data från alla källor, inklusive Data Box-enhet.|
| Azure Files                                                          | 5 TB per resurs.<br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen.       |
