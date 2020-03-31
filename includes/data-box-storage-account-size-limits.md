---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468104"
---
Här är gränserna för storleken på de data som kopieras till lagringskontot. Se till att de data du laddar upp överensstämmer med dessa gränser. Den senaste informationen om dessa begränsningar finns i [Skalbarhets- och prestandamål för Blob-lagring](../articles/storage/blobs/scalability-targets.md) och [Azure Files skalbarhet och prestandamål](../articles/storage/files/storage-files-scale-targets.md).

| Storleken på data som kopieras till Azure-lagringskonto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Blockblob och sidblob                                            | 2 PB för USA och Europa.<br>500 TB för alla andra regioner, vilket inkluderar Storbritannien.  <br> Detta inkluderar data från alla källor, inklusive Data Box.|
| Azure Files                                                          | 5 TB per aktie.<br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen.       |
